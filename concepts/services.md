---
type: Concept
title: Services
description: "service() recipe registers a constructor function; AngularJS instantiates it with new and caches the result as a singleton."
tags: [angularjs, services, di, singleton]
prerequisites:
  - concepts/providers
related:
  - concepts/factories
  - concepts/singleton-behavior
  - concepts/service-lifecycle
  - concepts/service-vs-controller-layer
resource: "https://docs.angularjs.org/guide/services"
timestamp: 2026-01-01
---

## Summary

**`.service(name, constructorFn)`** registers an injectable whose `$get` equivalent is `function() { return new constructorFn(...arguments); }`. The instance is attached to `this` inside the constructor. Services are the standard home for business logic, API clients, and shared state in AngularJS applications.

## Mental model

Services are **singletons by injector scope**:

- First injection triggers instantiation.
- Subsequent injections receive the same instance.
- Use services for **stateless operations** or **carefully managed shared state**.
- Controllers should delegate heavy logic to services—controllers wire scope to service methods.

`service` vs `factory`:

- **service**: write a constructor, attach methods to `this`.
- **factory**: write a function that returns any value (object, function, primitive).

## Example

```javascript
angular.module('app', [])
  .service('TodoService', function($http) {
    var todos = [];

    this.list = function() { return todos; };

    this.add = function(text) {
      todos.push({ text: text, done: false });
    };

    this.sync = function() {
      return $http.get('/api/todos').then(function(res) {
        todos = res.data;
      });
    };
  });
```

## Common mistakes

- Returning a value from a `service` constructor instead of attaching to `this`—the return value is ignored.
- Using services for DOM manipulation—belongs in directives.
- Storing per-view state in a service when each controller instance needs isolation.
- Creating circular dependencies between two services without `$injector` lazy lookup.

## Related concepts

- [Factories](/concepts/factories.md) — Alternative registration when returning a plain object.
- [Singleton Behavior](/concepts/singleton-behavior.md) — One instance per injector.
- [Service vs Controller Layer](/concepts/service-vs-controller-layer.md) — Architectural placement.
