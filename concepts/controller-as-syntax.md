---
type: Concept
title: Controller As Syntax
description: "controllerAs alias pattern attaches the controller instance to scope as vm, reducing $scope property proliferation."
tags: [angularjs, controllers, controller-as, legacy]
prerequisites:
  - concepts/controller-scope-model
related:
  - concepts/scope-inheritance
  - concepts/mvc-model
  - concepts/service-vs-controller-layer
resource: "https://docs.angularjs.org/api/ng/directive/ngController"
timestamp: 2026-01-01
---

## Summary

**Controller As** (`controllerAs: 'vm'` in routes or `ng-controller="MyCtrl as vm"`) binds the controller instance to the scope under an alias. Templates reference `vm.property` instead of bare `property`. This clarifies data origin and reduces accidental scope inheritance shadowing.

## Mental model

Traditional style:

```
$scope.foo = 1  → template: {{ foo }}
```

Controller As style:

```
this.foo = 1    → scope.vm = controllerInstance
                → template: {{ vm.foo }}
```

Benefits in legacy AngularJS:

- Explicit view model namespace (`vm`, `ctrl`, `$ctrl`).
- Easier migration toward component-like thinking (without Angular 2 components).
- Fewer primitive shadowing bugs in nested templates.

The controller is still a function—use `var vm = this` pattern for consistent alias inside the function body.

## Example

```javascript
angular.module('app', [])
  .controller('ProfileCtrl', function() {
    var vm = this;
    vm.name = 'Alice';
    vm.save = function() { /* ... */ };
  });
```

```html
<div ng-controller="ProfileCtrl as vm">
  <input ng-model="vm.name">
  <button ng-click="vm.save()">Save</button>
</div>
```

```javascript
$routeProvider.when('/profile', {
  controller: 'ProfileCtrl',
  controllerAs: 'vm',
  templateUrl: 'profile.html'
});
```

## Common mistakes

- Mixing `$scope` and `this` in the same controller inconsistently.
- Forgetting `as vm` in template when using `controllerAs` in route config only (route handles it; inline `ng-controller` needs explicit `as`).
- Expecting Controller As to eliminate scopes— a scope still exists; alias is a property on it.
- Using Controller As without updating all template bindings from bare names.

## Related concepts

- [Controller Scope Model](/concepts/controller-scope-model.md) — Underlying instantiation.
- [Scope Inheritance](/concepts/scope-inheritance.md) — Problems Controller As mitigates.
- [MVC-ish Model](/concepts/mvc-model.md) — View model placement.
