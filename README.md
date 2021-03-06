# modified [![NPM version](https://badge.fury.io/js/modified.png)](http://badge.fury.io/js/modified) [![Build Status](https://travis-ci.org/kaelzhang/node-modified.png?branch=master)](https://travis-ci.org/kaelzhang/node-modified) [![Dependency Status](https://gemnasium.com/kaelzhang/node-modified.png)](https://gemnasium.com/kaelzhang/node-modified)

Modified is a simple request client to deal with http local cache. 

Modified implemented `last-modified`, `if-modified-since`, `etag`, `if-none-match` of HTTP specifications.
	
## Synopsis

Modified is built upon [request](https://npmjs.org/package/request) and flavors it with cache support, so if you are familiar with request, you are almost ready to use modified.

```js
var modified = require('modified');
var request = modified(options); // Then use it almost the same as request

request('http://google.com/doodle.png').pipe(fs.createWriteStream('doodle.png'));
```

## Usage

If your server supports etag, or checks the `if-modified-since` header, `modified` will manage the local cache for you.

### Specify the cache routing

If `options.cacheMapper` is not specified, caches will be saved into `'~/.node_modified/'` directory by default.

But you can do it yourself for better control.

```js
var request = modified({
	cacheMapper: function(options, callback){
		// your code...
		callback(
			null, 
			path.join(
				pathToSaveNpmCache,
				url.parse(options.uri).pathname,
				'couchdb-document.json'
			)
		);
	}
});

request({
	method: 'GET',
	url: 'http://registry.npmjs.org/modified'
	
}, function(err, res, body){
	// ...
})
```

#### cache_file

`String` 

The file path of the local cache to save response body according to a specific request. (Response headers will be saved into `cache_file + '.modified-headers.json'`)

If you don't want modified to cache for a certain request, `cache_file` should be set to `null`

```js
{
	cacheMapper: function(options, callback){
		var path = url.parse(options.url).pathname;
		
		if (path) {
			// 'http://xxx.com/abc/' -> '/abc'
			path = path.replace(/\/$/, '');
			
			callback(
				null, 
				// Where the cache should be saved.
				path.join(__dirname, 'cache', path)
			);
		
		} else {
			callback(null, null);
		}
	}
}
```

With `options.cacheMapper`, you could specify the paths where the cache will be saved.


## Programmatical APIs

```
var request = modified(options);
```

- options `Object`
	- cacheMapper `function()` Which is described above

### request(options, callback)

`modified(options)` returns a function which acts nearly the same as [`request`](https://npmjs.org/package/request);

#### Returns

`request(options, callback)` returns an instance of `modified.Modified` which is a sub-class of [Readable Stream](http://nodejs.org/api/stream.html#stream_class_stream_readable). 

A instance of `Modified` is an [EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter) with the following extra event(s):


#### Event: 'complete'

- response [`http.ServerResponse`](http://nodejs.org/api/http.html#http_class_http_serverresponse) The response object
- body `String` The response body

Emitted when all the request process is complete, after the execution of user callback (the one of `request(options, callback)`).


## Release History

* 2.0.0 - Completely redesigned.

* 1.1.0 - Modified instances are streams now. You can use modified to fetch binary files.

* 1.0.0 - Initial release





