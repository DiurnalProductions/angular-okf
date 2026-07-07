---
type: Concept
title: ngRoute
description: "Core routing module with $routeProvider for mapping URLs to templates, controllers, and resolve dependencies."
tags: [angularjs, routing, ngroute, spa]
prerequisites:
  - concepts/angular-module
related:
  - concepts/spa-routing-model
  - concepts/view-swapping
  - concepts/config-runtime-phases
resource: "https://docs.angularjs.org/api/ngRoute"
timestamp: 2026-01-01
---

## Summary

**ngRoute** (`angular-route` bower/npm package, module name `ngRoute`) provides **`$routeProvider`** for declarative route tables, **`$route`** for current route state, and **`$routeParams`** for URL parameter extraction. Routes map paths to templates and controllers displayed in `ng-view`.

## Mental model

```
URL change ($location)
  → $route service matches route definition
  → run resolve promises ($q)
  → on success: swap ng-view template + instantiate controller
  → on failure: $routeChangeError
```

Route definition fields:

- `template` / `templateUrl`
- `controller` / `controllerAs`
- `resolve` — injectable map resolved before activation
- `redirectTo` — navigation shortcut

`$routeProvider` is configured in `.config()` using `$routeProvider.when()` and `.otherwise()`.

For larger apps, **UI-Router** (third-party) replaces ngRoute with state-based routing—still AngularJS 1.x SPA pattern but not part of core.

## Example

```javascript
angular.module('app', ['ngRoute'])
  .config(function($routeProvider) {
    $routeProvider
      .when('/users/:userId', {
        templateUrl: 'views/user.html',
        controller: 'UserCtrl',
        controllerAs: 'vm',
        resolve: {
          user: function($route, UserService) {
            return UserService.get($route.current.params.userId);
          }
        }
      })
      .otherwise({ redirectTo: '/' });
  });
```

```html
<div ng-view></div>
```

## Common mistakes

- Forgetting to add `'ngRoute'` to module dependencies.
- Resolve functions that reject without error route handling—blank views.
- Same controller name across routes without understanding re-instantiation on param change (use `$routeParams` `$watch` or resolve).
- Mixing hash mode and HTML5 mode without server fallback configuration.

## Related concepts

- [SPA Routing Model](/concepts/spa-routing-model.md) — URL modes and navigation.
- [View Swapping](/concepts/view-swapping.md) — `ng-view` behavior.
- [Config vs Runtime Phases](/concepts/config-runtime-phases.md) — `$routeProvider` in config.
