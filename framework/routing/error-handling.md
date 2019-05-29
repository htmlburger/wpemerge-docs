# Error Handling

WP Emerge comes with a basic `ErrorHandler` class which is used to translate errors and exceptions to response objects. It is often a good idea to extend or replace that class in order to customize error handling.

## Development

In development mode, the [filp/whoops](https://github.com/filp/whoops) package is included which provides you with a beautiful and interactive stack trace interface whenever an error occurs:
![filp/whoops](https://camo.githubusercontent.com/31a4e1410e740fd0ccda128cbcab8723f45e7e73/687474703a2f2f692e696d6775722e636f6d2f305651706539362e706e67)

## Production

In production, any unhandled errors or exceptions will be translated to a 500 error and the first available view will be loaded following this view hierarchy:

1. `{$status}.php`
2. `error.php`
3. `index.php`

## Customization

A popular use case for custom error handling is to translate common exceptions to responses - this way you will have handy shortcuts in controller methods. For example, you can throw a custom `ValidationException` and then translate it to a redirect response with flashed error messages to handle invalid form requests.

The default `ErrorHandler` class comes with automatic handling for the built-in `\WPEmerge\Routing\NotFoundException` exception. When thrown it will be translated to a 404 response by setting the status header to 404 and using the `404.php` view:

```php
use WPEmerge\Routing\NotFoundException;

// ...

public function index( $request, $view ) {
    // ...
    if ( ! $some_entity ) {
        throw new NotFoundException();
    }
    // ...
}
```

To use a custom `ErrorHandler` class, replace the `WPEMERGE_EXCEPTIONS_ERROR_HANDLER_KEY` key in the service container with a class that implements the `\WPEmerge\Exceptions\ErrorHandlerInterface` interface:

```php
// Run this inside the register() method of a service provider.
$container[ WPEMERGE_EXCEPTIONS_ERROR_HANDLER_KEY ] = function() {
    return new MyCustomErrorHandler();
};
```
