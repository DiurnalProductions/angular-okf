---
type: Concept
title: Watcher Explosion
description: "Uncontrolled growth of $watch registrations from bindings, ng-repeat, and directives causing digest slowdown."
tags: [angularjs, performance, watchers, digest]
prerequisites:
  - concepts/watchers
  - concepts/binding-performance
related:
  - concepts/digest-cost
  - concepts/optimization-strategies
  - concepts/ng-model
resource: "https://docs.angularjs.org/guide/scope"
timestamp: 2026-01-01
---

## Summary

**Watcher explosion** occurs when an AngularJS application registers thousands of watchers—via `{{ }}` bindings, `ng-repeat` rows, `ng-model`, isolate scope `$watch` handlers, and custom `$watch` calls—causing each digest to evaluate every watcher. UI jank and `$rootScope:infdig` errors often trace back to watcher count, not raw DOM size.

## Mental model

```
Watcher count ≈
  interpolations
  + ng-model bindings
  + directive isolate '=' watches
  + custom $watch / $watchCollection
  × ng-repeat iterations
```

Example: 500-row `ng-repeat` with 10 bindings each → 5000+ watchers **before** helper directives.

Symptoms:

- Input lag on keystrokes (full digest per `ng-model` change).
- Slow click handlers (digest before handler completes perception).
- DevTools: `$rootScope.$$watchersCount` (undocumented but widely used).

Root cause is architectural: **global dirty checking** scales with watcher count, not component boundaries.

## Example

```html
<!-- 1000 watchers from 100 items × 10 bindings -->
<tr ng-repeat="item in items">
  <td>{{ item.a }}</td>
  <td>{{ item.b }}</td>
  <!-- ... 8 more columns ... -->
</tr>
```

Mitigation starts with measurement, then reducing bindings or using one-time binding and `track by`.

## Common mistakes

- Adding third-party widgets that register hidden `$watch` per row.
- Using `ng-show` on massive lists instead of filtering source data.
- Deep `$watch` on each row item in a directive.
- Ignoring watcher count until production performance fails.

## Related concepts

- [Digest Cost](/concepts/digest-cost.md) — Computational impact.
- [Optimization Strategies](/concepts/optimization-strategies.md) — Reduction techniques.
- [Binding Performance](/concepts/binding-performance.md) — Expression evaluation cost.
