---
type: Concept
title: ng-model
description: "Directive connecting form controls to scope properties via $setViewValue, $render, parsers, and formatters pipeline."
tags: [angularjs, ng-model, forms, binding]
prerequisites:
  - concepts/two-way-binding
related:
  - concepts/binding-propagation
  - concepts/scope-inheritance
  - concepts/directive-definition-object
resource: "https://docs.angularjs.org/api/ng/directive/ngModel"
timestamp: 2026-01-01
---

## Summary

**`ng-model`** binds form controls (`input`, `select`, `textarea`, custom controls) to scope properties. It uses `NgModelController` internally with a pipeline: **parsers** (view → model), **formatters** (model → view), **`$validators`**, and **`$render`** to update the DOM when the model changes.

## Mental model

```
User types in input
  → $setViewValue(rawValue)
  → run parsers → assign to scope property
  → $validate → $apply → digest
  → formatters run on other bound views
  → $render updates this control if model changed externally
```

Custom controls integrate via `require: 'ngModel'` and implementing `$render`, `$setViewValue`.

`ng-model-options` controls timing:

- `{ debounce: 300 }` — delay model updates.
- `{ updateOn: 'blur' }` — reduce digest frequency.

`ng-model` is the most common source of **scope inheritance bugs** when binding bare primitives in child scopes.

## Example

```html
<input ng-model="user.name" ng-model-options="{ debounce: 200 }">
<input type="number" ng-model="order.quantity" min="1">
<div ng-class="{ 'has-error': form.email.$invalid }">
  <input name="email" ng-model="user.email" required>
</div>
```

```javascript
.directive('capitalize', function() {
  return {
    require: 'ngModel',
    link: function(scope, element, attrs, ngModel) {
      ngModel.$parsers.push(function(value) {
        return value ? value.toUpperCase() : value;
      });
    }
  };
});
```

## Common mistakes

- `ng-model="name"` on primitive inside nested scope—use `ng-model="user.name"`.
- Forgetting `$render` implementation in custom directives.
- Validators that mutate model instead of setting `$setValidity`.
- Debounce on model without understanding delayed validation feedback.

## Related concepts

- [Two-Way Binding](/concepts/two-way-binding.md) — Conceptual foundation.
- [Scope Inheritance](/concepts/scope-inheritance.md) — Primitive binding pitfalls.
- [Digest Cycle](/concepts/digest-cycle.md) — When view updates propagate.
