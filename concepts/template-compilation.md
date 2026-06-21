---
id: angularjs.template_compilation
type: concept
title: Template Compilation
description: $compile traverses DOM and directives, transforming templates into linking functions that bind scope to elements.
tags: [angularjs, compile, templates, directives]
prerequisites:
  - angularjs.expressions
  - angularjs.angular_module
related:
  - angularjs.compile_vs_link
  - angularjs.dom_linking
  - angularjs.directive_definition_object
resource: https://docs.angularjs.org/guide/compilation
timestamp: 2026-01-01
---

## Summary

**Template compilation** is the process by which `$compile` walks a DOM tree (or HTML string), matches directive definitions, executes compile functions, and returns a **linking function** that associates a scope with the compiled DOM.

## Mental model

Compilation is a **two-phase pipeline**:

```
Template (HTML + directives)
  → COMPILE phase (top-down, once per template structure)
      → directive compile fns transform DOM
      → child directives compiled
  → LINK phase (bottom-up, per scope instance)
      → directive link fns attach listeners & watchers
```

Key points:

- **Structural directives** (`ng-if`, `ng-repeat`) clone and re-link DOM subtrees.
- **Template URLs** fetched asynchronously still end in `$compile` once loaded.
- `$compileProvider` can whitelist/blacklist URLs and debug info.
- Same template structure can be linked to many scope instances (e.g., `ng-repeat`).

Compilation connects the declarative HTML world to the imperative scope/DOM world.

## Example

```javascript
angular.module('app', [])
  .directive('myPanel', function($compile) {
    return {
      restrict: 'E',
      link: function(scope, element, attrs) {
        // Manual compile of dynamic content
        var tpl = angular.element('<span>{{ dynamic }}</span>');
        element.append(tpl);
        $compile(tpl)(scope);
      }
    };
  });
```

```html
<div ng-controller="MainCtrl">
  <my-panel></my-panel>
</div>
```

## Common mistakes

- Calling `$compile` inside compile functions without understanding double-compilation risks.
- Manually `$compile`ing large subtrees on every digest trigger.
- Disabling debug info in production without understanding impact on `$compile` metadata tools.
- Using string concatenation for templates instead of `templateUrl` or `$templateCache`.

## Related concepts

- [Compile vs Link Phase](/concepts/compile-vs-link.md) — Phase ordering details.
- [DOM Linking](/concepts/dom-linking.md) — Applying link functions.
- [Directive Definition Object](/concepts/directive-definition-object.md) — Units `$compile` processes.
