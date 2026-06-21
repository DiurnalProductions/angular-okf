---
id: angularjs.view_swapping
type: concept
title: View Swapping
description: ng-view destroys the previous route scope and DOM, then compiles and links the new route template and controller.
tags: [angularjs, routing, ng-view, dom]
prerequisites:
  - angularjs.spa_routing_model
  - angularjs.template_compilation
related:
  - angularjs.dom_linking
  - angularjs.controller_scope_model
  - angularjs.ng_route
resource: https://docs.angularjs.org/api/ngRoute/directive/ngView
timestamp: 2026-01-01
---

## Summary

**View swapping** is the process by which `ng-view` replaces content on route activation. The previous child scope is destroyed (`$destroy`), DOM is cleared, the new template is fetched (if URL), compiled, linked to a fresh scope, and the route controller is instantiated.

## Mental model

```
Route A active in ng-view
  → navigate to Route B
  → $routeChangeStart
  → resolve Route B dependencies ($q)
  → $routeChangeSuccess
  → destroy Route A scope + listeners
  → empty ng-view element
  → $compile(Route B template)(newScope)
  → $controller(Route B ctrl, { $scope: newScope })
  → digest updates new view
```

Lifecycle hooks:

- `$routeChangeStart` / `$routeChangeSuccess` / `$routeChangeError`
- `$viewContentLoaded` on ng-view after DOM linked
- `$destroy` on outgoing scope—cleanup point for deregistering watchers

Partial views use multiple `ng-view` with **`ng-view` + named views** only in UI-Router; core ngRoute supports single primary outlet.

## Example

```html
<!-- shell layout -->
<div ng-include="'partials/header.html'"></div>
<div ng-view class="main-content"></div>
<div ng-include="'partials/footer.html'"></div>
```

```javascript
$scope.$on('$destroy', function() {
  // cleanup timers, websocket subscriptions for this view
  timer.cancel();
});
```

## Common mistakes

- Memory leaks from `$rootScope` listeners registered in route controllers without `$destroy` cleanup.
- Global `$broadcast` listeners in every controller instead of service-level subscription.
- Expecting ng-include shell partials to destroy on route change—they persist unless inside ng-view.
- Flash of empty content while `templateUrl` loads—use resolve or `$templateCache`.

## Related concepts

- [DOM Linking](/concepts/dom-linking.md) — Links new route template.
- [SPA Routing Model](/concepts/spa-routing-model.md) — Navigation trigger.
- [Template Compilation](/concepts/template-compilation.md) — Compiles new view markup.
