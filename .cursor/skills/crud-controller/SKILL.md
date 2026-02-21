---
name: crud-controller
description: Create CRUD controllers following vanilla Rails DDD conventions. Use when adding a new controller. Covers thin controllers that invoke rich models directly, without service objects.
---

# Controller Patterns

## Thin controllers invoke models directly

Controllers should only do three things: set up the resource, call a model method, and respond. All business logic belongs in the model.

```ruby
# BAD -- business logic in the controller
def create
  @card = Card.find(params[:card_id])
  @card.update!(closed: true, closed_at: Time.current, closed_by: Current.user)
  @card.watchers.each { |w| NotifyJob.perform_later(w, @card) }
  redirect_to @card
end

# GOOD -- controller calls a single model method
def create
  @card.close
  redirect_to @card
end
```

For simple CRUD, plain ActiveRecord operations in the controller are fine:

```ruby
def create
  @comment = @card.comments.create!(comment_params)
end
```

The line is: if the operation is more than a single ActiveRecord call, it should be a model method.

## When a controller action gets complex

A complex controller action is a signal. It usually means one of:

1. **Business logic should move to the model** -- extract a method on the model that the controller calls.
2. **You need a new resource** -- if the action doesn't map to standard CRUD, introduce a new resource (see `crud-routes` skill). E.g. "close a card" becomes `Cards::ClosuresController#create`.

Never solve this by introducing a service object.
