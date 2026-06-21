---
id: angularjs.factories
type: concept
title: Factories
description: factory() recipe registers a function whose return value becomes the injectable singleton instance.
tags: [angularjs, factories, di, services]
prerequisites:
  - angularjs.providers
related:
  - angularjs.services
  - angularjs.singleton_behavior
  - angularjs.service_lifecycle
resource: https://docs.angularjs.org/api/ng/service/$provide
timestamp: 2026-01-01
---

## Summary

**`.factory(name, factoryFn)`** registers an injectable where the instance is whatever `factoryFn` returns. AngularJS calls the function once (on first injection), caches the return value, and injects that same reference thereafter.

## Mental model

Factories are the **most flexible** shorthand recipe:

- Return a plain object with methods (most common).
- Return a function (e.g., a configured `$resource` or closure).
- Return a constructor-like object without using `new` semantics.

Internally: `provider.$get = factoryFn`. No `new` keyword is applied—unlike `.service()`.

Choose factory when:

- You want to return an object literal directly.
- You need conditional instantiation logic before returning.
- You are wrapping a third-party library instance.

Choose service when:

- You prefer constructor/`this` style and may use inheritance.

## Example

```javascript
angular.module('app', [])
  .factory('Logger', function($window) {
    return {
      log: function(msg) { $window.console.log('[App]', msg); },
      error: function(msg) { $window.console.error('[App]', msg); }
    };
  })

  .factory('createCounter', function() {
    return function(initial) {
      var count = initial || 0;
      return {
        inc: function() { return ++count; },
        value: function() { return count; }
      };
    };
  });
```

## Common mistakes

- Using `.factory()` but writing a constructor that expects `new` without returning an explicit object.
- Returning a new object on every `$get` call conceptually—AngularJS only calls `$get` once; but confusing factory-with-returned-factory patterns.
- Duplicating `.service()` and `.factory()` registrations for the same token name.
- Returning primitives when callers expect reference-stable mutation (singleton number/string cannot be mutated in place meaningfully).

## Related concepts

- [Services](/concepts/services.md) — Constructor-based alternative.
- [Providers](/concepts/providers.md) — Underlying registration mechanism.
- [Singleton Behavior](/concepts/singleton-behavior.md) — Cached return value semantics.
