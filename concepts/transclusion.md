---
type: Concept
title: Transclusion
description: "Captures original directive content and projects it into the directive template, preserving scope context options."
tags: [angularjs, directives, transclusion, dom]
prerequisites:
  - concepts/directive-definition-object
  - concepts/linking-functions
related:
  - concepts/isolate-scope
  - concepts/compile-vs-link
  - concepts/template-compilation
resource: "https://docs.angularjs.org/guide/transclusion"
timestamp: 2026-01-01
---

## Summary

**Transclusion** preserves the DOM content placed inside a directive's host element and re-inserts it elsewhere in the directive's template. Enable with `transclude: true` (or an object map for multi-slot). The link function receives `transcludeFn` to clone and attach content.

## Mental model

```html
<panel title="Settings">
  <p>This inner content is transcluded.</p>
</panel>
```

```
<panel> compiles:
  1. Extract inner <p> as transclude fragment (before replacement)
  2. Apply directive template (with ng-transclude slot or manual transcludeFn)
  3. Link transcluded DOM — default: sibling scope; 'element' transclusion: uses directive scope
```

Types:

- **Content transclusion** — move child nodes into template slot.
- **`ng-transclude` directive** — marks insertion point in template.
- **`transclude: 'element'`** — for structural directives (`ng-if` pattern)—transclude entire element.

Transcluded content is compiled, but linking timing depends on when `transcludeFn` is invoked.

## Example

```javascript
.directive('panel', function() {
  return {
    restrict: 'E',
    transclude: true,
    scope: { title: '@' },
    template:
      '<div class="panel">' +
        '<h3>{{ title }}</h3>' +
        '<div class="body" ng-transclude></div>' +
      '</div>'
  };
});
```

```javascript
// Manual transclusion in link
link: function(scope, element, attrs, ctrl, transclude) {
  transclude(function(clone) {
    element.find('.body').append(clone);
  });
}
```

## Common mistakes

- Expecting transcluded content to inherit isolate scope bindings automatically (uses sibling scope by default).
- Calling `transcludeFn` multiple times without cloning semantics understanding.
- Combining `replace: true` with transclusion in legacy directives (deprecated patterns).
- Missing `ng-transclude` slot in template—content disappears.

## Related concepts

- [Linking Functions](/concepts/linking-functions.md) — `transcludeFn` parameter.
- [Directive Definition Object](/concepts/directive-definition-object.md) — `transclude` option.
- [Template Compilation](/concepts/template-compilation.md) — When content is extracted.
