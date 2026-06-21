---
id: angularjs.scope_inheritance
type: concept
title: Scope Inheritance
description: Child scopes prototypally inherit from parent scopes; property reads traverse the chain, writes may shadow on the child.
tags: [angularjs, scope, inheritance, prototypal]
prerequisites:
  - angularjs.scope
  - angularjs.controller_scope_model
related:
  - angularjs.isolate_scope
  - angularjs.binding_propagation
  - angularjs.ng_model
resource: https://docs.angularjs.org/guide/scope#scope-hierarchy
timestamp: 2026-01-01
---

## Summary

**Scope inheritance** in AngularJS uses JavaScript prototypal inheritance. `$rootScope` is the ancestor; each `ng-controller`, `ng-if`, `ng-repeat`, and many directives create a **child scope** whose prototype links to the parent. Reads walk up the chain; writes to primitive properties on a child create **shadowing** copies on the child.

## Mental model

```
$rootScope
  └── childScope (proto → $rootScope)
        └── grandchildScope (proto → childScope)
```

| Operation | Behavior |
|-----------|----------|
| Read `name` | Walk prototype chain until found |
| Write `name = 'x'` on child | Sets property on child (may shadow parent) |
| Write to object property `user.name` | Mutates shared object visible to all scopes referencing it |

The **"dot rule"**: bind to object properties (`user.name`) not bare primitives (`name`) in nested scopes to avoid shadowing bugs—especially with `ng-model` in child directives.

`ng-repeat` creates a **new scope per iteration**—critical for list editing patterns.

## Example

```html
<div ng-controller="ParentCtrl">
  <input ng-model="title"> <!-- binds to parent scope -->
  <div ng-controller="ChildCtrl">
    <input ng-model="title"> <!-- may shadow child scope.title -->
    <input ng-model="model.title"> <!-- mutates shared object -->
  </div>
</div>
```

```javascript
// ParentCtrl: $scope.model = { title: 'Shared' };
// ChildCtrl: assigning $scope.title shadows; $scope.model.title does not
```

## Common mistakes

- `ng-model="item"` inside `ng-repeat` modifying wrong scope level without `track by`.
- Expecting sibling scopes to share primitive assignments.
- Debugging binding issues without checking scope hierarchy in Batarang/devtools.
- Confusing prototypal scope inheritance with isolate scope (explicit one-way/two-way bindings).

## Related concepts

- [Scope](/concepts/scope.md) — Base scope behavior.
- [Isolate Scope](/concepts/isolate-scope.md) — Breaks inheritance for directive encapsulation.
- [ng-model](/concepts/ng-model.md) — Common source of inheritance bugs.
