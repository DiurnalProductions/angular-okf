---
id: angularjs.expressions
type: concept
title: Expressions
description: JavaScript-like snippets evaluated against scope during interpolation, binding, and $watch registration.
tags: [angularjs, expressions, templates, scope]
prerequisites:
  - angularjs.scope
related:
  - angularjs.interpolation
  - angularjs.template_compilation
  - angularjs.two_way_binding
resource: https://docs.angularjs.org/guide/expression
timestamp: 2026-01-01
---

## Summary

**Expressions** in AngularJS are evaluated by the `$parse` service against a scope context. They appear in templates (`{{ a + b }}`), directive attributes (`ng-show="isVisible"`), and `$watch` registrations. Expressions are **limited JavaScript**—no statements, no `var`, no control flow beyond ternary.

## Mental model

Expressions are **pure-ish getters** from AngularJS's perspective:

- Evaluated repeatedly during digest (not cached across digests unless one-time binding).
- Sandboxed subset: no `window`, no assignments (except through `ng-model` pipeline), no `function` declarations.
- `$parse(expr)(scope, locals)` is the internal API.
- Filters use pipe syntax: `{{ price | currency }}` → `$filter('currency')(price)`.

Expression evaluation errors throw `$parse:lex` or `$parse:syntax` at compile time, or `$digest` errors at runtime if accessors throw.

Because expressions re-run on digest, **side effects in expressions are forbidden** by convention and cause unstable digests.

## Example

```html
<p>{{ user.firstName + ' ' + user.lastName }}</p>
<div ng-show="items.length > 0">Has items</div>
<input ng-model="search" ng-model-options="{ debounce: 300 }">
```

```javascript
$scope.$watch('items.length > 0', function(hasItems) {
  console.log('Has items:', hasItems);
});
```

## Common mistakes

- Calling functions with side effects in templates (`{{ computeTotal() }}`)—runs every digest.
- Using complex logic in expressions instead of computed scope properties or filters.
- Assuming expressions have access to full JavaScript (no `if` statements—use ternary or `ng-if`).
- Arrow functions in older AngularJS versions with different `this` binding in directives.

## Related concepts

- [Interpolation](/concepts/interpolation.md) — Primary expression display mechanism.
- [Template Compilation](/concepts/template-compilation.md) — Parses expressions into watch functions.
- [Watchers](/concepts/watchers.md) — Re-evaluate expressions each digest.
