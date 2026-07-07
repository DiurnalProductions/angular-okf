---
type: Concept
title: Scope
description: "Hierarchical context object that holds model data, hosts watchers, and participates in digest propagation."
tags: [angularjs, scope, runtime, core]
related:
  - concepts/watchers
  - concepts/expressions
  - concepts/controller-scope-model
  - concepts/scope-inheritance
resource: "https://docs.angularjs.org/guide/scope"
timestamp: 2026-01-01
---

## Summary

A **scope** in AngularJS is a JavaScript object that acts as the execution context for templates and controllers. Scopes form a tree rooted at `$rootScope`. They store model properties, register watchers, and emit/broadcast events. The digest cycle traverses this tree to synchronize scope state with the DOM.

## Mental model

Think of scope as a **reactive clipboard** attached to a subtree of the DOM—not a class instance or a component state container.

- Every template expression reads from a scope (or its prototype chain).
- Controllers **add behavior and data** to a scope; they do not replace the digest machinery.
- Child scopes **prototypally inherit** from parent scopes unless a directive creates an isolate scope.
- Scopes are **destroyed** when their DOM subtree is removed (`$destroy` event), which tears down watchers.

AngularJS is not event-driven reactivity (no Observables). Scopes are passive data holders until the digest cycle asks each watcher whether anything changed.

## Example

```javascript
angular.module('app', [])
  .controller('MainCtrl', function($scope) {
    $scope.greeting = 'Hello';
    $scope.setGreeting = function(name) {
      $scope.greeting = 'Hello, ' + name;
    };
  });
```

```html
<div ng-controller="MainCtrl">
  <p>{{ greeting }}</p>
  <button ng-click="setGreeting('World')">Greet</button>
</div>
```

## Common mistakes

- Mutating scope outside Angular-aware callbacks without `$apply` or `$applyAsync`, leaving the DOM stale.
- Assuming scope inheritance is class-based; it is **prototypal**—child assignment can shadow parent properties unexpectedly.
- Storing large object graphs directly on `$rootScope`, causing every digest to traverse unnecessary watchers.
- Using `$scope.$parent` chains instead of explicit data flow through isolate scope bindings in directives.

## Related concepts

- [Watchers](/concepts/watchers.md) — Registered on scopes to detect changes.
- [Digest Cycle](/concepts/digest-cycle.md) — Traverses scopes to run watchers.
- [Scope Inheritance](/concepts/scope-inheritance.md) — Prototypal parent/child scope chain.
- [Controller Scope Model](/concepts/controller-scope-model.md) — How controllers bind to scopes.
