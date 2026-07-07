---
type: Concept
title: Service Lifecycle
description: Injectable instances are created lazily on first injection and persist for the lifetime of the injector.
tags: [angularjs, services, lifecycle, di]
prerequisites:
  - concepts/services
  - concepts/dependency-injection-container
related:
  - concepts/singleton-behavior
  - concepts/config-runtime-phases
  - concepts/controller-scope-model
resource: "https://docs.angularjs.org/guide/services"
timestamp: 2026-01-01
---

## Summary

**Service lifecycle** in AngularJS is lazy and injector-scoped. Providers register at module load; instances materialize when first requested via `$injector.get` or constructor injection. Once created, the instance lives until the application (injector) is destroyed—there is no per-route or per-request lifecycle in core AngularJS.

## Mental model

```
Registration (module load)
  → provider stored in registry
First injection
  → resolve $get dependencies
  → invoke $get / constructor / factory fn
  → cache instance on injector
Subsequent injections
  → return cached instance
App teardown
  → instances eligible for GC (no formal destroy hook)
```

Implications:

- Services holding **mutable session state** persist across route changes.
- **Reset state** explicitly on logout/route if needed—no automatic cleanup.
- **`$scope.$destroy`** applies to scopes, not services.
- Decorators (`$provide.decorator`) wrap instances at first `$get`.

For per-view state, use controllers/scopes—not singleton services without reset logic.

## Example

```javascript
angular.module('app', [])
  .service('SessionStore', function() {
    var data = {};
    this.set = function(k, v) { data[k] = v; };
    this.get = function(k) { return data[k]; };
    this.clear = function() { data = {}; };
  })
  .run(function($rootScope, SessionStore) {
    $rootScope.$on('$routeChangeStart', function() {
      // Optional: partial reset on navigation
    });
  });
```

## Common mistakes

- Storing per-user UI state in services without clearing on logout.
- Assuming services re-instantiate per controller— they do not.
- Holding DOM references in services—lifetime exceeds DOM, causes leaks.
- Using services for values that should be constants or route resolves.

## Related concepts

- [Singleton Behavior](/concepts/singleton-behavior.md) — Cached instance semantics.
- [Config vs Runtime Phases](/concepts/config-runtime-phases.md) — Registration vs instantiation timing.
- [Services](/concepts/services.md) — Primary lifecycle host.
