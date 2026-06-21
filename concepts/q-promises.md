---
id: angularjs.q_promises
type: concept
title: $q Promises
description: AngularJS promise implementation integrated with digest via $evalAsync and then/finally callbacks wrapped in $apply.
tags: [angularjs, promises, async, q]
prerequisites:
  - angularjs.digest_cycle
related:
  - angularjs.http_service
  - angularjs.async_digest_integration
resource: https://docs.angularjs.org/api/ng/service/$q
timestamp: 2026-01-01
---

## Summary

**`$q`** is AngularJS's promise library modeled after Kris Kowal's Promises/A+. It provides `$q.defer()`, `$q.when()`, `$q.all()`, and `$q.race()`. Resolution callbacks run inside the Angular digest cycle, making `$q` the preferred async primitive over raw Promise in AngularJS apps.

## Mental model

```
$q.defer()
  → deferred.promise
  → deferred.resolve(value) / reject(reason)
  → .then handlers scheduled via $evalAsync / $apply
  → digest runs → scope bindings update
```

Why `$q` over native Promise (in AngularJS era):

- **Digest integration** — `.then` triggers `$rootScope.$digest` (or batches via `$evalAsync`).
- ** `$q.all` / `$q.race`** — familiar composition for `$http` orchestration.
- ** `$q.when`** — wrap sync values or foreign promises.

`$http` returns a promise-like object with extra methods (`success`/`error` deprecated pattern) built on `$q`.

## Example

```javascript
angular.module('app', [])
  .service('UserLoader', function($q, $http) {
    this.loadAll = function(ids) {
      var promises = ids.map(function(id) {
        return $http.get('/api/users/' + id).then(function(res) {
          return res.data;
        });
      });
      return $q.all(promises);
    };
  });

// In controller
UserLoader.loadAll([1, 2, 3]).then(function(users) {
  $scope.users = users; // digest already triggered by $q
});
```

## Common mistakes

- Mixing native `Promise` with `$scope` updates without `$scope.$apply`.
- Swallowing rejections—always chain `.catch` or second `.then(null, fn)`.
- Creating deferred manually but never resolving/rejecting (hanging UI).
- `$q` anti-pattern: resolving deferred inside `$apply` unnecessarily nested.

## Related concepts

- [$http Service](/concepts/http-service.md) — Built on `$q`.
- [Async Digest Integration](/concepts/async-digest-integration.md) — Why digest matters for promises.
- [Digest Cycle](/concepts/digest-cycle.md) — Triggered after `$q` resolution.
