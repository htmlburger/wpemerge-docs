# \App shortcuts

## \App::app()

`\App::app();`

Get the internal application instance.

## \App::resolve()

`\App::resolve( $key );`

Resolve a key from the dependency injection container:

```php
$service = \App::resolve( 'some_service_key' );
```

!> Using a [service provider](/framework/tools/service-providers.md) to inject your dependencies is the better approach - only use this shortcut sparingly.

## \App::container()

`\App::container();`

Get the application dependency injection container instance:

```php
$container = \App::container();
```

## \App::render()

`\App::render( $views, $context = [] )`

Render the first view that exists with the given context.

```php
// Render a specific view:
\App::render( 'content' );

// Render the first view that exists:
\App::render( ['views/content-single', 'views/content'] );
```

## \App::route()

`\App::route()`

Start a [route definition](/framework/routing/defining-routes.md).

```php
\App::route()->get()->where( 'is_home' )->handle( 'HomeController@index' );
```

## \App::run()

`\App::run( RequestInterface $request, $middleware, $handler, $arguments = [] )`

Run a request through the specified middleware and handler (including handler middleware, if any) with the given arguments returning a [PSR-7](https://www.php-fig.org/psr/psr-7/) response object outside of the normal WP Emerge flow:

```php
use WPEmerge\Requests\Request;

$response = \App::run(
    new Request( 'GET', 'http://mysite.test/' ),
    ['web'],
    'HomeController@index'
);

// If you wish to output the response as usual you can use this:
\App::responses()->respond( $response );
```

## \App::closure()

`\App::closure()->value( $key );`

Make a closure that resolves a value from the container:
```php
$closure = \App::closure()->value( 'my_service' );
$my_service = $closure();
```

`\App::closure()->method( $key, $method )`

Make a closure that resolves a class instance from the container and calls one of its methods.
Useful if you need to pass a callable to an API without container support such as the REST API:
```php
register_rest_route( 'myplugin/v1', '/author/(?P<id>\d+)', [
    'methods' => 'GET',
    'callback' => \App::closure()->method( 'my_service', 'my_method' ),
] );
```

## \App::responses()

`$responses_service = \App::responses();`

Get the [Response](/framework/routing/controllers.md#response-objects) service instance.

## \App::response()

`$response = \App::response();`

Create a new blank [PSR-7](https://www.php-fig.org/psr/psr-7/) response object.

See [Response Objects](/framework/routing/controllers.md#response-objects) for more response shortcuts:
```php
$response = \App::output( $text );
$response = \App::json( $data );
$response = \App::redirect();
$response = \App::view( $views );
$response = \App::error( $status );
```

## \App::views()

`$views_service = \App::views();`

Get the Views service instance.

## \App::layoutContent()

`\App::layoutContent();`

Ouput the layout content, if any. Relevant only for the default view engine and should be used in views which are used as layouts for other views.

## \App::csrf()

`$csrf_service = \App::csrf();`

Get the [CSRF](/framework/tools/csrf-protection.md) service instance.

## \App::flash()

`$flash_service = \App::flash();`

Get the [Flash](/framework/tools/flash.md) service instance.

## \App::oldInput()

`$old_input_service = \App::oldInput();`

Get the [Old Input](/framework/tools/oldinput.md) service instance.
