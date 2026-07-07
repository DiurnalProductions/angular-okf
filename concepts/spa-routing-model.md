---
type: Concept
title: SPA Routing Model
description: "Single-page navigation using $location to change views without full page reload via hash or HTML5 history API."
tags: [angularjs, routing, spa, location]
prerequisites:
  - concepts/ng-route
related:
  - concepts/view-swapping
  - concepts/controller-scope-model
resource: "https://docs.angularjs.org/guide/$location"
timestamp: 2026-01-01
---

## Summary

The **SPA routing model** in AngularJS uses **`$location`** to synchronize the browser URL with application state without full server round-trips. **`ngRoute`** (or UI-Router) listens to location changes and swaps views client-side. Two URL modes exist: **hashbang** (`#/path`) and **HTML5 mode** (`/path` with `<base href>`).

## Mental model

```
User clicks ng-href / browser back
  → $location updates URL
  → $route / $state matches path
  → resolve async dependencies
  → destroy old scope subtree
  → compile + link new view in ng-view
  → no full document reload
```

Components:

- **`$location`** — read/write URL, `$location.path()`, `$location.search()`.
- **`$locationProvider`** — `html5Mode`, `hashPrefix` configuration.
- **`$anchorScroll`** — scroll to hash fragments.
- **Server config** — HTML5 mode requires rewrite rules to `index.html`.

AngularJS SPAs are **stateful in memory**—browser refresh reloads JS bootstrap; route resolves re-fetch data.

## Example

```javascript
.config(function($locationProvider) {
  $locationProvider.html5Mode(true);
})
.run(function($rootScope, $location) {
  $rootScope.$on('$locationChangeStart', function(event, next, current) {
    // auth guard example
  });
});
```

```html
<a ng-href="/users/123">User 123</a>
<!-- html5 mode -->
<a href="#/users/123">User 123</a>
<!-- hash mode -->
```

## Common mistakes

- HTML5 mode without `<base href="/">`—broken relative URLs.
- Server returning 404 on deep links in HTML5 mode.
- Using `<a href>` without `ng-click` prevention for internal routes incorrectly.
- Assuming route change clears all `$rootScope` state—it does not.

## Related concepts

- [ngRoute](/concepts/ng-route.md) — Default routing module.
- [View Swapping](/concepts/view-swapping.md) — DOM effect of navigation.
- [Controller Scope Model](/concepts/controller-scope-model.md) — Per-route controller lifecycle.
