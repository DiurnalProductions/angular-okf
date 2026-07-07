---
type: Concept
title: Controller Scope Model
description: "Controllers attach presentation logic to a scope; AngularJS instantiates them when ng-controller or route resolves."
tags: [angularjs, controllers, scope, mvc]
prerequisites:
  - concepts/scope
related:
  - concepts/scope-inheritance
  - concepts/controller-as-syntax
  - concepts/mvc-model
  - concepts/service-vs-controller-layer
resource: "https://docs.angularjs.org/guide/controller"
timestamp: 2026-01-01
---

## Summary

A **controller** in AngularJS is a function (or array-wrapped function) that receives a `$scope` (or uses `controllerAs`) and attaches properties and methods for a view. Controllers are instantiated by `$controller` when a directive like `ng-controller` or `ng-view` requests them. They do not manage digest—they participate in it via scope mutations.

## Mental model

Controllers are **scope decorators**, not view owners:

```
Route / ng-controller
  → $controller('MyCtrl', { $scope: childScope })
    → controller function runs
      → $scope.title = '...'
      → $scope.save = function() { ... }
  → template binds to same scope
  → digest updates DOM from scope
```

Responsibilities (ideal):

- Expose view model state on scope (or `vm`).
- Handle user actions (`ng-click` handlers).
- Delegate domain logic to **services**.

Anti-patterns:

- DOM manipulation in controllers.
- `$http` calls with complex business rules inline.
- Storing long-lived state that survives view destruction.

When the view is destroyed, the scope `$destroy`s and the controller instance is discarded.

## Example

```javascript
angular.module('app', [])
  .controller('TodoCtrl', function($scope, TodoService) {
    $scope.todos = [];

    TodoService.load().then(function(data) {
      $scope.todos = data;
    });

    $scope.addTodo = function(text) {
      TodoService.add(text).then(function(todo) {
        $scope.todos.push(todo);
      });
    };
  });
```

## Common mistakes

- Treating controllers as singletons—they are created per scope instance.
- Using `$scope.$apply` inside every controller method (usually unnecessary—Angular handles it for DOM events).
- `$scope.$on` listeners without `$destroy` cleanup (memory leaks).
- Fat controllers with hundreds of lines instead of extracted services.

## Related concepts

- [Scope](/concepts/scope.md) — What controllers extend with behavior.
- [Scope Inheritance](/concepts/scope-inheritance.md) — Nested controller scope chains.
- [Controller As Syntax](/concepts/controller-as-syntax.md) — Alias-based view model pattern.
