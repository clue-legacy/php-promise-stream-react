# Deprecation notice

This package has now been migrated over to
[react/promise-stream](https://github.com/reactphp/promise-stream)
and only exists for BC reasons.

```bash
$ composer require react/promise-stream
```

If you've previously used this package, upgrading should take no longer than a few minutes.
All functions have been merged as-is from the latest v0.1.2 release with no other changes,
so you can simply update your code to use the updated namespace like this:

```php
// old from clue/promise-stream-react
\Clue\React\Promise\Stream\buffer(…);

// new
\React\Promise\Stream\buffer(…);
```

See [react/promise-stream](https://github.com/reactphp/promise-stream) for more details.

The below documentation applies to the last release of this package.
Further development will take place in the updated
[react/promise-stream](https://github.com/reactphp/promise-stream),
so you're highly recommended to upgrade as soon as possible.

# Legacy clue/promise-stream-react [![Build Status](https://travis-ci.org/clue/php-promise-stream-react.svg?branch=master)](https://travis-ci.org/clue/php-promise-stream-react)

The missing link between Promise-land and Stream-land, 
built on top of [React PHP](http://reactphp.org/).

**Table of Contents**

* [Usage](#usage)
  * [buffer()](#buffer)
  * [first()](#first)
  * [all()](#all)
  * [unwrapReadable()](#unwrapreadable)
  * [unwrapWritable()](#unwrapwritable)
* [Install](#install)
* [License](#license)

> Note: This project is in beta stage! Feel free to report any issues you encounter.

## Usage

This lightweight library consists only of a few simple functions.
All functions reside under the `Clue\React\Promise\Stream` namespace.

The below examples assume you use an import statement similar to this:

```php
use Clue\React\Promise\Stream;

Stream\buffer(…);
```

Alternatively, you can also refer to them with their fully-qualified name:

```php
\Clue\React\Promise\Stream\buffer(…);
``` 

### buffer()

The `buffer(ReadableStreamInterface $stream)` function can be used to create
a `Promise` which resolves with the stream data buffer.

```php
$stream = accessSomeJsonStream();

Stream\buffer($stream)->then(function ($contents) {
    var_dump(json_decode($contents));
});
```

The promise will resolve with all data chunks concatenated once the stream closes.

The promise will resolve with an empty string if the stream is already closed.

The promise will reject if the stream emits an error.

The promise will reject if it is canceled.

### first()

The `first(ReadableStreamInterface|WritableStreamInterface $stream, $event = 'data')`
function can be used to create a `Promise` which resolves once the given event triggers for the first time.

```php
$stream = accessSomeJsonStream();

Stream\first($stream)->then(function ($chunk) {
    echo 'The first chunk arrived: ' . $chunk;
});
```

The promise will resolve with whatever the first event emitted or `null` if the
event does not pass any data.
If you do not pass a custom event name, then it will wait for the first "data"
event and resolve with a string containing the first data chunk.

The promise will reject once the stream closes – unless you're waiting for the
"close" event, in which case it will resolve.

The promise will reject if the stream is already closed.

The promise will reject if it is canceled.

### all()

The `all(ReadableStreamInterface|WritableStreamInterface $stream, $event = 'data')`
function can be used to create a `Promise` which resolves with an array of all the event data.

```php
$stream = accessSomeJsonStream();

Stream\all($stream)->then(function ($chunks) {
    echo 'The stream consists of ' . count($chunks) . ' chunk(s)';
});
```

The promise will resolve with an array of whatever all events emitted or `null` if the
events do not pass any data.
If you do not pass a custom event name, then it will wait for all the "data"
events and resolve with an array containing all the data chunks.

The promise will resolve with an array once the stream closes.

The promise will resolve with an empty array if the stream is already closed.

The promise will reject if the stream emits an error.

The promise will reject if it is canceled.

### unwrapReadable()

The `unwrapReadable(PromiseInterface $promise)` function can be used to unwrap
a `Promise` which resolves with a `ReadableStreamInterface`.

This function returns a readable stream instance (implementing `ReadableStreamInterface`)
right away which acts as a proxy for the future promise resolution.
Once the given Promise resolves with a `ReadableStreamInterface`, its data will
be piped to the output stream.

```php
//$promise = someFunctionWhichResolvesWithAStream();
$promise = startDownloadStream($uri);

$stream = Stream\unwrapReadable($promise);

$stream->on('data', function ($data) {
   echo $data;
});

$stream->on('end', function () {
   echo 'DONE';
});
```

If the given promise is either rejected or fulfilled with anything but an
instance of `ReadableStreamInterface`, then the output stream will emit
an `error` event and close:

```php
$promise = startDownloadStream($invalidUri);

$stream = Stream\unwrapReadable($promise);

$stream->on('error', function (Exception $error) {
    echo 'Error: ' . $error->getMessage();
});
```

The given `$promise` SHOULD be pending, i.e. it SHOULD NOT be fulfilled or rejected
at the time of invoking this function.
If the given promise is already settled and does not resolve with an
instance of `ReadableStreamInterface`, then you will not be able to receive
the `error` event.

### unwrapWritable()

The `unwrapWritable(PromiseInterface $promise)` function can be used to unwrap
a `Promise` which resolves with a `WritableStreamInterface`.

This function returns a writable stream instance (implementing `WritableStreamInterface`)
right away which acts as a proxy for the future promise resolution.
Once the given Promise resolves with a `WritableStreamInterface`, any data you
wrote to the proxy will be piped to the inner stream.

```php
//$promise = someFunctionWhichResolvesWithAStream();
$promise = startUploadStream($uri);

$stream = Stream\unwrapWritable($promise);

$stream->write('hello');
$stream->end('world');

$stream->on('close', function () {
   echo 'DONE';
});
```

If the given promise is either rejected or fulfilled with anything but an
instance of `WritableStreamInterface`, then the output stream will emit
an `error` event and close:

```php
$promise = startUploadStream($invalidUri);

$stream = Stream\unwrapWritable($promise);

$stream->on('error', function (Exception $error) {
    echo 'Error: ' . $error->getMessage();
});
```

The given `$promise` SHOULD be pending, i.e. it SHOULD NOT be fulfilled or rejected
at the time of invoking this function.
If the given promise is already settled and does not resolve with an
instance of `WritableStreamInterface`, then you will not be able to receive
the `error` event.

## Install

The recommended way to install this library is [through Composer](https://getcomposer.org).
[New to Composer?](https://getcomposer.org/doc/00-intro.md)

This will install the latest supported version:

```bash
$ composer require clue/promise-stream-react:^0.1.2
```

See also the [CHANGELOG](CHANGELOG.md) for details about version upgrades.

## License

MIT
