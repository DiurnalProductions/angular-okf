---
type: Concept
title: Providers
description: "The foundational $provide recipe type; all service, factory, and value registrations ultimately define a provider with $get."
tags: [angularjs, providers, di, modules]
prerequisites:
  - concepts/angular-module
  - concepts/config-runtime-phases
related:
  - concepts/services
  - concepts/factories
  - concepts/values-constants
  - concepts/dependency-injection-container
resource: "https://docs.angularjs.org/api/auto/service/$provide"
timestamp: 2026-01-01
---

## Summary

A **provider** is the most configurable registration recipe in AngularJS. Every injectable token resolves to a provider object with a `$get` method that produces the instance. Convenience methods—`.service()`, `.factory()`, `.value()`, `.constant()`—are shorthand over `.provider()`.

## Mental model

```
.provider('token', { $get: function(deps) { return instance; } })
     ↓ shorthand
.factory('token', function(deps) { return instance; })
.service('token', function(deps) { /* constructor body */ })
.value('token', instance)
.constant('token', instance)
```

| Recipe | `$get` behavior | Configurable in `.config()`? |
|--------|-----------------|------------------------------|
| `provider` | custom | yes (methods on provider) |
| `factory` | return fn result | no |
| `service` | `new` the fn | no |
| `value` | return fixed value | no |
| `constant` | return fixed value | injectable in config |

Providers exist **before** instantiation. During config, you can call `myProvider.setApiUrl(...)` on a custom provider. Factories and services cannot be reconfigured after registration.

## Example

```javascript
angular.module('app', [])
  .provider('ApiConfig', function() {
    var baseUrl = '/api';
    this.setBaseUrl = function(url) { baseUrl = url; };
    this.$get = function() {
      return { baseUrl: baseUrl };
    };
  })
  .config(function(ApiConfigProvider) {
    ApiConfigProvider.setBaseUrl('/api/v2');
  });
```

## Common mistakes

- Using `.service()` when you need to return a plain object literal (use `.factory()` instead—`service` uses `new`).
- Expecting `.value()` to be injectable in config blocks (only `.constant()` and providers are).
- Overusing `.provider()` when `.factory()` suffices, adding unnecessary boilerplate.
- Forgetting that `$get` can depend on other services—those services instantiate first on first injection of this token.

## Related concepts

- [Services](/concepts/services.md) — Constructor-style provider shorthand.
- [Factories](/concepts/factories.md) — Function-return provider shorthand.
- [Values and Constants](/concepts/values-constants.md) — Pre-built instance recipes.
