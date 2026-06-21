---
id: angularjs.digest_cost
type: concept
title: Digest Cost
description: Computational cost of $digest grows with watcher count and number of stabilization passes until TTL limit.
tags: [angularjs, performance, digest, runtime]
prerequisites:
  - angularjs.digest_cycle
  - angularjs.watcher_explosion
related:
  - angularjs.optimization_strategies
  - angularjs.binding_performance
  - angularjs.dirty_checking
resource: https://docs.angularjs.org/api/ng/type/$rootScope.Scope
timestamp: 2026-01-01
---

## Summary

**Digest cost** is the CPU time spent running `$rootScope.$digest()` and its repeated passes until the watcher graph stabilizes or hits the **TTL** (default 10 iterations). Cost is roughly **O(watchers × digest passes × expression complexity)** per triggering event.

## Mental model

```
Single user keystroke (ng-model)
  → $apply
  → digest pass 1: evaluate ALL watchers
  → any listener dirtys model?
      yes → digest pass 2: evaluate ALL watchers again
      … repeat up to TTL (10)
  → $rootScope:infdig if still dirty at TTL
```

Cost amplifiers:

- **Many watchers** — linear scan every pass.
- **Multiple passes** — cascading `$watch` listeners mutating watched values.
- **Expensive watch expressions** — function calls, filters, deep equality.
- **Large scope tree** — child scopes still participate in global digest from root.

Profiling tools: Batarang, `$performance` patterns, manual timing around `$digest`, browser Performance tab.

## Example

```javascript
// Anti-pattern: cascading watchers
$scope.$watch('a', function() { $scope.b = compute($scope.a); });
$scope.$watch('b', function() { $scope.c = transform($scope.b); });
$scope.$watch('c', function() { $scope.a = tweak($scope.c); }); // infinite loop risk

// Better: compute in single handler or service method
$scope.$watch('a', function(a) {
  $scope.c = transform(compute(a));
});
```

## Common mistakes

- Assuming `$scope.$digest()` on subtree avoids global cost—it limits evaluation scope in advanced use but typical apps digest from root.
- Chaining filters in templates on hot paths.
- Using two-way binding everywhere when one-way display suffices.
- Not fixing `$rootScope:infdig` by restructuring watcher dependencies.

## Related concepts

- [Digest Cycle](/concepts/digest-cycle.md) — What is being measured.
- [Watcher Explosion](/concepts/watcher-explosion.md) — Primary multiplier.
- [Optimization Strategies](/concepts/optimization-strategies.md) — How to reduce cost.
