# Middleware

## Route Middleware

Middleware allow you to modify the request and/or response before and/or after it reaches the route handler. Middleware can be any of the following:
1. the class name of a class that has a method with the following signature:  
`public function handle( \WPEmerge\Requests\RequestInterface $request, \Closure $next )`
2. an alias of a middleware class as defined in the `middleware` key of your configuration. **This is the recommended way of using middleware.**

A common example for middleware usage is protecting certain routes to be accessible by logged in users only:

```php
class AuthenticationMiddleware {
    public function handle( $request, $next ) {
        if ( ! is_user_logged_in() ) {
            $return_url = $request->getUrl();
            $login_url = wp_login_url( $return_url );
            return \WPEmerge\redirect()->to( $login_url );
        }
        return $next( $request );
    }
}

Route::get()
    ->url( '/protected-url/')
    ->middleware( AuthenticationMiddleware::class )
    ->handle( ... );
```

?> There's built-in middleware that deals with this exact use case - `user.logged_in`. Check out the [Built-in Middleware](#built-in-middleware) section below for more information.

You can also define middleware that is automatically applied to all routes - check out the [Configuration](/framework/configuration) page for more details.

!> Middleware is only applied on defined routes - normal WordPress requests that do not match any route will NOT have middleware applied. To apply middleware to all requests you have to match all requests with routes. Take a look at [Handling all requests](/framework/routing/methods#handling-all-requests) for an easy way to achieve this.

## Controller Middleware

Another way you can specify middleware is by declaring it in your controller's `__construct()` method. To do so, follow these steps:
1. Implement the `HasControllerMiddlewareInterface` interface and use the `HasControllerMiddlewareTrait` trait like so:
  ```php
  use WPEmerge\Middleware\HasControllerMiddlewareInterface;
  use WPEmerge\Middleware\HasControllerMiddlewareTrait;
  
  class MyController implements HasControllerMiddlewareInterface {
      use HasControllerMiddlewareTrait;

      // ...
  }
  ```
2. Declare the `__construct()` method if you haven't already:
  ```php
  public function __construct() {

  }
  ```
3. Add your middleware using the `middleware()` method:
  ```php
  public function __construct() {
      $this->middleware( 'mymiddleware' );
  }
  ```

Here's a full example:
```php
class UserDashboard implements HasControllerMiddlewareInterface {
    use HasControllerMiddlewareTrait;

    public function __construct() {
        // Apply the 'csrf' middleware to all methods of this controller:
        $this->middleware( 'csrf' );

        // Apply the 'user.logged_in' middleware to all methods of this
        // controller except 'login':
        $this->middleware( 'user.logged_in' )->except( 'login' );

        // Apply the 'user.logged_out' middleware only to the 'login' method
        // of this controller:
        $this->middleware( 'user.logged_out' )->only( 'login' );
    }

    public function login() {
        // ...
    }

    public function logout() {
        // ...
    }
}
```

## Passing arguments to middleware

Another benefit of using middleware aliases is that they enable you to pass arguments to middleware in a comma-separated list.

For example, if you have a middleware class that limits access based on the capabilities of the current user you can reuse it for different capabilities by passing an argument:

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

?> There's built-in middleware that deals with this exact use case - `user.can`. Check out the [Built-in Middleware](#built-in-middleware) section below for more information.

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

## Order of Execution

Middleware is sorted by priority as specified in the `'middleware_priority'` key of your [configuration](/framework/configuration).

!> When compared, middleware that is not specified in the priority array will be executed after all middleware that is and will keep its relative order to other middleware without a specified priority.

## Built-in Middleware

### flash

Flash middleware is applied globally by default. Check out the [Flash](/framework/tools/flash) article for more information.

### old_input

OldInput middleware is applied globally by default. Check out the [OldInput](/framework/tools/oldinput) article for more information.

### csrf

CSRF middleware is **NOT** applied globally by default. Check out the [CSRF Protection](/framework/tools/csrf-protection) article for more information.

### user.logged_in:redirect_url

Require the current user to be logged in redirecting non-logged in users to the login page:
```php
Route::middleware( 'user.logged_in' )->...
```

Optionally, you can specify a custom url to redirect to:
```php
Route::middleware( 'user.logged_in:http://example.com' )->...
```

You can also filter the final url:
```php
add_filter( 'wpemerge.middleware.user.logged_in.redirect_url', function ( $url, $request ) {
    return 'http://example.com'; 
}, 10, 2 );
```

### user.logged_out:redirect_url

The opposite of `user.logged_in` - require the current user to be logged out redirecting logged in users to the home page:
```php
Route::middleware( 'user.logged_out' )->...
```

Optionally, you can specify a custom url to redirect to:
```php
Route::middleware( 'user.logged_out:http://example.com' )->...
```

You can also filter the final url:
```php
add_filter( 'wpemerge.middleware.user.logged_out.redirect_url', function ( $url, $request ) {
    return 'http://example.com'; 
}, 10, 2 );
```

### user.can:capability,object_id,redirect_url

Require the current user to have a capability redirecting users who do not to the homepage:
```php
Route::middleware( 'user.can:edit_posts' )->...
```

Optionally, you can specify an object ID for the capability check:
```php
Route::middleware( 'user.can:edit_post,10' )->...
```

Optionally, you can specify a custom url to redirect to:
```php
Route::middleware( 'user.can:edit_posts,0,http://example.com' )->...
```

You can also filter the capability:
```php
add_filter( 'wpemerge.middleware.user.can.capability', function ( $capability, $request ) {
    return 'edit_posts'; 
}, 10, 2 );
```

... the object ID:
```php
add_filter( 'wpemerge.middleware.user.can.object_id', function ( $object_id, $capability, $request ) {
    return 10; 
}, 10, 3 );
```

... and the final url:
```php
add_filter( 'wpemerge.middleware.user.can.redirect_url', function ( $url, $request ) {
    return 'http://example.com'; 
}, 10, 2 );
```
