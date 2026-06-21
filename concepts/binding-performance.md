---
id: angularjs.binding_performance
type: concept
title: Binding Performance
description: Performance cost of watch strategies, expression re-evaluation, and binding patterns in large templates.
tags: [angularjs, performance, binding, watchers]
prerequisites:
  - angularjs.binding_propagation
  - angularjs.watchers
related:
  - angularjs.watcher_explosion
  - angularjs.optimization_strategies
  - angularjs.dirty_checking
resource: https://docs.angularjs.org/guide/scope#scope-life-cycle
timestamp: 2026-01-01
---

## Summary

**Binding performance** in AngularJS is dominated by watcher count and expression evaluation cost during each digest. Deep watches, heavy filters in templates, large `ng-repeat` lists without `track by`, and unnecessary two-way bindings amplify digest time linearly or worse.

## Mental model

Cost factors:

| Pattern | Cost driver |
|---------|-------------|
| `{{ fn() }}` in template | Function runs every digest × binding count |
| `$watch(obj, true)` | Deep equality scan each digest |
| `ng-repeat` without `track by` | DOM destroy/recreate on collection identity change |
| Many `=` isolate bindings | Extra `$watch` per binding per directive instance |
| Global `$rootScope` watches | Never destroyed until app teardown |

Optimization mindset:

- Reduce watchers (one-time binding, manual `$render`).
- Use `$watchCollection` instead of deep `$watch` when sufficient.
- Move stable data out of digest path (cache in scope on event, not expression).
- Profile with `angular.element(document).injector().get('$rootScope').$$watchersCount` (dev).

## Example

```html
<!-- Expensive: filter runs every digest for every row -->
<li ng-repeat="item in items | orderBy:'name'">{{ item.name }}</li>

<!-- Better: pre-sort in controller on items change -->
<li ng-repeat="item in sortedItems track by item.id">{{ item.name }}</li>

<!-- One-time binding for static fields -->
<span>{{ ::item.id }}</span>
```

```javascript
$scope.$watchCollection('items', function(items) {
  $scope.sortedItems = items.slice().sort(byName);
});
```

## Common mistakes

- Deep watching entire API response objects.
- Using `ng-show`/`ng-hide` on hundreds of elements instead of filtering data.
- Creating new object/array literals in templates (`ng-class="{'a': cond}"` is OK; `ng-repeat="x in getList()"` is not).
- Ignoring `$watch` deregistration when conditionally creating watchers in loops.

## Related concepts

- [Watcher Explosion](/concepts/watcher-explosion.md) — How watcher count grows.
- [Optimization Strategies](/concepts/optimization-strategies.md) — Mitigation techniques.
- [Dirty Checking](/concepts/dirty-checking.md) — Equality strategy costs.
