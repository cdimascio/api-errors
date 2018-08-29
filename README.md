# wcp-errors

Package for normalizing api errors using the following format:

![](https://github.com/cdimascio/wcp-errors/blob/master/assets/error.png?raw=true)

## Install

```shell
npm install wcp-errors
```

## Usage

```JavaScript
const { notFound } = require('wcp-errors');
throw badRequest('first name is required.');
```

## Examples (ExpressJS)

```javascript
app.get('/not_found', function(req, res, next) {
  next(notFound());
});

// Bad request example
app.get('/bad_request', function(req, res, next) {
  next(
    badRequest('Eek! A bad request', new Error(), {
      type: 'parameter',
      name: 'Eek',
    })
  );
});

// Throw! example
app.get('/throws', function(req, res, next) {
  throw new Error('Oh noes!');
});

// Error handler
app.use(function(err, req, res, next) {
  if (err instanceof ApiError) {
    res.status(err.statusCode).json(err);
  } else {
    res.status(500).json(internalServerError(err.message, err));
  }
});
```

## Run the examples

- `cd example/express`
- `npm install`
- `npm start`

Open a browser and run

- [http://localhost:3000/not_found](http://localhost:3000/not_found)
- [http://localhost:3000/bad_request](http://localhost:3000/bad_request)
- [http://localhost:3000/throws](http://localhost:3000/throws)

## APIs

### Raw

The raw API is only necessary when multiple errors are to be returned in the wcp error response. For scenarios where a single error is to be returned, use the [Basic](#basic) Apis.

```javascript
 const { ApiError } = require('wcp-errors');

// Manually create a new API error
const e = new ApiError({
  statusCode: 409,
  code: 'conflict',
  message,
  error, // optional error
  target, // optional target
});

// Add additional errors to the error
e.add({
  code = 'error',
  message = 'unxepected_error',
  target, // optional target
  error // optional error object
})
```

### Basic

All basic Apis take the following three ***optional*** arguments: 

- `message`: a string describing the error
- `error`: an `Error` object
- `target`: an object with shape `{ type, name }`

### All APIs

```javascript
badRequest();
conflict();
forbidden();
internalServerError();
methodNotAllowed();
notAcceptable();
notFound();
requestEntityTooLarge();
unAuthorized();
unsupportedMediaType();
```

## TODO
Create dedicated Express middleware, such that a user does not have to write the fallback error handler middleware.

ex:

```javascript
app.use(function(err, req, res, next) {
  if (err instanceof ApiError) {
    res.status(err.statusCode).json(err);
  } else {
    res.status(500).json(internalServerError(err.message, err));
  }
});
```

## License

MIT
