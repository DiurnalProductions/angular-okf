---
id: angularjs.isolate_scope
type: concept
title: Isolate Scope
description: Directive-local scope with explicit one-way, two-way, and expression bindings decoupled from parent inheritance.
tags: [angularjs, directives, isolate-scope, binding]
prerequisites:
  - angularjs.scope
  - angularjs.directive_definition_object
related:
  - angularjs.scope_inheritance
  - angularjs.two_way_binding
  - angularjs.transclusion
resource: https://docs.angularjs.org/guide/directive#isolating-the-scope-of-a-directive
timestamp: 2026-01-01
---

## Summary

An **isolate scope** (`scope: {}` in a DDO) creates a directive-specific scope that does **not** prototypally inherit from the parent scope. Data flow is explicit through binding definitions: `@` (attribute string), `=` (two-way), `<` (one-way, 1.5+), `&` (expression wrapper).

## Mental model

```
Parent scope                    Isolate scope
  user: { name: 'A' }    =user   user → two-way link to parent.user
  label: "Title"         @label  label → string from attribute
  onSave()               &save   save({ data }) → invokes parent fn
```

Binding prefixes:

| Symbol | Direction | Mechanism |
|--------|-----------|-----------|
| `@` | Parent → directive (string) | `$observe` attribute |
| `=` | Two-way | `$watch` + assign on change |
| `<` | One-way | `$watch` parent, no write back |
| `&` | Callback | `$parse` wrapper creating function |

Isolate scope enables **reusable encapsulated widgets** without leaking `$scope` pollution.

## Example

```javascript
.directive('datePicker', function() {
  return {
    restrict: 'E',
    scope: {
      selectedDate: '=',
      format: '@',
      onChange: '&'
    },
    template: '<input type="date" ng-change="onChange({ date: selectedDate })">',
    link: function(scope, element, attrs) {
      // scope.selectedDate syncs with parent via '='
    }
  };
});
```

```html
<date-picker selected-date="vm.date" format="yyyy-MM-dd"
             on-change="vm.handleDateChange(date)"></date-picker>
```

## Common mistakes

- Two-way binding (`=`) to primitive values without dot notation object wrapper.
- Using `@` for interpolated objects expecting live updates (strings only unless `$observe` rebinds).
- Mixing isolate scope with template accessing parent scope properties directly.
- `&` expression invocation wrong locals (`&save({ value: x })` not `&save(x)`).

## Related concepts

- [Scope Inheritance](/concepts/scope-inheritance.md) — What isolate scope replaces.
- [Two-Way Binding](/concepts/two-way-binding.md) — `=` binding mechanics.
- [Directive Definition Object](/concepts/directive-definition-object.md) — Where isolate scope is declared.
