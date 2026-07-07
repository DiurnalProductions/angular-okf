---
type: Concept
title: Singleton Behavior
description: "Each service, factory, or value registration yields one shared instance per injector for the application lifetime."
tags: [angularjs, singleton, services, di]
prerequisites:
  - concepts/service-lifecycle
related:
  - concepts/services
  - concepts/factories
  - concepts/service-vs-controller-layer
resource: "https://docs.angularjs.org/guide/services"
timestamp: 2026-01-01
---

## Summary

AngularJS **services are singletons** within an injector. The first injection invokes the provider's `$get` (or equivalent recipe) and caches the result. All subsequent requests for the same token receive the identical object reference.

## Mental model

```
$injector.get('ApiService') === $injector.get('ApiService')  // true

Controller A ──┐
Controller B ──┼──► same ApiService instance
Directive C  ──┘
```

Contrast with:

- **Controllers** — new instance per scope attachment.
- **Scopes** — new instance per DOM binding context (with inheritance).
- **Filters** — singleton factory, but stateless by convention.
- **Multiple bootstraps** — separate injectors → separate singleton sets.

Singleton behavior makes services ideal for **shared caches**, **WebSocket connections**, **event buses**, and **API clients**. It requires discipline when storing mutable state.

## Example

```javascript
.factory('Counter', function() {
  var count = 0;
  return {
    increment: function() { return ++count; },
    get: function() { return count; }
  };
});

// CtrlA and CtrlB share count state
```

## Common mistakes

- Expecting factory to return new instance per injection—only the inner factory function pattern creates multiple instances if you design it that way.
- Accidental global state bugs when tests share singleton state between specs without `$injector` reset/module reload.
- Using `$rootScope` as a global state bag instead of intentional service singletons (both persist; service is clearer).
- Multiple `angular.bootstrap` on same page with unintended shared services if modules overlap incorrectly.

## Related concepts

- [Service Lifecycle](/concepts/service-lifecycle.md) — When singleton is created.
- [Services](/concepts/services.md) — Registration recipe.
- [Service vs Controller Layer](/concepts/service-vs-controller-layer.md) — Where state should live.
