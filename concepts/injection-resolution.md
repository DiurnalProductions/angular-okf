---
type: Concept
title: Injection Resolution
description: "How $injector resolves token names to provider instances across the module dependency graph."
tags: [angularjs, di, injector, resolution]
prerequisites:
  - concepts/dependency-injection-container
related:
  - concepts/circular-dependency-handling
  - concepts/providers
  - concepts/services
resource: "https://docs.angularjs.org/api/auto/service/$injector"
timestamp: 2026-01-01
---

## Summary

**Injection resolution** is the process by which `$injector` maps a requested token string (e.g., `'$http'`, `'UserService'`) to a cached instance or a provider's `$get` result. Resolution walks the merged provider registry from the bootstrapped module and its transitive `requires`.

## Mental model

```
Request token 'UserService'
  → find provider registered for 'UserService'
  → if instance cached → return cache
  → else resolve $get's dependencies recursively
  → invoke $get → cache → return
```

Rules:

- **First wins** for duplicate registrations across modules (last loaded provider overrides in module merge—behavior depends on module order; avoid duplicate tokens).
- **Built-in services** (`$scope`, `$compile`, `$http`) come from `ng` module automatically included.
- **Locals** passed to `$controller`, `$compile` linking, or `$injector.instantiate` can override or supplement injection.
- **`$injector.has(token)`** checks availability without instantiation.

Resolution is **synchronous**. Async module loading (e.g., ocLazyLoad) is a third-party pattern outside core AngularJS.

## Example

```javascript
// Explicit annotation survives minification
function MyCtrl($scope, $location) { /* ... */ }
MyCtrl.$inject = ['$scope', '$location'];

// Locals override during linking
$compile(element)(scope, function(scope, element, attrs, ctrl, transcludeFn) {
  // ctrl resolved from directive controller if requested in DI array
});
```

## Common mistakes

- Typo in token name → `$injector:unpr` (Unknown provider).
- Injecting `$scope` into a service constructor—services are singletons; `$scope` is per DOM subtree.
- Assuming alphabetical or declaration-order injection order for unrelated services—only `$get` dependency chain order matters.
- Registering the same service name in two feature modules with different implementations.

## Related concepts

- [Dependency Injection Container](/concepts/dependency-injection-container.md) — Host of resolution logic.
- [Circular Dependency Handling](/concepts/circular-dependency-handling.md) — When recursive resolution fails.
- [Providers](/concepts/providers.md) — What gets resolved.
