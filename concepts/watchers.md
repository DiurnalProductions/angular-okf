---
id: angularjs.watchers
type: concept
title: Watchers
description: Functions registered on scopes that compare old and new expression values during each digest pass.
tags: [angularjs, watchers, reactivity, core]
prerequisites:
  - angularjs.scope
related:
  - angularjs.dirty_checking
  - angularjs.digest_cycle
  - angularjs.two_way_binding
  - angularjs.watcher_explosion
resource: https://docs.angularjs.org/api/ng/type/$rootScope.Scope
timestamp: 2026-01-01
---

## Summary

**Watchers** are the fundamental unit of reactivity in AngularJS. Each `$watch`, `$watchGroup`, or `$watchCollection` registration adds a watcher to a scope's internal queue. During digest, AngularJS evaluates the watch expression, compares the result to the previous value, and runs the listener if a change is detected.

## Mental model

AngularJS reactivity is **pull-based polling**, not push-based notification.

- A watcher is a tuple: `(watchFn, listenerFn, lastValue)`.
- `{{ expression }}` interpolations compile into watchers automatically.
- `ng-model` adds watchers bridging view value and model value.
- Directives may register watchers in their `link` functions.
- More watchers = more work per digest. There is no granular dependency graph—every digest potentially runs every watcher (until stable).

Watchers do not run continuously. They run only when `$digest` or `$apply` triggers a digest cycle.

## Example

```javascript
$scope.$watch('user.name', function(newVal, oldVal) {
  if (newVal !== oldVal) {
    console.log('Name changed from', oldVal, 'to', newVal);
  }
});

$scope.$watch(function(scope) {
  return scope.items.length;
}, function(newLen, oldLen) {
  console.log('Item count:', newLen);
});
```

## Common mistakes

- Deep-watching large objects with `$watch('obj', fn, true)`—triggers expensive `angular.equals` on every digest.
- Creating watchers inside other watchers' listeners, causing watcher count to grow unbounded.
- Forgetting that `$watch` listeners fire at least once on registration (with `newVal === oldVal` initially for reference watches).
- Using `$watch` for logic that belongs in event handlers when no binding synchronization is needed.

## Related concepts

- [Dirty Checking](/concepts/dirty-checking.md) — The comparison strategy watchers use.
- [Digest Cycle](/concepts/digest-cycle.md) — Executes all watchers until stable.
- [Two-Way Binding](/concepts/two-way-binding.md) — Built on paired watchers between model and DOM.
- [Watcher Explosion](/concepts/watcher-explosion.md) — Performance cost of excessive watchers.
