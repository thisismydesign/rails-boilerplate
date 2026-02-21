---
name: crud-routes
description: Add RESTful routes following vanilla Rails DDD conventions. Use when wiring a new resource. Covers the everything-is-CRUD pattern and scope module nesting.
---

# Route Patterns

## Everything is CRUD

Never add custom actions to a resource. Instead, model the action as a new resource:

```ruby
# BAD -- custom actions
resources :cards do
  post :close
  post :reopen
  post :pin
end

# GOOD -- each action is CRUD on a new resource
resources :cards do
  scope module: :cards do
    resource :closure    # create = close, destroy = reopen
    resource :pin        # create = pin, destroy = unpin
  end
end
```

This pairs with the state-as-records pattern (see `crud-model` skill). Each state change gets its own model, controller, and singular resource route.

## Nesting with `scope module:`

Use `scope module:` (not `namespace`) when nesting resources. This namespaces the controller without doubling the parent name in the URL:

```ruby
resources :cards do
  scope module: :cards do
    resources :comments      # Cards::CommentsController, URL: /cards/:card_id/comments
    resource :closure        # Cards::ClosuresController, URL: /cards/:card_id/closure
  end
end
```

With `namespace`, the URL would be `/cards/:card_id/cards/comments` -- redundant.

## Singular resources for state changes

Use `resource` (singular) for state-change models where there's one per parent and no ID in the URL:

```ruby
resource :closure      # create/destroy only -- no index, no ID
resource :publication   # create = publish, destroy = unpublish
resource :goldness      # create = gild, destroy = ungild
```

The controller typically only needs `create` and `destroy`.
