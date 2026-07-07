---
okf_version: "0.1"
id: angular-okf
name: AngularJS (1.x) Knowledge Pack
version: "0.1"
description: "OKF knowledge base for AngularJS 1.x (digest cycle, scopes, directives, dependency injection, services, controllers)"
tags: [angular, javascript, spa, legacy, frontend]
---

# AngularJS (1.x) Knowledge Pack

AngularJS is a declarative SPA framework built on scopes, dirty checking, and a digest cycle that synchronizes model and DOM.

This bundle is an OKF v0.1 knowledge graph for AngularJS 1.x mental models and runtime behavior. It is documentation-only — not a sample application or modern Angular (2+) material.

## Start Here

If you are new to AngularJS, begin with the runtime model. Everything else — binding, directives, services, routing — flows from how scopes, watchers, and the digest cycle keep the DOM in sync with JavaScript state.

**Recommended learning progression:**

1. [Scope](/concepts/scope.md) → [Watchers](/concepts/watchers.md) → [Dirty Checking](/concepts/dirty-checking.md) → [Digest Cycle](/concepts/digest-cycle.md)
2. [Expressions](/concepts/expressions.md) → [Interpolation](/concepts/interpolation.md) → [Template Compilation](/concepts/template-compilation.md)
3. [Two-Way Binding](/concepts/two-way-binding.md) → [ng-model](/concepts/ng-model.md) → [Binding Propagation](/concepts/binding-propagation.md)
4. [angular.module](/concepts/angular-module.md) → [Providers](/concepts/providers.md) → [Services](/concepts/services.md) → [Dependency Injection Container](/concepts/dependency-injection-container.md)
5. [Controller Scope Model](/concepts/controller-scope-model.md) → [Directive Definition Object](/concepts/directive-definition-object.md) → [Compile vs Link](/concepts/compile-vs-link.md)
6. [$q Promises](/concepts/q-promises.md) → [$http Service](/concepts/http-service.md) → [Async Digest Integration](/concepts/async-digest-integration.md)
7. [Watcher Explosion](/concepts/watcher-explosion.md) → [Digest Cost](/concepts/digest-cost.md) → [Optimization Strategies](/concepts/optimization-strategies.md)
8. [MVC-ish Model](/concepts/mvc-model.md) → [Separation of Concerns](/concepts/separation-of-concerns.md) → [Service vs Controller Layer](/concepts/service-vs-controller-layer.md)

---

## Runtime model

* [Scope](/concepts/scope.md) — Hierarchical context object that holds model data and hosts watchers.
* [Watchers](/concepts/watchers.md) — Functions registered on scopes that detect model changes during digest.
* [Dirty Checking](/concepts/dirty-checking.md) — Compare-old-vs-new strategy AngularJS uses instead of observable push reactivity.
* [Digest Cycle](/concepts/digest-cycle.md) — The core `$digest` / `$apply` loop that propagates changes to the DOM.

## Scope and binding system

* [Expressions](/concepts/expressions.md) — Evaluated against scope; power interpolation and directives.
* [Interpolation](/concepts/interpolation.md) — `{{ }}` bindings compiled into watcher-backed DOM updates.
* [Two-Way Binding](/concepts/two-way-binding.md) — Model-to-view and view-to-model synchronization via watchers.
* [ng-model](/concepts/ng-model.md) — Directive that wires form controls to scope properties with `$setViewValue` / `$render`.
* [Binding Propagation](/concepts/binding-propagation.md) — How a single change ripples through parent and child scopes during digest.
* [Binding Performance](/concepts/binding-performance.md) — Cost of deep watches, `$watchCollection`, and unnecessary re-evaluation.

## Dependency injection

* [angular.module](/concepts/angular-module.md) — Module registry and composition root for an AngularJS app.
* [Config vs Runtime Phases](/concepts/config-runtime-phases.md) — When providers are registered versus when instances are created.
* [Providers](/concepts/providers.md) — `$provide` recipe types and the provider object pattern.
* [Services](/concepts/services.md) — `service()` recipe: constructor invoked once, instance registered.
* [Factories](/concepts/factories.md) — `factory()` recipe: function return value registered as instance.
* [Values and Constants](/concepts/values-constants.md) — Pre-built instances and non-overridable constants.
* [Dependency Injection Container](/concepts/dependency-injection-container.md) — `$injector` resolution, `$inject` annotations, and module imports.
* [Injection Resolution](/concepts/injection-resolution.md) — Order of lookup across modules and inline vs array notation.
* [Circular Dependency Handling](/concepts/circular-dependency-handling.md) — `$injector:unpr` errors and `$injector.invoke` workarounds.
* [Service Lifecycle](/concepts/service-lifecycle.md) — Instantiation timing tied to first injection.
* [Singleton Behavior](/concepts/singleton-behavior.md) — One instance per injector for service/factory registrations.

## Directives

* [Directive Definition Object](/concepts/directive-definition-object.md) — DDO fields: `restrict`, `template`, `scope`, `link`, `compile`.
* [Compile vs Link Phase](/concepts/compile-vs-link.md) — DOM traversal, template cloning, and when each function runs.
* [Linking Functions](/concepts/linking-functions.md) — `link` / `pre` / `post` hooks that bind scope to DOM.
* [Isolate Scope](/concepts/isolate-scope.md) — Directive-local scope with explicit `=` / `@` / `&` bindings.
* [Transclusion](/concepts/transclusion.md) — Preserving and projecting original DOM content into directive templates.

## Controllers

* [Controller Scope Model](/concepts/controller-scope-model.md) — Controllers attach behavior to a scope; they do not own the digest.
* [Scope Inheritance](/concepts/scope-inheritance.md) — Prototypal `$scope` chain from `$rootScope` downward.
* [Controller As Syntax](/concepts/controller-as-syntax.md) — `controllerAs` alias pattern and `$scope` reduction.

## Templates

* [Template Compilation](/concepts/template-compilation.md) — `$compile` transforms markup into linking functions.
* [DOM Linking](/concepts/dom-linking.md) — Applying compiled link functions to live DOM nodes.

## Async system

* [$q Promises](/concepts/q-promises.md) — AngularJS promise wrapper integrated with digest notifications.
* [$http Service](/concepts/http-service.md) — XHR abstraction returning promise-like `$http` responses.
* [Async Digest Integration](/concepts/async-digest-integration.md) — Why `$apply` / `$applyAsync` / `$timeout` matter after async callbacks.

## Routing

* [ngRoute](/concepts/ng-route.md) — `$routeProvider`, route definitions, and `$routeParams`.
* [SPA Routing Model](/concepts/spa-routing-model.md) — Hash or HTML5 mode, single-page navigation without full reload.
* [View Swapping](/concepts/view-swapping.md) — `ng-view` replacement and controller/template re-instantiation per route.

## Performance model

* [Watcher Explosion](/concepts/watcher-explosion.md) — How bindings and directives multiply watcher count.
* [Digest Cost](/concepts/digest-cost.md) — O(watchers × digest passes) and the `$digest` TTL limit.
* [Optimization Strategies](/concepts/optimization-strategies.md) — One-time binding (`::`), `track by`, `$watchCollection`, manual `$digest`.

## Architecture patterns

* [MVC-ish Model](/concepts/mvc-model.md) — Views (templates), controllers (presentation logic), services (domain/data).
* [Separation of Concerns](/concepts/separation-of-concerns.md) — Keeping DOM manipulation in directives, state in services.
* [Service vs Controller Layer](/concepts/service-vs-controller-layer.md) — Fat services, thin controllers in legacy SPA design.
