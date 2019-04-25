# Defining Routes

There are 3 different groups of routes in WP Emerge - `web`, `admin` and `ajax`.
- `web` - Loaded for front-end requests.
- `admin` - Loaded for `/wp-admin/` requests, except `/wp-admin/admin-ajax.php`.
- `ajax` - Loaded for `/wp-admin/admin-ajax.php` requests. 

!> You should almost always avoid using `Route::all()` when defining `admin` or `ajax` routes otherwise you will take over all custom admin pages and/or AJAX requests (even ones created by third party plugins).

## Configuration

To define your desired routes you should create a separate file for each group you intend to use, adding the absolute file path to the configuration:
```php
WPEmerge::bootstrap( [
    'routes' => [
        // Assuming your route files are created in /wp-content/themes/my-theme/routes/
        'web'   => get_template_directory() . '/routes/web.php',
        'admin' => get_template_directory() . '/routes/admin.php',
        'ajax'  => get_template_directory() . '/routes/ajax.php',
    ],
    // ... other options go here
] );
```

?> Route files are optional so if you will not be using any `admin` routes you can omit the file from the configuration and skip creating it entirely.

Routes defined in this way will automatically have the middleware group of the same name applied to them so if you wish to have some custom middleware applied to all of your `web` routes, for example, you can add them to the `web` middleware group in the `middleware_groups` configuration option. You can read more on the subject in the [Configuration](/framework/configuration) and [Middleware](/framework/routing/middleware) articles.

## Route Definition

A typical route definition consists of several attribute declarations and a finalizer, for example:
```php
Route::get()->url( '/' )->handle( 'HomeController@index' );
```
Let's break it down:
  - `Route::` - The Route facade provides you with tools to create and register your routes. All of your routes will start this way.
  - `get()` - Shorthand for `methods( ['GET'] )`. See below for more information on the `methods` attribute.
  - `url( '/' )` - Shorthand for `where( 'url', '/' )`. See below for more information on `where` attribute.
  - `handle( 'HomeController@index' )` - Finalize the definition as a single route which is handled by `HomeController@index` when its conditions are satisfied.
  
!> Not finalizing a route with `handler()` or `group()` will invalidate the route.

!> Setting the same attribute multiple times will merge it. Take a loot at every attribute below to see how merging happens.

## Route Groups

Route groups represent a collection of attributes that should be applied to all routes and child groups within that group.
To create a group finalize a route definition with `group()` instead of `handle()` like this:
```php
Route::url( '/dashboard/' )->group( function () {
    // Group routes go here, for example:
    Route::get()->url( '/profile/' )->handle( 'DashboardController@profile' );
    Route::get()->url( '/orders/' )->handle( 'DashboardController@orders' );
    // ...
} );
```

In the above example we define 2 routes matching GET requests for `/dashboard/profile/` and `/dashboard/orders/`. As you can see, the URL we defined for the group gets merged with the URL for each individual route.

?> Instead of an anonymous function you can also pass an absolute path to a file which contains your route definitions for that group.

## Attributes

### Methods

The `methods` attribute defines which request methods a route should match:
```php
Route::methods( ['GET', 'POST'] )->...
``` 

There are also shorthand methods for every request method type (and even any request method):
```php
Route::get()->...
Route::post()->...
Route::put()->...
Route::patch()->...
Route::delete()->...
Route::options()->...
Route::any()->...
```

#### Merging Methods

When 2 `methods` attributes are merged, their values get added together:
```php
Route::get()->post()->...
// is equal to:
Route::methods( ['GET', 'POST'] )->...
```

### Where

The `where` attribute defines the condition(s) under which the route should be satisfied. For example, if we wish to have a route that matches when a user visits the `/dashboard/` URL we can use the built-in `url` condition:
```php
Route::where( 'url', '/dashboard/' )->...
```
There's also a shorthand available:
```php
Route::url( '/dashboard/' )->...
// is equal to:
Route::where( 'url', '/dashboard/' )->...
```

You can find a full list of built-in conditions in the [Conditions](/framework/routing/conditions) article. 

#### Merging Where

When 2 `where` attributes are merged, their values get added together into a `multiple` condition which requires all of its conditions to be satisfied. The only exception is the `url` condition which gets concatenated with other `url` conditions.
```php
Route::url( '/dashboard/' )->url( '/profile/' )...
// is equal to:
Route::where( 'url', '/dashboard/profile/' )->...

Route::where( 'post_type', 'page' )->where( 'post_slug', 'dashboard' )...
// is equal to:
Route::where( [
    ['post_type', 'page'],
    ['post_slug', 'dashboard'],
] )->...
```

### Middleware

The `middleware` attributes defines the middleware that should be applied to the route:
```php
Route::middleware( 'auth' )->...
Route::middleware( ['auth', 'cache'] )->...
```

#### Merging Middleware

When 2 `middleware` attributes are merged, their values get added together:
```php
Route::middleware( 'auth' )->middleware( 'cache' )->...
// is equal to:
Route::middleware( ['auth', 'cache'] )->...
```

### Query

The `query` attribute defines a filter that should be applied to the query vars passed to the main `WP_Query` instance when making front-end requests:
```php
Route::query( function ( $query_vars ) {
    return array_merge( $query_vars, [
        // For example, change the number of posts for the current request to 20:
        'posts_per_page' => 20,
    ] );
} )->...
``` 

!> Since almost all conditions rely on the main query, only routes using the `url` condition can have this attribute applied.

#### Merging Query

When 2 `query` attributes are merged, their filters will be executed in the order that they are defined (the latter receiving the filtered value from the former):
```php
Route::query( $filter1 )->query( $filter2 )->...
// is equal to:
Route::query( function ( $query_vars ) {
    $query_vars = $filter1( $query_vars );
    $query_vars = $filter2( $query_vars );
    return $query_vars;
} )->...
```

### Namespace

The `namespace` attribute defines what namespace should be automatically prepended to the route handler:
```php
Route::setNamespace( '\\App\\Controllers\\Dashboard\\' )->...
``` 

?> The `namespace` attribute is set using `setNamespace` as PHP<7 does not allow method names to match reserved keywords.

This is especially useful if you have namespaces for different route groups, for example:
```php
Route::url( '/dashboard/' )
    ->setNamespace( '\\App\\Controllers\\Dashboard\\' )
    ->group( function () {
        // Match /dashboard/profile/ ...
        Route::get( '/profile/' )->handle( 'Profile@index' );
        // ... using \App\Controllers\Dashboard\Profile@index as the handler.
    } );
```

#### Merging Namespace

When 2 `namespace` attributes are merged, the latter will override the former completely:
```php
Route::setNamespace( '\\App\\First\\' )->setNamespace( '\\App\\Second\\' )->...
// is equal to:
Route::setNamespace( '\\App\\Second\\' )->...
```
