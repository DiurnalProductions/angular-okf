---
id: angularjs.http_service
type: concept
title: $http Service
description: XHR and JSONP wrapper returning promise-like responses with interceptors, transforms, and $q integration.
tags: [angularjs, http, async, xhr]
prerequisites:
  - angularjs.q_promises
related:
  - angularjs.async_digest_integration
  - angularjs.services
  - angularjs.config_runtime_phases
resource: https://docs.angularjs.org/api/ng/service/$http
timestamp: 2026-01-01
---

## Summary

**`$http`** is AngularJS's AJAX service. It wraps XMLHttpRequest (and JSONP), applies request/response transforms, runs interceptors, and returns a promise that resolves to a response object `{ data, status, headers, config }`. Success handlers trigger digest automatically.

## Mental model

```
$http.get(url, config)
  → request interceptors (modify config)
  → XHR send
  → response interceptors
  → transforms (JSON parse, etc.)
  → $q resolve/reject
  → .then callback → scope update → digest
```

Key `$httpProvider` config (in `.config()`):

- **`interceptors`** — auth tokens, global error handling.
- **`defaults`** — headers, `withCredentials`, transform defaults.
- **`useApplyAsync`** — batch digest after multiple responses.

`$resource` (ngResource module) builds RESTful shortcuts on top of `$http`—still `$q`-backed.

Caching: `{ cache: $cacheFactory }` or boolean for `$http` response cache.

## Example

```javascript
angular.module('app', [])
  .config(function($httpProvider) {
    $httpProvider.interceptors.push('AuthInterceptor');
  })
  .service('Api', function($http) {
    this.getUser = function(id) {
      return $http.get('/api/users/' + id).then(function(res) {
        return res.data;
      });
    };
  });
```

```javascript
.factory('AuthInterceptor', function($q) {
  return {
    request: function(config) {
      config.headers.Authorization = 'Bearer ' + getToken();
      return config;
    },
    responseError: function(rejection) {
      if (rejection.status === 401) { /* redirect */ }
      return $q.reject(rejection);
    }
  };
});
```

## Common mistakes

- Using deprecated `.success()` / `.error()` instead of `.then()` / `.catch()`.
- Mutating `$scope` in `$http` callback without realizing double `$apply` (usually harmless).
- Forgetting error handling on promise chain—silent failures in UI.
- Sending non-JSON payloads without proper `transformRequest` / Content-Type headers.

## Related concepts

- [$q Promises](/concepts/q-promises.md) — Underlying async primitive.
- [Async Digest Integration](/concepts/async-digest-integration.md) — Post-request UI updates.
- [Config vs Runtime Phases](/concepts/config-runtime-phases.md) — `$httpProvider` configuration.
