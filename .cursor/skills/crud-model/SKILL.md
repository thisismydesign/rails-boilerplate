---
name: crud-model
description: Create models following vanilla Rails DDD conventions. Use when adding a new model. Covers rich domain APIs, concerns for organization, and state-as-records patterns.
---

# Model Patterns

## Rich domain APIs

Expose intention-revealing methods on the model. Controllers call these directly -- never through service objects.

```ruby
# BAD -- service object mediates between controller and model
class CardClosingService
  def call(card, user)
    card.update!(closed: true, closed_by: user)
    NotificationJob.perform_later(card)
  end
end

# GOOD -- model exposes a natural API, hides complexity
class Card < ApplicationRecord
  def close(closer: Current.user)
    closures.create!(user: closer)
  end
end

# Controller just calls the model
class Closure
  def create
    @card.close
  end
end
```

The model API should read like natural language. Prefer `recording.incinerate` over `Recording::IncinerationService.execute(recording)`. The first form hides complexity and doesn't shift the burden of composition to the caller.

## Concerns for organization

When a model gains many responsibilities, use concerns to organize related behavior. This avoids fat models without introducing services.

Name concerns as adjectives describing the capability they add:

```ruby
class Card < ApplicationRecord
  include Closeable, Assignable, Searchable
end
```

Each concern encapsulates its own associations, callbacks, and methods:

```ruby
module Closeable
  extend ActiveSupport::Concern

  included do
    has_one :closure, dependent: :destroy
  end

  def closed?
    closure.present?
  end

  def close(closer: Current.user)
    closures.create!(user: closer)
  end
end
```

When a concern's logic is complex, delegate to a dedicated object behind the concern's public API:

```ruby
module Copyable
  extend ActiveSupport::Concern

  def copy_to(destination)
    Copier.new(self, destination).copy
  end
end
```

This keeps the model's public API clean (`recording.copy_to(destination)`) while the implementation complexity lives in `Copier`. The model acts as a facade -- it doesn't violate SRP because it delegates, not implements.

## State as records

Represent state changes as separate models instead of boolean columns. This preserves history, allows richer data per state change, and maps naturally to CRUD.

```ruby
# BAD -- boolean flags
class Card < ApplicationRecord
  # closed: boolean column
  # pinned: boolean column
end

# GOOD -- state records
class Closure < ApplicationRecord
  belongs_to :card, touch: true
  belongs_to :closer, class_name: "User"
end

class Pin < ApplicationRecord
  belongs_to :card
  belongs_to :user
end
```

Each state record becomes its own CRUD resource with its own controller (see `crud-routes` skill). Creating a Closure closes the card. Destroying it reopens it.

## Non-persisted models (POROs)

Not every model needs a database table. When a domain concept involves orchestrating a multi-step process -- especially one that needs validation and form integration -- use a plain Ruby class with `ActiveModel::Model` instead of a service object.

```ruby
# BAD -- a service that imperatively runs steps
class SignupService
  def call(email:, name:)
    identity = Identity.find_or_create_by!(email_address: email)
    account = Account.create_with_owner(name: name, identity: identity)
    # ...
  end
end

# GOOD -- a domain model that happens to not be persisted
class Signup
  include ActiveModel::Model
  include ActiveModel::Validations

  attr_accessor :full_name, :email_address

  validates :email_address, format: { with: URI::MailTo::EMAIL_REGEXP }, on: :identity_creation
  validates :full_name, presence: true, on: :completion

  def create_identity
    @identity = Identity.find_or_create_by!(email_address: email_address)
  end

  def complete
    if valid?(:completion)
      create_account
    end
  end
end
```

This gives you validations, error handling, and `form_with` compatibility while keeping the concept a proper domain model. The controller interacts with it the same way as any ActiveRecord model:

```ruby
class SignupsController < ApplicationController
  def create
    @signup = Signup.new(signup_params)
    if @signup.valid?(:identity_creation)
      @signup.create_identity
    end
  end
end
```

Use non-persisted models when: a domain concept needs validation but no table, a process spans multiple steps or screens, or a form doesn't map directly to a single ActiveRecord model.

## Async operations

Use `_later` / `_now` naming for methods that enqueue background work:

```ruby
module Relayable
  extend ActiveSupport::Concern

  included do
    after_create_commit :relay_later
  end

  def relay_later
    RelayJob.perform_later(self)
  end

  def relay_now
    # actual work here
  end
end
```

Jobs should be thin -- they just call the `_now` method on the model.
