---
id: angularjs.binding_propagation
type: concept
title: Binding Propagation
description: How a single scope mutation ripples through watchers on parent, child, and sibling scopes during digest passes.
tags: [angularjs, binding, digest, propagation]
prerequisites:
  - angularjs.two_way_binding
  - angularjs.digest_cycle
related:
  - angularjs.scope_inheritance
  - angularjs.binding_performance
  - angularjs.watchers
resource: https://docs.angularjs.org/guide/scope
timestamp: 2026-01-01
---

## Summary

**Binding propagation** describes how changes traverse the scope tree and watcher graph during `$digest`. A single assignment can trigger listeners on the same scope, parent scopes (via inherited reads), isolate scope `$watch` handlers, and interpolated DOM across the app—all within one or more digest passes.

## Mental model

```
Change: scope.user.name = 'Bob'
  → $apply triggers $digest
  → every registered watcher evaluated (not just subtree)
  → watchers whose expressions depend on user.name fire
  → listeners may mutate other scope properties
  → if any listener dirtys scope → another digest pass (up to TTL)
  → DOM updates batched per listener
```

Propagation paths:

- **Shared object references** — mutation visible to all scopes pointing at same object.
- **Isolate `=` watches** — propagate parent ↔ child both directions on digest.
- **`$watchCollection`** — detects shallow collection changes, fires propagation to list re-render paths.
- **`$broadcast` / `$emit`** — event-based propagation outside dirty checking (orthogonal pattern).

AngularJS does not do localized "component rerender"—digest is global from `$rootScope` (unless `$scope.$digest()` on subtree in advanced cases).

## Example

```javascript
$scope.$watch('cart.total', function(total) {
  $scope.freeShipping = total > 50;
});

$scope.$watch('freeShipping', function(enabled) {
  // Second listener runs in same or next digest pass
  $scope.banner = enabled ? 'Free shipping!' : '';
});

$scope.cart.total = 75; // triggers chain via digest
```

## Common mistakes

- Cascading watcher listeners that each modify watched values → infinite digest.
- Assuming child scope assignment propagates to parent for primitives.
- Relying on watcher order between unrelated expressions (order not guaranteed across scopes).
- Using `$broadcast` for data sync instead of services/shared objects (harder to trace).

## Related concepts

- [Digest Cycle](/concepts/digest-cycle.md) — Engine of propagation.
- [Binding Performance](/concepts/binding-performance.md) — Cost of wide propagation.
- [Two-Way Binding](/concepts/two-way-binding.md) — Source of model/view sync.
