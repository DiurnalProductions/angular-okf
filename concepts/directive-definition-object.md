---
type: Concept
title: Directive Definition Object
description: "Configuration object returned by directive factories defining restrict, template, scope, compile, link, and controller behavior."
tags: [angularjs, directives, ddo, compile]
prerequisites:
  - concepts/scope
related:
  - concepts/compile-vs-link
  - concepts/linking-functions
  - concepts/isolate-scope
  - concepts/transclusion
resource: "https://docs.angularjs.org/guide/directive"
timestamp: 2026-01-01
---

## Summary

The **Directive Definition Object (DDO)** is the return value of a `.directive()` factory. It tells `$compile` how to match elements (`restrict`), what template to use, whether to create isolate scope, how to compile/link, and whether to transclude content.

## Mental model

Directives are **DOM compilation plugins**:

| DDO field | Role |
|-----------|------|
| `restrict` | `'A'`, `'E'`, `'C'`, `'M'` — match attribute, element, class, comment |
| `template` / `templateUrl` | Replace or append HTML |
| `replace` | (deprecated) replace host element |
| `scope` | `true` (new child), `{}` (isolate), `false` (default inherit) |
| `bindToController` | Map isolate bindings to controller instance |
| `controller` / `controllerAs` | Directive-local controller |
| `compile` | Transform DOM before linking; return `{ pre, post }` link fns |
| `link` | Shorthand for post-link only |
| `transclude` | Capture and project original content |
| `priority` / `terminal` | Control compile order |

Component directives in AngularJS 1.5+ are sugar over DDO with `bindToController` and isolate scope defaults.

## Example

```javascript
angular.module('app', [])
  .directive('userCard', function() {
    return {
      restrict: 'E',
      scope: {
        user: '=',
        onSelect: '&'
      },
      templateUrl: 'user-card.html',
      link: function(scope, element, attrs) {
        element.on('click', function() {
          scope.onSelect({ id: scope.user.id });
        });
      }
    };
  });
```

## Common mistakes

- Two-way binding (`=`) primitives without object wrapper—breaks with isolate scope.
- `scope: true` when isolate scope is needed—inheritance leaks.
- DOM manipulation in compile function that should be in link (runs once vs per instance confusion).
- Missing `restrict` leading to accidental attribute+element double matching.

## Related concepts

- [Compile vs Link Phase](/concepts/compile-vs-link.md) — When DDO hooks run.
- [Isolate Scope](/concepts/isolate-scope.md) — DDO `scope: {}` configuration.
- [Transclusion](/concepts/transclusion.md) — DDO `transclude` option.
