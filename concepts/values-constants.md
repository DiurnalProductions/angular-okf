---
id: angularjs.values_constants
type: concept
title: Values and Constants
description: value() and constant() register pre-built instances; constants are available during the config phase.
tags: [angularjs, values, constants, di, modules]
prerequisites:
  - angularjs.providers
related:
  - angularjs.config_runtime_phases
  - angularjs.dependency_injection_container
resource: https://docs.angularjs.org/api/ng/service/$provide
timestamp: 2026-01-01
---

## Summary

**`.value(name, instance)`** and **`.constant(name, instance)`** register pre-constructed values as injectables. Both are singletons. The critical difference: **constants** can be injected into `.config()` blocks; **values** cannot.

## Mental model

```
.value('apiKey', 'abc123')     → injectable at runtime only
.constant('MAX_ITEMS', 100)    → injectable in config AND runtime
```

Use cases:

- **Constants**: configuration literals, feature flags, enum maps needed during provider configuration.
- **Values**: runtime objects that may be decorated or replaced before first use (via `$provide.decorator`).

Neither supports lazy computation— the instance exists at registration time. For computed singletons, use `.factory()`.

Constants cannot be overwritten by `$provide.decorator` in the same way values can (constants are fixed by design).

## Example

```javascript
angular.module('app', [])
  .constant('APP_VERSION', '1.0.0')
  .value('defaultLocale', 'en-US')
  .config(function(APP_VERSION, $provide) {
    // APP_VERSION injectable here
    // defaultLocale NOT injectable here
  })
  .run(function(defaultLocale, APP_VERSION) {
    console.log(APP_VERSION, defaultLocale);
  });
```

## Common mistakes

- Using `.value()` for config-phase needs, then wondering why injection fails in `.config()`.
- Registering mutable shared objects as `.value()` and mutating them globally without clear ownership.
- Using `.constant()` for secrets that should not be hard-coded in client bundles.
- Expecting `.constant()` to enforce immutability at runtime—it prevents provider override, not JavaScript mutation.

## Related concepts

- [Providers](/concepts/providers.md) — Parent registration type.
- [Config vs Runtime Phases](/concepts/config-runtime-phases.md) — When each recipe is injectable.
