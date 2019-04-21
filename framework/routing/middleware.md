# Route Middleware

Middleware allow you to modify the request and/or response before and/or after it reaches the route handler. Middleware can be any of the following:
1. the class name of a class that has a method with the following signature: `public function handle( \WPEmerge\Requests\RequestInterface $request, \Closure $next )`
1. an alias of a middleware class as defined in the `middleware` key of your config. This is the recommended way of using middleware.

A common example for middleware usage is protecting certain routes to be accessible by logged in users only:

```php
class AuthenticationMiddleware {
    public function handle( $request, $next ) {
        if ( ! is_user_logged_in() ) {
            $return_url = $request->getUrl();
            $login_url = wp_login_url( $return_url );
            return app_redirect()->to( $login_url );
        }
        return $next( $request );
    }
}

Route::get()
    ->url( '/protected-url/')
    ->middleware( AuthenticationMiddleware::class )
    ->handle( ... );
```

You can also define middleware that is automatically applied to all routes - check out the [Configuration](framework/configuration.md) page for more details.

!> Middleware is only applied on defined routes - normal WordPress requests that do not match any route will NOT have middleware applied. To apply middleware to all requests you have to match all requests with routes. Take a look at [Handling all requests](framework/routing/methods.md#handling-all-requests) for an easy way to achieve this.

## Order of execution

Middleware is sorted by priority as specified in the `'middleware_priority'` key of your [configuration](framework/configuration.md).

!> When compared, middleware that is not specified in the priority array will be executed after all middleware that is and will keep its relative order to other middleware without a specified priority.
