---
type: Concept
title: Dependency Injection Container
description: "The $injector built at bootstrap resolves tokens to instances using module recipes and annotation metadata."
tags: [angularjs, di, injector, modules]
prerequisites:
  - concepts/angular-module
  - concepts/providers
related:
  - concepts/injection-resolution
  - concepts/circular-dependency-handling
  - concepts/service-lifecycle
resource: "https://docs.angularjs.org/guide/di"
timestamp: 2026-01-01
---

## Summary

The **dependency injection container** (`$injector`) is created when AngularJS bootstraps. It merges provider recipes from the app module and all required modules, instantiates services on demand, and injects dependencies into controllers, directives, filters, and other factories by resolving parameter names or explicit annotations.

## Mental model

AngularJS DI is **name-based resolution** at runtime (after minification-safe annotation):

```
Bootstrap
  → collect all providers from module graph
  → create root injector
  → $injector.get('MyService') → runs provider.$get → caches instance
  → $injector.instantiate(Controller, locals) → new Controller(deps...)
```

Annotation strategies (in order of reliability):

1. **Inline array**: `['$http', 'MyService', function($http, MyService) { }]`
2. **`$inject` property**: `MyFn.$inject = ['$http', 'MyService']`
3. **Implicit** (dev only): parameter names parsed from `function($http)` — breaks under minification.

The injector hierarchy supports `$injector.get`, `$injector.invoke`, and `$injector.instantiate` for programmatic resolution.

## Example

```javascript
angular.module('app', [])
  .controller('MainCtrl', ['$scope', 'UserService', function($scope, UserService) {
    UserService.load().then(function(user) {
      $scope.user = user;
    });
  }]);

// Programmatic invocation
angular.module('app').run(function($injector) {
  $injector.invoke(['Logger', function(Logger) {
    Logger.log('App started');
  }]);
});
```

## Common mistakes

- Relying on implicit annotation in production builds without `ngAnnotate` or manual arrays.
- Creating multiple bootstrapped apps on the same page without understanding separate injector trees.
- Using `$injector.get` inside config blocks for runtime services.
- Module load order issues—registering consumers before providers on the same module is fine, but cross-module requires must be declared.

## Related concepts

- [Injection Resolution](/concepts/injection-resolution.md) — Lookup order and token matching.
- [Circular Dependency Handling](/concepts/circular-dependency-handling.md) — Breaking DI cycles.
- [angular.module](/concepts/angular-module.md) — Source of provider recipes.
