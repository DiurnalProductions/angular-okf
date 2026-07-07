# Concepts

* [angular.module](angular-module.md) - Module registry and composition root that defines injectable recipes, config blocks, and run blocks for an AngularJS application.
* [Async Digest Integration](async-digest-integration.md) - How $apply, $applyAsync, $evalAsync, and $timeout bridge non-Angular async callbacks into the digest cycle.
* [Binding Performance](binding-performance.md) - Performance cost of watch strategies, expression re-evaluation, and binding patterns in large templates.
* [Binding Propagation](binding-propagation.md) - How a single scope mutation ripples through watchers on parent, child, and sibling scopes during digest passes.
* [Circular Dependency Handling](circular-dependency-handling.md) - Strategies for breaking circular DI chains that cause $injector:unpr or $injector:cdep errors at bootstrap or instantiation.
* [Compile vs Link Phase](compile-vs-link.md) - Compile transforms template DOM once; link binds a specific scope instance to each compiled node.
* [Config vs Runtime Phases](config-runtime-phases.md) - Two bootstrap phases—config registers providers; run executes after injection is ready and DOM is compiled.
* [Controller As Syntax](controller-as-syntax.md) - controllerAs alias pattern attaches the controller instance to scope as vm, reducing $scope property proliferation.
* [Controller Scope Model](controller-scope-model.md) - Controllers attach presentation logic to a scope; AngularJS instantiates them when ng-controller or route resolves.
* [Dependency Injection Container](dependency-injection-container.md) - The $injector built at bootstrap resolves tokens to instances using module recipes and annotation metadata.
* [Digest Cost](digest-cost.md) - Computational cost of $digest grows with watcher count and number of stabilization passes until TTL limit.
* [Digest Cycle](digest-cycle.md) - The $digest and $apply loop that runs watchers and synchronizes scope state with the DOM until no further changes occur.
* [Directive Definition Object](directive-definition-object.md) - Configuration object returned by directive factories defining restrict, template, scope, compile, link, and controller behavior.
* [Dirty Checking](dirty-checking.md) - Compare-old-vs-new strategy AngularJS uses to detect model changes instead of observable or proxy-based reactivity.
* [DOM Linking](dom-linking.md) - Applying compiled linking functions to live DOM nodes, associating scopes and triggering initial digest synchronization.
* [Expressions](expressions.md) - JavaScript-like snippets evaluated against scope during interpolation, binding, and $watch registration.
* [Factories](factories.md) - factory() recipe registers a function whose return value becomes the injectable singleton instance.
* [$http Service](http-service.md) - XHR and JSONP wrapper returning promise-like responses with interceptors, transforms, and $q integration.
* [Injection Resolution](injection-resolution.md) - How $injector resolves token names to provider instances across the module dependency graph.
* [Interpolation](interpolation.md) - Double-mustache bindings compile into watcher-backed text node updates synchronized during digest.
* [Isolate Scope](isolate-scope.md) - Directive-local scope with explicit one-way, two-way, and expression bindings decoupled from parent inheritance.
* [Linking Functions](linking-functions.md) - Pre and post link functions connect a scope instance to compiled DOM, registering watchers and event handlers.
* [MVC-ish Model](mvc-model.md) - AngularJS organizes SPAs into views (templates), controllers (presentation logic), and services (domain and data access).
* [ng-model](ng-model.md) - Directive connecting form controls to scope properties via $setViewValue, $render, parsers, and formatters pipeline.
* [ngRoute](ng-route.md) - Core routing module with $routeProvider for mapping URLs to templates, controllers, and resolve dependencies.
* [Optimization Strategies](optimization-strategies.md) - Techniques to reduce digest work including one-time binding, track by, bind once directives, and manual $digest control.
* [Providers](providers.md) - The foundational $provide recipe type; all service, factory, and value registrations ultimately define a provider with $get.
* [$q Promises](q-promises.md) - AngularJS promise implementation integrated with digest via $evalAsync and then/finally callbacks wrapped in $apply.
* [Scope](scope.md) - Hierarchical context object that holds model data, hosts watchers, and participates in digest propagation.
* [Scope Inheritance](scope-inheritance.md) - Child scopes prototypally inherit from parent scopes; property reads traverse the chain, writes may shadow on the child.
* [Separation of Concerns](separation-of-concerns.md) - Legacy AngularJS best practice of keeping DOM in directives, state in services, and controllers as thin presentation adapters.
* [Service Lifecycle](service-lifecycle.md) - Injectable instances are created lazily on first injection and persist for the lifetime of the injector.
* [Service vs Controller Layer](service-vs-controller-layer.md) - Fat services and thin controllers—controllers expose view state; services encapsulate reusable domain and integration logic.
* [Services](services.md) - service() recipe registers a constructor function; AngularJS instantiates it with new and caches the result as a singleton.
* [Singleton Behavior](singleton-behavior.md) - Each service, factory, or value registration yields one shared instance per injector for the application lifetime.
* [SPA Routing Model](spa-routing-model.md) - Single-page navigation using $location to change views without full page reload via hash or HTML5 history API.
* [Template Compilation](template-compilation.md) - $compile traverses DOM and directives, transforming templates into linking functions that bind scope to elements.
* [Transclusion](transclusion.md) - Captures original directive content and projects it into the directive template, preserving scope context options.
* [Two-Way Binding](two-way-binding.md) - Synchronization between model and view through paired watchers that propagate changes in both directions during digest.
* [Values and Constants](values-constants.md) - value() and constant() register pre-built instances; constants are available during the config phase.
* [View Swapping](view-swapping.md) - ng-view destroys the previous route scope and DOM, then compiles and links the new route template and controller.
* [Watcher Explosion](watcher-explosion.md) - Uncontrolled growth of $watch registrations from bindings, ng-repeat, and directives causing digest slowdown.
* [Watchers](watchers.md) - Functions registered on scopes that compare old and new expression values during each digest pass.
