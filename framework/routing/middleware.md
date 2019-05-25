# Route Middleware

Middleware allow you to modify the request and/or response before and/or after it reaches the route handler. Middleware can be any of the following:
1. the class name of a class that has a method with the following signature:  
`public function handle( \WPEmerge\Requests\RequestInterface $request, \Closure $next )`
1. an alias of a middleware class as defined in the `middleware` key of your configuration. This is the recommended way of using middleware.

A common example for middleware usage is protecting certain routes to be accessible by logged in users only:

```php
class AuthenticationMiddleware {
    public function handle( $request, $next ) {
        if ( ! is_user_logged_in() ) {
            $return_url = $request->getUrl();
            $login_url = wp_login_url( $return_url );
            return WPEmerge\redirect()->to( $login_url );
        }
        return $next( $request );
    }
}

Route::get()
    ->url( '/protected-url/')
    ->middleware( AuthenticationMiddleware::class )
    ->handle( ... );
```

You can also define middleware that is automatically applied to all routes - check out the [Configuration](/framework/configuration) page for more details.

!> Middleware is only applied on defined routes - normal WordPress requests that do not match any route will NOT have middleware applied. To apply middleware to all requests you have to match all requests with routes. Take a look at [Handling all requests](/framework/routing/methods#handling-all-requests) for an easy way to achieve this.

## Passing arguments to middleware

Another benefit to using route aliases is that they enable you to pass parameters to middleware in a comma-separated list.

For example, if you have a middleware class that limits access to a route based on the capabilities of the current user you can reuse it for different capabilities by passing an argument:

Our middleware class:
```php
class CapabilityMiddleware {
    // Note the new $capability parameter:
    public function handle( RequestInterface $request, Closure $next, $capability ) {
        if ( ! current_user_can( $capability ) ) {
            // Return a 404 when the current user fails the capability check:
            throw new NotFoundException();
        }

        return $next( $request );
    }
}
```

Defining the middleware alias:
```php
WPEmerge::bootstrap( [
    'middleware' => [
        'capability' => CapabilityMiddleware::class,
    ],
    // ...
] );
```

Passing `manage_options` as the required capability to the middleware:
```php
Route::middleware( 'capability:manage_options' )->...
```

## Middleware Groups

Commonly used middleware can be grouped up so you do not have to list it every time and pass the same arguments (if any). To define a new middleware group you have to add a new key to the `middleware_groups` configuration option with a value of the array of middleware that you wish this group to include:
```php
WPEmerge::bootstrap( [
    'middleware_groups' => [
        'mygroup' => [
            'mymiddleware1',
            'mymiddleware2',
            // ...
        ],
    ],
] );
```

Using middleware groups is done in exactly the same way as you would use a single middleware alias:
```php
Route::middleware( 'mygroup' )->...
```

Refer to the [Configuration](/framework/configuration) article for more information on middleware groups.

## Order of execution

Middleware is sorted by priority as specified in the `'middleware_priority'` key of your [configuration](/framework/configuration).

!> When compared, middleware that is not specified in the priority array will be executed after all middleware that is and will keep its relative order to other middleware without a specified priority.
