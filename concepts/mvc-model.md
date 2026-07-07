---
type: Concept
title: MVC-ish Model
description: "AngularJS organizes SPAs into views (templates), controllers (presentation logic), and services (domain and data access)."
tags: [angularjs, architecture, mvc, spa]
prerequisites:
  - concepts/controller-scope-model
  - concepts/services
related:
  - concepts/separation-of-concerns
  - concepts/service-vs-controller-layer
  - concepts/directive-definition-object
resource: "https://docs.angularjs.org/guide/concepts"
timestamp: 2026-01-01
---

## Summary

AngularJS advertises an **MVC-ish** architecture: **Model** (scope data + services), **View** (templates/DOM), **Controller** (glue between model and view). In practice, **directives** also manipulate the view, and **services** hold durable model logic—so the pattern is better described as **MVVM-with-directives** or **scope-centric MVC**.

## Mental model

```
View (template + directives)
  ↔ two-way binding ↔
Scope (view model state)
  ↔ controller methods ↔
Services (domain logic, API, persistence)
```

Roles:

| Layer | Responsibility |
|-------|----------------|
| **Template** | Declarative HTML, bindings, `ng-*` directives |
| **Controller** | Expose state/actions to template; thin orchestration |
| **Service** | Business rules, `$http`, caching, cross-view state |
| **Directive** | Reusable DOM behavior, widgets, low-level DOM integration |
| **Filter** | Pure formatting transforms in expressions |

AngularJS does **not** enforce layers—discipline is conventional. Digest cycle ties all layers together at runtime.

## Example

```
User clicks Save (View)
  → ng-click calls vm.save() (Controller)
  → vm.save delegates to UserService.update() (Service)
  → $http PUT /api/user (Service)
  → $q resolves → $scope.user updated (Model on scope)
  → digest → template re-renders (View)
```

## Common mistakes

- Treating controllers as the "model"—services own domain truth.
- Putting `$http` in controllers instead of services—hard to test and reuse.
- Directives that embed business rules instead of presentation DOM logic.
- Confusing AngularJS MVC with server-side MVC routing (different concern).

## Related concepts

- [Controller Scope Model](/concepts/controller-scope-model.md) — Controller role.
- [Services](/concepts/services.md) — Model/service layer.
- [Separation of Concerns](/concepts/separation-of-concerns.md) — Refining layer boundaries.
