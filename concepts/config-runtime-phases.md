---
type: Concept
title: Config vs Runtime Phases
description: "Two bootstrap phases—config registers providers; run executes after injection is ready and DOM is compiled."
tags: [angularjs, modules, bootstrap, di]
prerequisites:
  - concepts/angular-module
related:
  - concepts/providers
  - concepts/dependency-injection-container
  - concepts/service-lifecycle
resource: "https://docs.angularjs.org/guide/module#module-loading-dependencies"
timestamp: 2026-01-01
---

## Summary

AngularJS bootstrap has distinct **configuration** and **runtime** phases. `.config()` blocks run during provider registration—only providers and constants are injectable. `.run()` blocks execute after the injector is created—full services are available. Controllers and directives instantiate later during compilation and linking.

## Mental model

```
Module registration (JS loads)
  → config blocks (configure $routeProvider, $httpProvider, etc.)
  → provider recipes collected
Bootstrap (ng-app / angular.bootstrap)
  → injector created from providers
  → run blocks execute
  → $compile linked to DOM
  → controllers instantiated on demand
  → services instantiated on first injection
```

| Phase | Injectables | Purpose |
|-------|-------------|---------|
| `config` | providers, `$provide`, constants | Wire routes, interceptors, decorator recipes |
| `run` | any service | Startup logic, event listeners, initial $http |
| runtime | any service | Per-controller, per-directive, per-service use |

You cannot inject `$http` into `.config()`—only `$httpProvider`. This prevents partially configured services from being used too early.

## Example

```javascript
angular.module('app', [])
  .config(function($routeProvider, $httpProvider) {
    $routeProvider.when('/', { template: '<h1>Home</h1>' });
    $httpProvider.defaults.withCredentials = true;
  })
  .run(function($rootScope, AuthService) {
    AuthService.restoreSession();
  });
```

## Common mistakes

- Injecting services (not providers) into `.config()` blocks.
- Putting DOM manipulation in `.run()` before `$compile` finishes—prefer directives or `$document.ready` patterns.
- Using `.config()` to fetch remote data—config must be synchronous; use `.run()` or a resolver.
- Assuming `.run()` blocks run before all modules' config blocks—config always completes first across all modules.

## Related concepts

- [angular.module](/concepts/angular-module.md) — Hosts config and run blocks.
- [Providers](/concepts/providers.md) — Only injectable during config phase (as providers).
- [Service Lifecycle](/concepts/service-lifecycle.md) — When instances materialize after config.
