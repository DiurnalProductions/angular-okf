---
type: Concept
title: Digest Cycle
description: "The $digest and $apply loop that runs watchers and synchronizes scope state with the DOM until no further changes occur."
tags: [angularjs, digest, runtime, core]
prerequisites:
  - concepts/dirty-checking
  - concepts/watchers
related:
  - concepts/async-digest-integration
  - concepts/binding-propagation
  - concepts/digest-cost
  - concepts/q-promises
resource: "https://docs.angularjs.org/guide/scope#scope-life-cycle"
timestamp: 2026-01-01
---

## Summary

The **digest cycle** is AngularJS's core runtime loop. `$rootScope.$digest()` traverses the scope tree, executing all watchers. If any listener mutates scope data, another pass runs—up to a TTL limit (default 10). `$scope.$apply(expr)` wraps code in `$eval` then starts `$digest` from `$rootScope`.

## Mental model

The digest cycle is the **heartbeat** of an AngularJS application.

```
User event / async callback
  → $apply (enters Angular zone)
    → $digest (dirty check all scopes)
      → watchers fire → DOM updates (via $watch listeners / $render)
      → if dirty again → another $digest pass
    → done (or $rootScope:infdig error if TTL exceeded)
```

Entry points:

- **`ng-click`, `ng-change`, etc.** — internally call `$apply`.
- **`$http`, `$timeout`, `$q`** — schedule `$apply` or `$digest` after resolution.
- **Manual `$scope.$apply()`** — required for third-party async callbacks.

`$applyAsync` and `$evalAsync` batch work into the current or next digest for performance.

Without digest, watchers never run and bindings appear frozen.

## Example

```javascript
// Manual integration with non-Angular event source
element.addEventListener('click', function() {
  $scope.$apply(function() {
    $scope.clicked = true;
  });
});

// Inspect digest in action (dev only)
$scope.$watch('value', function(n) { console.log('digest saw:', n); });
$scope.value = 1; // no log until digest
$scope.$apply();  // logs: digest saw: 1
```

## Common mistakes

- Calling `$apply` while already inside a digest (`$apply already in progress` error)—use `$evalAsync` instead.
- Nested `$apply` from `$http` success handlers (already wrapped)—usually harmless but can cause double digest.
- Infinite digest loops: watcher listener modifies the same watched value every pass → `$rootScope:infdig`.
- Skipping `$apply` after async jQuery/websocket callbacks, causing "stale UI" bugs.

## Related concepts

- [Dirty Checking](/concepts/dirty-checking.md) — What each digest pass executes.
- [Async Digest Integration](/concepts/async-digest-integration.md) — Bridging external async with digest.
- [Digest Cost](/concepts/digest-cost.md) — Performance characteristics of repeated passes.
- [Binding Propagation](/concepts/binding-propagation.md) — How changes spread during digest.
