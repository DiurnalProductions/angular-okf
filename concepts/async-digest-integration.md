---
type: Concept
title: Async Digest Integration
description: "How $apply, $applyAsync, $evalAsync, and $timeout bridge non-Angular async callbacks into the digest cycle."
tags: [angularjs, async, digest, apply]
prerequisites:
  - concepts/q-promises
  - concepts/digest-cycle
  - concepts/http-service
related:
  - concepts/binding-propagation
  - concepts/q-promises
resource: "https://docs.angularjs.org/api/ng/type/$rootScope.Scope"
timestamp: 2026-01-01
---

## Summary

**Async digest integration** is the bridge between third-party asynchronous callbacks and AngularJS's dirty-checking world. `$scope.$apply`, `$scope.$applyAsync`, `$scope.$evalAsync`, and `$timeout` schedule work so scope mutations propagate to the DOM.

## Mental model

| API | When to use |
|-----|-------------|
| `$apply(fn)` | Enter Angular from outside (websocket, jQuery event)—runs digest after fn |
| `$applyAsync(fn)` | Batch multiple outside updates into one digest |
| `$evalAsync(fn)` | Schedule work before next digest pass (already inside Angular) |
| `$timeout(fn, delay)` | `$apply`-wrapped delayed execution; `$timeout.cancel` for cleanup |
| `$q.then` | Already digest-aware |

```
Outside world event
  → mutate scope in callback
  → MUST wrap in $apply (or use digest-aware service)
  → digest runs → bindings update
```

Without integration, the model changes but the view stays stale until some unrelated digest occurs.

## Example

```javascript
// WebSocket (outside Angular)
socket.onmessage = function(event) {
  $scope.$apply(function() {
    $scope.messages.push(JSON.parse(event.data));
  });
};

// Prefer $timeout for testing/mocking
$timeout(function() {
  $scope.loaded = true;
}, 0);

// Inside digest already — avoid nested $apply
$scope.$evalAsync(function() {
  $scope.status = 'processing';
});
```

## Common mistakes

- `$apply already in progress` error from calling `$apply` inside `$apply`.
- Using `$apply` in `$http` `.then` (redundant—already wrapped).
- Raw `setTimeout` without `$apply` or `$timeout`.
- Forgetting `$timeout.cancel` on destroyed scopes (leaks + errors after `$destroy`).

## Related concepts

- [Digest Cycle](/concepts/digest-cycle.md) — What async integration triggers.
- [$q Promises](/concepts/q-promises.md) — Built-in digest integration.
- [$http Service](/concepts/http-service.md) — Resolves with digest notification.
