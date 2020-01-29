# Route Conditions

## URL

Match against a specific path:

```php
\App::route()->get()->url( '/foo/bar/' )->handle( $handler );
```

?> Paths in URL conditions are relative to the site's home url.

!> You should use `query()` on routes which do not match any valid WordPress URL or your WP Query will consider the request as a `404 - Not Found`. You can find more information on this in the dedicated [Query API](https://wpemerge.com/2018/11/15/a-quick-look-into-wp-emerges-new-route-query-api/) blog post.

---

Use parameters in the path:

```php
\App::route()->get()
    ->url(
        '/foo/{param1}/bar/{param2?}/baz/{param3}/{param4?}',
        [
            'param3' => '/^\d+$/',
            'param4' => '/^\d+$/',
        ]
      )
    ->handle( function( $request, $view, $param1, $param2, $param3, $param4 ) {
        // ...
    } );
);
```

- `param1` - required, matches everything.
- `param2` - optional, matches everything.
- `param3` - required, matches a custom regular expression.
- `param4` - optional, matches a custom regular expression.

?> Parameter values are passed as arguments to the handler method.

---

Filter the WP_Query parameters for your route:

```php
\App::route()->get()
    ->url( '/foo/bar/{page_id}' )
    ->query( function ( $query_vars, $page_id ) {
        return [
            'page_id' => intval( $page_id ),
        ];
    } )
    ->handle( $handler );
```

?> Route parameters will be passed as additional arguments to the callable you supply to `->query()`.

---

Match __any__ url:

```php
\App::route()->get()->url( '*' )->handle( $handler );
```

?> `\App::route()->all()` uses this internally.

## Admin

Match against a custom WordPress admin page.

!> WP Emerge will not register any pages when you use this condition - you have to register those pages yourself using [add_menu_page()](https://developer.wordpress.org/reference/functions/add_menu_page/) or [add_submenu_page()](https://developer.wordpress.org/reference/functions/add_submenu_page/).

```php
\App::route()->get()->where( 'admin', 'my-menu-page' )->handle( $handler );

\App::route()->get()->where( 'admin', 'my-submenu-page', 'my-menu-page' )->handle( $handler );
```

## AJAX

Match against an WordPress AJAX request:

```php
// Match requests from authenticated users only:
\App::route()->get()->where( 'ajax', 'my-ajax-action', true, false )->handle( $handler );

// Match requests from unauthenticated users only:
\App::route()->get()->where( 'ajax', 'my-ajax-action', false, true )->handle( $handler );

// Match requests from any user:
\App::route()->get()->where( 'ajax', 'my-ajax-action', true, true )->handle( $handler );
```

## Post ID

Match against the current post id:

```php
\App::route()->get()->where( 'post_id', 10 )->handle( $handler );
```

## Post slug

Match against the current post slug:

```php
\App::route()->get()->where( 'post_slug', 'about-us' )->handle( $handler );
```

## Post template

Match against the current post template:

```php
\App::route()->get()->where( 'post_template', 'templates/contact-us.php' )->handle( $handler );
```

## Post status

Match against the current post status:

```php
\App::route()->get()->where( 'post_status', 'publish' )->handle( $handler );
```

## Post type

Match against the current post type:

```php
\App::route()->get()->where( 'post_type', 'product' )->handle( $handler );
```

## Query var

Match when a specified query var is present (any value is accepted):

```php
\App::route()->get()->where( 'query_var', 's' )->handle( $handler );
```

This is especially useful when dealing with custom endpoints ([add_rewrite_endpoint()](https://codex.wordpress.org/Rewrite_API/add_rewrite_endpoint)):

```php
add_action( 'init', function() {
    // remember to refresh your rewrite rules!
    add_rewrite_endpoint( 'my_custom_endpoint', EP_PAGES );
} );

// ...

\App::route()->get()->where( 'query_var', 'my_custom_endpoint' )->handle( $handler );
```

When combined with the post template condition, you can create pages that optionally receive additional parameters in the url using clean url "/sections/" instead of query arguments:

```php
add_action( 'init', function() {
    // remember to refresh your rewrite rules!
    add_rewrite_endpoint( 'secret', EP_PAGES );
} );

// ...

\App::route()->get()
    ->where( 'post_template', 'templates/page-with-secret.php' )
    ->where( 'query_var', 'secret' )
    ->handle( $handler );
```

You can match with a specific value of the query var as well:

```php
\App::route()->get()
    ->where( 'query_var', 'some_query_var_name', 'some_query_var_value' )
    ->handle( $handler );
```

## Custom

The custom condition allows you to add a callable which must return a boolean (whether the route has matched the current request or not):

```php
\App::route()->get()
    ->where( function() {
        $my_condition = true; // your custom code here
        return $my_condition;
    } )
    ->handle( $handler );
```

---

You can also pass parameters to use built-in callables:

```php
\App::route()->get()
    ->where( 'is_tax', 'app_custom_taxonomy' )
    ->handle( $handler );
```

Any parameters you pass will be provided to both the callable AND the `$handler`:

```php
\App::route()->get()
    ->where( 'is_tax', 'app_custom_taxonomy' )
    ->handle( function( $request, $view, $taxonomy ) {
        // $taxonomy is passed after $request and $view which are always passed to handlers.
    } );
```

This works with anonymous functions as well, which can be used to reduce duplication:

```php
\App::route()->get()
    ->where(
        function( $foo, $bar ) {
            // $foo and $bar are available here.
            return true;
        },
        'foo',
        'bar'
    )
    ->handle( function( $request, $view, $foo, $bar ) {
        // ... and here!
    } );
```

!> This use-case is a bit hard to read - exact same usage is not advisable.

## Multiple

To match multiple conditions specify them one after the other:

```php
\App::route()->get()
    ->where( 'is_tax', 'app_custom_taxonomy' )
    ->where( function() {
        return true;
    } )
    ->handle( $handler );
```

Multiple conditions will also pass ALL arguments to the handler following the definition order.

## Negate

The negate condition allows you to negate another condition's result. The following example will match any request as long as it is not for the singular view of the post with id of 3:

```php
\App::route()->get()->where( '!post_id', 3 )->handle( $handler ); // notice the exclamation mark.
```

?> The negate condition will also pass whatever arguments its child condition passes to the handler.

---

Since not all conditions are defined using strings, here's the full syntax which you can use for any condition:

```php
// Negate a single condition:
\App::route()->get()
    ->where( 'negate', 'post_id', 3 )
    ->handle( $handler );

// Negate a custom condition:
\App::route()->get()
    ->where( 'negate', function() {
      return false;
    } )
    ->handle( $handler );

// Negate multiple conditions:
\App::route()->get()
    ->where( 'negate', [
      ['is_tax', 'app_custom_taxonomy'],
      [function() {
          return true;
      }],
    ] )
    ->handle( $handler );
```

## Extending Conditions

You can define your own custom condition classes that you can reuse throughout your project. For more information on how to do this please refer to the [Extending](/framework/extending/overview) section.
