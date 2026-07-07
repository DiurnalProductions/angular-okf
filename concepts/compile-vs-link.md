---
type: Concept
title: Compile vs Link Phase
description: "Compile transforms template DOM once; link binds a specific scope instance to each compiled node."
tags: [angularjs, compile, link, directives]
prerequisites:
  - concepts/template-compilation
related:
  - concepts/linking-functions
  - concepts/directive-definition-object
  - concepts/dom-linking
resource: "https://docs.angularjs.org/guide/compilation"
timestamp: 2026-01-01
---

## Summary

AngularJS directive processing splits into **compile** and **link** phases. **Compile** runs once when `$compile` first traverses a template, allowing structural DOM changes shared across all instances. **Link** runs when a concrete scope is attached—once per scope/DOM pairing—registering watchers and event handlers.

## Mental model

```
COMPILE (shared, template-level)
  parent directive compile
    → modify DOM structure
    → compile children
  child directive compile
    ...

LINK (per instance, scope-level)
  child post-link
  child pre-link
  parent post-link
  parent pre-link
```

Order details:

- **Compile**: parent before child (depth-first down).
- **Pre-link**: parent before child (top-down).
- **Post-link**: child before parent (bottom-up)—most common hook site.
- `link` property in DDO = **post-link** function.

Use **compile** when:

- Cloning the same DOM structure for `ng-repeat`-like reuse.
- Adding identical event delegation without per-instance setup.

Use **link** when:

- Registering `$watch`, `ngModel` controllers, or DOM listeners needing scope.

## Example

```javascript
.directive('repeatable', function() {
  return {
    restrict: 'A',
    compile: function(tElement, tAttrs) {
      // Runs once: add shared CSS class to template DOM
      tElement.addClass('repeatable-template');
      return {
        pre: function(scope, iElement, iAttrs) {
          // Rare: needs child DOM before child links
        },
        post: function(scope, iElement, iAttrs) {
          // Per instance: register watchers
          scope.$watch('value', function(v) {
            iElement.text(v);
          });
        }
      };
    }
  };
})
```

## Common mistakes

- Scope-dependent logic in compile function (scope not available yet).
- Returning a link function from compile AND specifying `link` property (both run—confusing).
- Expensive work in link that could run once in compile for cloned templates.
- Assuming compile runs again when scope data changes—it does not; link watchers handle updates.

## Related concepts

- [Linking Functions](/concepts/linking-functions.md) — pre/post link details.
- [Template Compilation](/concepts/template-compilation.md) — `$compile` entry point.
- [DOM Linking](/concepts/dom-linking.md) — End-to-end linking flow.
