---
type: Concept
title: Service vs Controller Layer
description: "Fat services and thin controllers—controllers expose view state; services encapsulate reusable domain and integration logic."
tags: [angularjs, architecture, controllers, services]
prerequisites:
  - concepts/separation-of-concerns
  - concepts/services
  - concepts/controller-scope-model
related:
  - concepts/singleton-behavior
  - concepts/mvc-model
  - concepts/controller-as-syntax
resource: "https://docs.angularjs.org/guide/controller"
timestamp: 2026-01-01
---

## Summary

The **service vs controller layer** pattern assigns **controllers** to a specific view instance: load data onto scope, handle user gestures, manage view-local UI flags. **Services** hold logic shared across controllers: API integration, validation rules, caching, authentication, and cross-route session state.

## Mental model

```
Controller (per view, dies with scope)
  → calls Service methods (singleton)
  → assigns results to vm / $scope
  → template binds to scope

Service (app lifetime)
  → no $scope awareness
  → returns promises / data structures
  → testable in isolation
```

Heuristics:

| Put in controller | Put in service |
|-------------------|----------------|
| `$scope.isEditing = true` | `validateOrder(order)` |
| `vm.submit = function()` delegating | `$http` calls |
| Route-specific view model assembly | Shared caches |
| `$routeParams` interpretation | Authentication token management |

**Controller As** reinforces thin controllers by namespacing `vm` and avoiding `$scope` ceremony.

## Example

```javascript
.service('TodoService', function($http, $q) {
  var todos = [];

  this.list = function() { return todos; };

  this.load = function() {
    return $http.get('/api/todos').then(function(res) {
      todos = res.data;
      return todos;
    });
  };

  this.add = function(text) {
    return $http.post('/api/todos', { text: text }).then(function(res) {
      todos.push(res.data);
      return res.data;
    });
  };
})

.controller('TodoListCtrl', function TodoListCtrl(TodoService) {
  var vm = this;
  vm.todos = [];
  vm.newTodo = '';

  TodoService.load().then(function(data) {
    vm.todos = data;
  });

  vm.add = function() {
    TodoService.add(vm.newTodo).then(function(todo) {
      vm.todos.push(todo);
      vm.newTodo = '';
    });
  };
});
```

## Common mistakes

- Duplicating business logic across multiple controllers instead of extracting a service.
- Services that depend on `$scope` or `$routeParams`—pass parameters explicitly instead.
- Controllers that manipulate DOM (toggle classes manually) instead of binding flags.
- Storing view-specific `$scope` state in singleton services without namespacing by context.

## Related concepts

- [Services](/concepts/services.md) — Service registration and singleton behavior.
- [Controller Scope Model](/concepts/controller-scope-model.md) — Controller lifecycle.
- [Separation of Concerns](/concepts/separation-of-concerns.md) — Broader architectural context.
