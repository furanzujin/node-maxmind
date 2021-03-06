node-maxmind [![Build Status](https://travis-ci.org/runk/node-maxmind.png)](https://travis-ci.org/runk/node-maxmind)
========


Pure Javascript module for Geo IP lookup using Maxmind binary databases (aka mmdb or geoip2).
Fastest Maxmind lookup library available - up to [17,000% faster](https://github.com/runk/node-maxmind-benchmark) than other libraries. Module has 100% test coverage with comprehensive test suite. It natively works with binary Maxmind database format and doesn't require any "CSV - {specific lib format}" conversions as some other modules do. Maxmind binary databases are highly optimized for size and performance so there's no point working with other than that format.


## GEO databases

You might want to use [geolite2](https://github.com/runk/node-geolite2) module with free geo databases. Alternatively, free databases available for [download here](http://dev.maxmind.com/geoip/geoip2/geolite2/). If you need better accuracy you should consider buying [commercial subscription](https://www.maxmind.com/en/geoip2-databases).


## Installation

```shell
npm i maxmind
```


## Usage

```javascript
var maxmind = require('maxmind');

maxmind.open('/path/to/GeoLite2-City.mmdb', (err, cityLookup) => {
  var city = cityLookup.get('66.6.44.4');
});

maxmind.open('/path/to/GeoOrg.mmdb', (err, orgLookup) => {
  var city = orgLookup.get('66.6.44.4');
});

// Be careful with sync version! Since mmdb files
// are quite large (city database is about 100Mb)
// `fs.readFileSync` blocks whole process while it
// reads file into buffer.

var cityLookup = maxmind.openSync('/path/to/GeoLite2-City.mmdb');
var city = cityLookup.get('66.6.44.4');

var orgLookup = maxmind.openSync('/path/to/GeoOrg.mmdb');
var organization = orgLookup.get('66.6.44.4');
```


## V6 Support

Module is fully compatible with IPv6. There are no differences in API between IPv4 and IPv6.

```javascript
var lookup = maxmind.openSync('/path/to/GeoLite2.mmdb');
var location = lookup.get('2001:4860:0:1001::3004:ef68');
```


## Options
### cache
Module uses [tiny-lru](https://github.com/avoidwork/tiny-lru). You can configure its settings by doing following:

```javascript
var lookup = maxmind.openSync('/path/to/GeoLite2.mmdb', {
  cache: {
    max: 500, // Max items in cache, by default it's 6000
  }
});
lookup.get('1.1.1.1');
```
### watchForUpdates
Supports reloading the reader when changes occur to the database that is loaded. (default `false`). Only supported by Node v0.5.10+.
```javascript
var lookup = maxmind.openSync('/path/to/GeoLite2.mmdb', { watchForUpdates: true });
lookup.get('1.1.1.1');
```

You also can specify wether the watcher should be persistent or not. If it is persistent, a node process will be blocked in watching state if the watcher is the only thing still running in the program. You can use `watchForUpdatesNonPersistent` option (default `false`) to prevent this behavior.
```javascript
var lookup = maxmind.openSync('/path/to/GeoLite2.mmdb', {
  watchForUpdates: true,
  watchForUpdateNonPersistent: true,
});
lookup.get('1.1.1.1');
```


Also, you can specify custom hook function on database update.

```javascript
var opts = {
  watchForUpdates: true,
  watchForUpdatesHook: () => { console.log('database updated!'); },
};
var lookup = maxmind.openSync('/path/to/GeoLite2.mmdb', opts);
lookup.get('1.1.1.1');
```

## IP addresses validation

Module supports validation for both IPv4 and IPv6:

```javascript
maxmind.validate('66.6.44.4'); // returns true
maxmind.validate('66.6.44.boom!'); // returns false

maxmind.validate('2001:4860:0:1001::3004:ef68'); // returns true
maxmind.validate('2001:4860:0:1001::3004:boom!'); // returns false
```


## GeoIP Legacy binary format

In case you want to use legacy GeoIP binary databases you should use [maxmind@0.6](https://github.com/runk/node-maxmind/releases/tag/v0.6.0).


## References
 - Loosely based on https://github.com/PaddeK/node-maxmind-db
 - MaxMind DB file format specification http://maxmind.github.io/MaxMind-DB/
 - MaxMind test/sample DB files https://github.com/maxmind/MaxMind-DB
 - GeoLite2 Free Downloadable Databases http://dev.maxmind.com/geoip/geoip2/geolite2/
 - Great talk about V8 performance https://www.youtube.com/watch?v=UJPdhx5zTaw
 - V8 Optimization killers https://github.com/petkaantonov/bluebird/wiki/Optimization-killers
 - More V8 performance tips http://www.html5rocks.com/en/tutorials/speed/v8/


## License

MIT
