# Controllers

A controller can be any class and any method of that class can be used as a route handler.

## Requirements

Route handlers have a couple of requirements:

1. Must receive at least 2 arguments
    1. `$request` - an object representing the current request to the server
    2. `$view` - the view filepath WordPress is currently attempting to load
    3. You may have additional arguments depending on the route condition(s) you are using (e.g. URL parameters, custom condition arguments etc.)
2. Must return one the following:
    1. Any `string` which will be output literally
    2. Any `array` which will be output as a JSON response
    3. an object implementing the `Psr\Http\Message\ResponseInterface` interface.
3. Can optionally throw exceptions. Make sure you catch these exceptions in your `ErrorHandler` and translate them to responses so that visitors are not greeted with blank pages or stack traces. An example exception that is handled for you by default is the `\WPEmerge\Routing\NotFoundException` exception which is translated to a 404 response.

## Instantiation

By default, WP Emerge will instantiate your controller class and call the specified method.
However, if your controller class is registered in the service container with its class name as the key, then the class will be resolved from the service container instead of being directly instantiated:

```php
// Run this inside the register() method of a service provider.
$container[ HomeController::class ] = function( $container ) {
    // your custom instantiation code here, e.g.:
    return new HomeController();
}
```

## Response Objects

To return a suitable response object you can use one of the built-in utility functions:

```php
class MyController {
    public function someHandlerMethod( $request, $view ) {
        return \WPEmerge\view( 'templates/about-us.php' );
        return \WPEmerge\redirect()->to( home_url( '/' ) );
        return \WPEmerge\error( 404 );
        return \WPEmerge\output( 'Hello World!' ); // same as returning a string
        return \WPEmerge\json( ['foo' => 'bar'] ); // same as returning an array
        return \WPEmerge\response(); // a blank response object
    }
}
```

Since all of the above functions return an object implementing the `ResponseInterface` interface, you can use immutable chain calls to customize the response, e.g. changing the status:

```php
class MyController {
    public function someHandlerMethod( $request, $view ) {
        return \WPEmerge\view( 'templates/about-us.php' )->withStatus( 201 );
    }
}
```

### `\WPEmerge\output( $output );`

Returns a new response object with the supplied string as the body.

### `\WPEmerge\view( $views );`

By default, uses `locate_template( $views )` to resolve a view and applies the view output as the response body.

Optionally, you can pass context values to be used from inside the view by chaining `->with( ['foo' => 'bar'] )`.

### `\WPEmerge\json( $data );`

Returns a new response object json encoding the passed data as the body.

### `\WPEmerge\redirect()->to( $url, $status = 302 );`

Returns a new response object with location and status headers to redirect the user.

### `\WPEmerge\redirect()->back( $fallback, $status = 302 );`

Returns a new response object with location and status headers to redirect the user. By default it will use the `Referer` request header. If one is not specified, it will use the supplied `$fallback` as the url. If neither is specified it will use the current request url instead.

### `\WPEmerge\error( $status );`

Returns a new response object with the supplied status code. Additionally, attempts to render a suitable `{$status}.php` view file.

### `\WPEmerge\response();`

Returns a blank response object.
