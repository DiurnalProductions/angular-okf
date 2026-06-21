---
id: angularjs.angular_module
type: concept
title: angular.module
description: Module registry and composition root that defines injectable recipes, config blocks, and run blocks for an AngularJS application.
tags: [angularjs, modules, di, architecture]
prerequisites: []
related:
  - angularjs.config_runtime_phases
  - angularjs.providers
  - angularjs.dependency_injection_container
  - angularjs.ng_route
resource: https://docs.angularjs.org/guide/module
timestamp: 2026-01-01
---

## Summary

**`angular.module(name, [requires])`** creates or retrieves a module. Modules are namespaces for controllers, directives, services, filters, and configuration. The `requires` array declares dependency on other modules, merging their injectable registrations into the effective injector graph at bootstrap.

## Mental model

A module is a **recipe book**, not a runtime object.

- **Creation**: `angular.module('myApp', ['ngRoute', 'myShared'])` registers a new module.
- **Retrieval**: `angular.module('myApp')` (no second arg) fetches an existing module for further registration.
- **Bootstrap**: `angular.bootstrap(domElement, ['myApp'])` or `ng-app="myApp"` builds the injector from accumulated recipes.
- Modules enable **lazy composition**—feature areas register their own directives and services under distinct module names.

The module system separates **registration time** (before bootstrap) from **instantiation time** (after bootstrap). Nothing is injected until the app starts.

## Example

```javascript
angular.module('myApp', ['ngRoute'])
  .controller('HomeCtrl', function($scope) {
    $scope.title = 'Home';
  })
  .service('UserService', function($http) {
    this.fetch = function(id) { return $http.get('/api/users/' + id); };
  });

// In index.html: <html ng-app="myApp">
// Or manual bootstrap:
angular.bootstrap(document, ['myApp']);
```

## Common mistakes

- Calling `angular.module('myApp', [])` twice—second call throws "Module already exists".
- Forgetting to list module dependencies in `requires`, causing "Unknown provider" at runtime.
- Registering components on the wrong module after splitting into feature modules.
- Using `angular.module('myApp')` before the module is created elsewhere.

## Related concepts

- [Config vs Runtime Phases](/concepts/config-runtime-phases.md) — When module blocks execute.
- [Providers](/concepts/providers.md) — Registered via module API methods.
- [Dependency Injection Container](/concepts/dependency-injection-container.md) — Built from module recipes at bootstrap.
