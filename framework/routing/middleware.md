# Route Middleware

Middleware allow you to modify the request and/or response before and/or after it reaches the route handler. Middleware can be any of the following:
1. the class name of a class that implements `MiddlewareInterface` (see `src/Middleware/MiddlewareInterface`). This is the recommended way of defining middleware.
1. any anonymous function
1. an instance of a class  that implements `MiddlewareInterface`

A common example for middleware usage is protecting certain routes to be accessible by logged in users only:

```php
class AuthenticationMiddleware implements \WPEmerge\Middleware\MiddlewareInterface {
    public function handle( $request, Closure $next ) {
        if ( ! is_user_logged_in() ) {
            $return_url = $request->getUrl();
            $login_url = wp_login_url( $return_url );
            return app_redirect()->to( $login_url );
        }
        return $next( $request );
    }
}

Router::get( '/protected-url/')
    ->add( AuthenticationMiddleware::class );
```

You can also define global middleware which is applied to all defined routes when booting the framework:

!> Global middleware is only applied on defined routes - normal WordPress requests that do not match any route 
will NOT have middleware applied. To apply global middleware to all requests you have to match all requests with 
routes. Take a look at [Handling all requests](framework/routing/methods.md#handling-all-requests) for an easy way to 
achieve this.

```php
WPEmerge::boot( [
    'global_middleware' => [
        AuthenticationMiddleware::class,
    ],
] );
```

## Order of execution

Step 1: Middleware is concatenated in the following order:

1. Global middleware
2. Route group middleware
3. Route middleware

Step 2: Middleware is sorted by priority as specified in the `'middleware_priority'` key of your [configuration](framework/configuration.md):

```php
WPEmerge::boot( [
    'middleware_priority' => [
        // CLASS_NAME => DESIRED_PRIORITY,
        \App\Middleware\YourMiddleware::class => 30,
    ],
] );
```

!> Middleware defined with an anonymous function will use the default priority as specified in your [configuration](framework/configuration.md).

!> When compared, middleware with equal priority will retain their original relative order from Step 1.
