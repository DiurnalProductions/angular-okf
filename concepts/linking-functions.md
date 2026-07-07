---
type: Concept
title: Linking Functions
description: "Pre and post link functions connect a scope instance to compiled DOM, registering watchers and event handlers."
tags: [angularjs, link, directives, dom]
prerequisites:
  - concepts/compile-vs-link
  - concepts/directive-definition-object
related:
  - concepts/dom-linking
  - concepts/transclusion
  - concepts/ng-model
resource: "https://docs.angularjs.org/api/ng/service/$compile"
timestamp: 2026-01-01
---

## Summary

**Linking functions** bind a specific scope to a compiled DOM subtree. The DDO `link` property defines a post-link function. A compile function can return `{ pre: fn, post: fn }` for finer control. Link functions receive `(scope, element, attrs, controllers, transcludeFn)`.

## Mental model

Link is where **behavior meets DOM**:

- Register `$watch` and `$observe` on attributes.
- Attach jQuery/jqLite event handlers (prefer `ngEvent` directives when possible).
- Interact with **require**d controller APIs (`ngModel`, sibling directives).
- Call `transcludeFn(scope, cloneAttachFn)` to insert transcluded content.
- **Clean up** on `scope.$on('$destroy', ...)` to prevent leaks.

The fifth argument `transcludeFn` is only available when `transclude: true` on the DDO.

Link functions should be idempotent per instance—each scope+element pair gets one link invocation.

## Example

```javascript
.directive('autoFocus', function($timeout) {
  return {
    restrict: 'A',
    link: function(scope, element) {
      $timeout(function() { element[0].focus(); });
    }
  };
})

.directive('myTabs', function() {
  return {
    restrict: 'E',
    transclude: true,
    scope: {},
    controller: function() { this.select = function(i) { /* ... */ }; },
    link: function(scope, element, attrs, ctrl, transclude) {
      transclude(scope, function(clone) {
        element.find('.content').append(clone);
      });
    }
  };
});
```

## Common mistakes

- Forgetting `$destroy` cleanup for manual `element.on()` listeners.
- Calling `$apply` inside every link without checking if already in digest.
- Using `attrs` observation without understanding `$observe` triggers on interpolation changes only.
- Heavy DOM queries in link on large lists—cache references.

## Related concepts

- [Compile vs Link Phase](/concepts/compile-vs-link.md) — When link runs relative to compile.
- [Transclusion](/concepts/transclusion.md) — `transcludeFn` usage.
- [DOM Linking](/concepts/dom-linking.md) — Full linking pipeline.
