---
type: Concept
title: Two-Way Binding
description: Synchronization between model and view through paired watchers that propagate changes in both directions during digest.
tags: [angularjs, binding, two-way, watchers]
prerequisites:
  - concepts/watchers
  - concepts/digest-cycle
related:
  - concepts/ng-model
  - concepts/binding-propagation
  - concepts/isolate-scope
resource: "https://docs.angularjs.org/tutorial/step_04"
timestamp: 2026-01-01
---

## Summary

**Two-way binding** keeps a scope property and its DOM representation in sync. When the model changes, the view updates on digest. When the user interacts with the view (input, click handlers updating model), `$apply` triggers digest to propagate model changes elsewhere.

## Mental model

Two-way binding is **not magic observable sync**—it is **dual watcher wiring**:

```
Model → View: $watch model → update DOM ($render, interpolation)
View → Model: DOM event → $setViewValue / ng-model listener → model assignment → $apply → digest
```

Mechanisms:

- **`ng-model`** — canonical two-way binding for form controls.
- **Isolate `=` bindings** — parent ↔ directive property sync.
- **NOT interpolation** — `{{ }}` is one-way display only.

All paths converge on the digest cycle. Without digest, view and model diverge.

## Example

```html
<input ng-model="user.email">
<p>Email: {{ user.email }}</p>
<button ng-click="user.email = 'reset@example.com'">Reset</button>
```

Changing the input updates `user.email` and the paragraph on digest. Clicking Reset updates both input and paragraph.

```javascript
// Isolate scope two-way
scope: { item: '=' }
// Parent: <widget item="selectedItem">
```

## Common mistakes

- Confusing one-way interpolation with two-way binding.
- Two-way binding large object graphs causing expensive deep equality checks.
- Binding to primitives in nested scopes without dot notation (shadowing breaks sync).
- Expecting instant cross-browser consistency without understanding `ng-model` parsers/formatters.

## Related concepts

- [ng-model](/concepts/ng-model.md) — Primary two-way directive.
- [Binding Propagation](/concepts/binding-propagation.md) — Ripple effects during digest.
- [Watchers](/concepts/watchers.md) — Underlying mechanism.
