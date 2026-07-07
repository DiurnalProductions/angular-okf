---
type: Concept
title: DOM Linking
description: "Applying compiled linking functions to live DOM nodes, associating scopes and triggering initial digest synchronization."
tags: [angularjs, dom, compile, link]
prerequisites:
  - concepts/template-compilation
  - concepts/linking-functions
related:
  - concepts/compile-vs-link
  - concepts/directive-definition-object
  - concepts/view-swapping
resource: "https://docs.angularjs.org/api/ng/service/$compile"
timestamp: 2026-01-01
---

## Summary

**DOM linking** is the final step where `$compile`'s output—a linking function—is invoked with a scope to connect behavior to live DOM. Bootstrap, route changes, `ng-if` toggles, and `ng-repeat` iterations all create linking scenarios where compiled templates meet runtime scopes.

## Mental model

```
$compile(template)(scope, cloneAttachFn?)
  → traverse linked DOM
  → run pre/post link functions
  → register watchers & listeners
  → (optional) initial $digest from caller
```

Scenarios:

- **App bootstrap** — `$compile($rootElement)($rootScope)`.
- **Manual linking** — `$compile(htmlString)(childScope)` in directives.
- **Clone linking** — `ng-repeat` links same compiled template to many scopes with cloned DOM nodes.
- **Destruction** — removing DOM triggers `$destroy` on scope, unlinking watchers.

Link-once-compile-many is the performance win of wrepeat-style patterns and structural directives.

## Example

```javascript
angular.module('app', [])
  .directive('dynamicTemplate', function($compile) {
    return {
      scope: { templateName: '=' },
      link: function(scope, element) {
        scope.$watch('templateName', function(name) {
          element.empty();
          var tpl = angular.element('<div ng-include="\'' + name + '\'"></div>');
          element.append(tpl);
          $compile(tpl)(scope);
        });
      }
    };
  });
```

## Common mistakes

- Linking before async template URL resolves—results in empty DOM.
- Forgetting to destroy child scopes when replacing manually compiled content (memory leaks).
- Double compilation (marking already compiled DOM with `$compile` again).
- Not using `$templateCache` for repeated template fetches in link functions.

## Related concepts

- [Template Compilation](/concepts/template-compilation.md) — Produces linking functions.
- [Linking Functions](/concepts/linking-functions.md) — Per-node behavior attachment.
- [View Swapping](/concepts/view-swapping.md) — Route-driven re-linking.
