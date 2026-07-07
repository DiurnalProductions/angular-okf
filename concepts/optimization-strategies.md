---
type: Concept
title: Optimization Strategies
description: "Techniques to reduce digest work including one-time binding, track by, bind once directives, and manual $digest control."
tags: [angularjs, performance, optimization, binding]
prerequisites:
  - concepts/digest-cost
related:
  - concepts/watcher-explosion
  - concepts/binding-performance
  - concepts/interpolation
resource: "https://docs.angularjs.org/guide/one-time-bindings"
timestamp: 2026-01-01
---

## Summary

**Optimization strategies** in AngularJS target watcher count, digest frequency, and expression cost. Core techniques include **one-time binding (`::`)**, **`track by` in `ng-repeat`**, **`$watchCollection` over deep watch**, **`ng-model-options` debouncing**, **`$applyAsync` batching**, and **`bind-once` third-party directives** for legacy versions.

## Mental model

| Strategy | Effect |
|----------|--------|
| `{{ ::expr }}` | Remove watcher after first stable value |
| `ng-repeat="item in items track by item.id"` | Reuse DOM nodes, fewer relinks |
| `$watchCollection` | Cheaper than deep `$watch` for arrays/objects |
| `ng-model-options="{ debounce: N }"` | Fewer digests on typing |
| `$httpProvider.useApplyAsync(true)` | Batch multiple responses into one digest |
| Manual `$interval` + single `$digest` | Polling without per-tick full binding (advanced) |
| `limitTo` filter + pagination | Reduce bound row count |
| `$onDestroy` cleanup | Prevent leak-driven watcher growth |

Architectural strategies:

- Move list rendering to **virtualization** (third-party) for huge datasets.
- **React/Vue islands** (late migration pattern)—outside pure AngularJS but common in legacy transitions.

## Example

```html
<ul>
  <li ng-repeat="user in users track by user.id">
    {{ ::user.id }} — {{ user.name }}
  </li>
</ul>

<input ng-model="search" ng-model-options="{ debounce: 300, updateOn: 'default blur' }">
```

```javascript
.config(function($httpProvider) {
  $httpProvider.useApplyAsync(true);
})
```

## Common mistakes

- One-time binding on values that arrive asynchronously before data load completes (shows empty until manual refresh—use `$watch` once then deregister pattern if needed).
- `track by $index` when list reorder mutates identity incorrectly.
- Debouncing model without updating validation UX expectations.
- Micro-optimizing expressions while ignoring 10× watcher count problem.

## Related concepts

- [Digest Cost](/concepts/digest-cost.md) — What optimizations reduce.
- [Watcher Explosion](/concepts/watcher-explosion.md) — Primary target.
- [Interpolation](/concepts/interpolation.md) — One-time binding syntax.
