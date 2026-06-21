---
id: angularjs.separation_of_concerns
type: concept
title: Separation of Concerns
description: Legacy AngularJS best practice of keeping DOM in directives, state in services, and controllers as thin presentation adapters.
tags: [angularjs, architecture, soc, patterns]
prerequisites:
  - angularjs.mvc_model
related:
  - angularjs.service_vs_controller_layer
  - angularjs.directive_definition_object
  - angularjs.controller_scope_model
resource: https://docs.angularjs.org/guide/concepts
timestamp: 2026-01-01
---

## Summary

**Separation of concerns** in AngularJS means each artifact has a single primary job: **directives** own DOM and reusable UI behavior, **services** own data and business logic, **controllers** wire scope to services for a specific view, **filters** format display values, and **templates** declare structure and bindings.

## Mental model

```
┌─────────────────────────────────────────┐
│ Template + Directives  (View / DOM)      │
├─────────────────────────────────────────┤
│ Controller             (Presentation)    │
├─────────────────────────────────────────┤
│ Services               (Domain / Data)   │
└─────────────────────────────────────────┘
         All synchronized by digest cycle
```

Guidelines:

- **No `$()` in controllers** — jQuery belongs in directives (if at all).
- **No `$http` in filters** — filters must be pure synchronous functions.
- **Shared state** across routes → services with explicit reset, not `$rootScope` soup.
- **Reusable UI** → directives/components with isolate scope, not copy-paste templates.

Testing follows separation: services unit-tested without DOM; controllers tested with mocked services; directives with `$compile` tests.

## Example

```javascript
// Good separation
.service('InvoiceCalculator', function() {
  this.total = function(items) {
    return items.reduce(function(sum, i) { return sum + i.price; }, 0);
  };
})
.controller('InvoiceCtrl', function($scope, InvoiceCalculator) {
  $scope.items = [];
  $scope.getTotal = function() {
    return InvoiceCalculator.total($scope.items);
  };
})
.directive('currencyInput', function() {
  return { require: 'ngModel', /* DOM formatting */ };
});
```

## Common mistakes

- **God controllers** — hundreds of lines mixing validation, HTTP, and DOM flags.
- **`$rootScope` event soup** — `$broadcast` for everything instead of service APIs.
- **Template logic** — complex `ng-if` chains duplicating business rules from controllers.
- **Service DOM access** — breaks testability and lifecycle assumptions.

## Related concepts

- [MVC-ish Model](/concepts/mvc-model.md) — Architectural overview.
- [Service vs Controller Layer](/concepts/service-vs-controller-layer.md) — Specific fat-service/thin-controller pattern.
- [Directive Definition Object](/concepts/directive-definition-object.md) — DOM concern encapsulation.
