---
type: Concept
title: Interpolation
description: Double-mustache bindings compile into watcher-backed text node updates synchronized during digest.
tags: [angularjs, interpolation, templates, binding]
prerequisites:
  - concepts/expressions
related:
  - concepts/template-compilation
  - concepts/two-way-binding
  - concepts/optimization-strategies
resource: "https://docs.angularjs.org/api/ng/service/$interpolate"
timestamp: 2026-01-01
---

## Summary

**Interpolation** (`{{ expression }}`) embeds model data into HTML text nodes. `$interpolate` parses mixed static text and bindings into a `$watch`-backed function that updates DOM text when expression values change during digest.

## Mental model

```
"Hello, {{ name }}!"
  → compile: watchFn returns concatenated string
  → link: register $watch → listener sets textContent
  → digest: if string changed → DOM updated
```

Properties:

- **One-way display binding**—interpolation does not write back to model.
- Multiple `{{ }}` in one text node are supported.
- `ng-non-bindable` excludes subtrees from compilation.
- One-time binding prefix `{{ ::expr }}` stops watching after first non-undefined value (Angular 1.3+).

Interpolation runs through `$sanitize` in ngSanitize module contexts for untrusted HTML—but interpolation itself is text, not HTML binding (`ng-bind-html` is separate).

## Example

```html
<h1>{{ title | uppercase }}</h1>
<p>{{ user.name }} has {{ cart.items.length }} items.</p>
<p>{{ ::staticLabel }}</p> <!-- one-time binding -->
```

```javascript
// $interpolate service usage
var fn = $interpolate('Hello, {{name}}!');
var html = fn({ name: 'World' }); // "Hello, World!"
```

## Common mistakes

- Using interpolation for dynamic HTML (`{{ htmlContent }}` shows escaped text, does not render HTML).
- Heavy function calls or filters inside `{{ }}` causing digest slowdown.
- Binding untrusted user content without sanitization strategies.
- Forgetting one-time binding for static configuration labels in large `ng-repeat` lists.

## Related concepts

- [Expressions](/concepts/expressions.md) — What interpolation evaluates.
- [Optimization Strategies](/concepts/optimization-strategies.md) — One-time binding (`::`).
- [Digest Cycle](/concepts/digest-cycle.md) — When text updates occur.
