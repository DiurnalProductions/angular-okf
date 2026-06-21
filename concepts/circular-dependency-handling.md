---
id: angularjs.circular_dependency_handling
type: concept
title: Circular Dependency Handling
description: Strategies for breaking circular DI chains that cause $injector:unpr or $injector:cdep errors at bootstrap or instantiation.
tags: [angularjs, di, circular-dependency, debugging]
prerequisites:
  - angularjs.injection_resolution
related:
  - angularjs.dependency_injection_container
  - angularjs.services
  - angularjs.factories
resource: https://docs.angularjs.org/error/$injector/unpr
timestamp: 2026-01-01
---

## Summary

**Circular dependencies** occur when service A depends on service B and B depends on A (directly or transitively). AngularJS detects cycles during instantiation and throws `$injector:cdep` or masks them as `$injector:unpr`. The standard fix is lazy resolution via `$injector` inside a factory function rather than constructor injection.

## Mental model

```
// Broken: A → B → A
.factory('A', function(B) { return { use: B }; })
.factory('B', function(A) { return { use: A }; })
// Bootstrap fails: circular dependency
```

AngularJS builds a dependency graph at `$get` invocation time. Cycles prevent deterministic instantiation order.

**Break the cycle**:

1. **`$injector` lazy get** — inject `$injector`, call `$injector.get('OtherService')` inside methods, not at factory init.
2. **Restructure** — extract shared logic to a third service C that both A and B depend on.
3. **Events** — decouple with `$rootScope.$emit` / `$broadcast` (use sparingly).

Circular DI is an architecture smell in AngularJS—prefer unidirectional dependency flow.

## Example

```javascript
// Fixed with lazy lookup
angular.module('app', [])
  .factory('OrderService', function($injector) {
    return {
      notifyUser: function(order) {
        var UserService = $injector.get('UserService');
        UserService.sendConfirmation(order.userId);
      }
    };
  })
  .factory('UserService', function($injector) {
    return {
      sendConfirmation: function(userId) { /* ... */ },
      lastOrder: function() {
        return $injector.get('OrderService').getLast();
      }
    };
  });
```

## Common mistakes

- Misdiagnosing `$injector:unpr` as a missing module when the real issue is a hidden circular dependency.
- Using `$injector.get` at factory initialization time instead of inside methods (still circular if both call each other in `$get`).
- Over-using `$rootScope` events to avoid all cross-service calls.
- Circular dependencies between `.config()` provider configuration and runtime services (wrong phase).

## Related concepts

- [Injection Resolution](/concepts/injection-resolution.md) — Where cycles are detected.
- [Services](/concepts/services.md) — Common site of circular references.
- [Dependency Injection Container](/concepts/dependency-injection-container.md) — `$injector` API.
