---
id: angularjs.dirty_checking
type: concept
title: Dirty Checking
description: Compare-old-vs-new strategy AngularJS uses to detect model changes instead of observable or proxy-based reactivity.
tags: [angularjs, dirty-checking, runtime, core]
prerequisites:
  - angularjs.watchers
related:
  - angularjs.digest_cycle
  - angularjs.two_way_binding
  - angularjs.binding_performance
resource: https://docs.angularjs.org/guide/expression
timestamp: 2026-01-01
---

## Summary

**Dirty checking** is AngularJS's change detection mechanism. On each digest pass, the framework re-evaluates watch expressions and compares results to cached "last" values using reference equality (`===`) by default, or deep equality when explicitly requested. If different, the scope is marked "dirty" and listeners fire.

## Mental model

AngularJS does not intercept property assignments. It **polls** state on a schedule defined by digest triggers.

```
Model change → (maybe outside Angular) → $apply/$digest
  → for each watcher: newVal = watchFn(); if newVal !== oldVal → listener()
  → repeat until no changes OR TTL exceeded
```

Key properties:

- **Reference equality** is the default—mutating object internals without replacing the reference may not trigger a simple `$watch('obj', ...)`.
- **`$watchCollection`** checks shallow object/array mutations.
- **Deep watch** (`$watch(..., true)`) uses `angular.equals`—powerful but expensive.
- Third-party libraries (jQuery plugins, raw XHR) that mutate scope outside `$apply` bypass dirty checking until the next digest.

This is fundamentally different from Vue's dependency tracking or React's explicit re-render scheduling.

## Example

```javascript
// Reference watch — mutation invisible
$scope.user = { name: 'Alice' };
$scope.$watch('user', function(n, o) { console.log('changed'); });
$scope.user.name = 'Bob'; // NO listener fire

// Replace reference — detected
$scope.user = { name: 'Bob' }; // listener fires

// Deep watch — detects mutation, costly
$scope.$watch('user', function(n, o) { console.log('deep change'); }, true);
$scope.user.name = 'Carol'; // listener fires
```

## Common mistakes

- Expecting AngularJS to detect in-place array/object mutations with default `$watch` on the object reference.
- Using deep watches on large structures in hot paths (lists with hundreds of items).
- Believing `Object.defineProperty` or ES6 Proxies integrate automatically—they do not unless wrapped in `$apply`.
- Confusing dirty checking with immutable data patterns; AngularJS 1.x was designed before immutability-first workflows.

## Related concepts

- [Watchers](/concepts/watchers.md) — The functions that perform dirty checks.
- [Digest Cycle](/concepts/digest-cycle.md) — Repeats dirty checking until stable.
- [Binding Performance](/concepts/binding-performance.md) — Cost implications of equality strategy choices.
