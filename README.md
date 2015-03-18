# Distribucache Redis Store [![Build Status](https://secure.travis-ci.org/areusjs/distribucache-redis-store.png)](http://travis-ci.org/areusjs/distribucache-redis-store) [![NPM version](https://badge.fury.io/js/distribucache-redis-store.svg)](http://badge.fury.io/js/distribucache-redis-store)

A Redis datastore for the [Distribucache](https://github.com/areusjs/distribucache) auto-repopulating cache.

## Usage

Here's what a simple service using Distribucache with Redis may look like:

```js
var distribucache = require('distribucache'),
  redisStore = require('distribucache-redis-store'),

  cacheClient = distribucache.createClient(redisStore({
    host: 'localhost',
    port: 6379
  })),

  cache,
  Service;

cache = cacheClient.create('my:values', {
  staleIn: '10 sec',
  populateIn: '5 sec',
  pausePopulateIn: '1 min',
  populate: function (key, cb) {
    model.get(key, cb);
  }
});

Service.get = function (key, cb) {
  cache.get(key, cb);
};
```


### API

  - `redisStore(config)`

Possible `config` values below.
```
{String} [config.namespace]
{Boolean} [config.isPreconfigured] defaults to false (see note below)
```

`isPreconfigured` is used to determine whether the store needs to set-up
keyspace notifications, which are used to support the `populateIn` feature in a
way that multiple Distribucache clients would not conflict with each other while
automatically populating the cache.  Disable this to avoid a warning if you're
running Redis in an environment where `CONFIG` is not available.
One such environment is AWS; where you will need to set the `notify-keyspace-events`
property to `Kx` manually through the AWS Management Console.

In addition to the config above, all of the options defined in the
[node\_redis#createClient()](https://github.com/mranney/node_redis#rediscreateclient)
are allowed to configure the Redis client.


## License

[MIT](/LICENSE)
