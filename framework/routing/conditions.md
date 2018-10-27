# Route Conditions

## URL

Match against a specific path:

```php
Router::get( '/foo/bar/', $handler );
```

?> Paths in URL conditions are relative to the site's home url.

!> You should use `->query()` on routes which do not match any valid WordPress URL or your WP Query will consider the request as a `404 - Not Found`.

---

Use parameters in the path:

```php
Router::get(
    '/foo/{param1}/bar/{param2?}/baz/{param3:\d+}/{param4?:\d+}',
    function( $request, $view, $param1, $param2, $param3, $param4 ) {
        // ...
    }
);
```

- `param1` - required, matches everything
- `param2` - optional, matches everything
- `param3` - required, matches a custom regex
- `param4` - optional, matches a custom regex

?> Parameter values are passed as arguments to the handler method.

---

Filter the WP_Query parameters for your route:

```php
Router::get( '/foo/bar/{page_id}', $handler )
    ->query( function ( $query_vars, $page_id ) {
        return [
            'page_id' => intval( $page_id ),
        ];
    } );
```

?> Route parameters will be passed as additional arguments to the callable you supply to `->query()`.

---

Match __any__ url:

```php
Router::get( '*', $handler );
```

?> `Router::handleAll()` uses this internally.

## Post ID

Match against the current post id:

```php
Router::get( ['post_id', 10], $handler );
```

## Post slug

Match against the current post slug:

```php
Router::get( ['post_slug', 'about-us'], $handler );
```

## Post template

Match against the current post template:

```php
Router::get( ['post_template', 'templates/contact-us.php'], $handler );
```

## Post status

Match against the current post status:

```php
Router::get( ['post_status', 'publish'], $handler );
```

## Post type

Match against the current post type:

```php
Router::get( ['post_type', 'product'], $handler );
```

## Query var

Match when a specified query var is present (any value is accepted):

```php
Router::get( ['query_var', 's'], $handler );
```

This is especially useful when dealing with custom endpoints ([add_rewrite_endpoint()](https://codex.wordpress.org/Rewrite_API/add_rewrite_endpoint)):

```php
add_action( 'init', function() {
    // remember to refresh your rewrite rules!
    add_rewrite_endpoint( 'my_custom_endpoint', EP_PAGES );
} );

// ...

Router::get( ['query_var', 'my_custom_endpoint'], $handler );
```

When combined with the post template condition, you can create pages that optionally receive additional parameters in the url using clean url "/sections/" instead of query arguments:

```php
add_action( 'init', function() {
    // remember to refresh your rewrite rules!
    add_rewrite_endpoint( 'secret', EP_PAGES );
} );

...

Router::get( [
    ['post_template', 'templates/page-with-secret.php'],
    ['query_var', 'secret'],
], $handler );
```

You can match with a specific value of the query var as well:

```php
Router::get( ['query_var', 'some_query_var_name', 'some_query_var_value'], $handler );
```

## Custom

The custom condition allows you to add a callable which must return a boolean (whether the route has matched the current request or not):

```php
Router::get( function() {
    $my_condition = true; // your custom code here
    return $my_condition;
}, $handler );
```

?> When using the array syntax, adding `'custom'` literally is optional and all examples will not use it for simplicity.

---

You can also pass parameters to use built-in callables:

```php
Router::get( ['is_tax', 'app_custom_taxonomy'], $handler );
```

Any parameters you pass will be provided to both the callable AND the $handler:

```php
Router::get( ['is_tax', 'app_custom_taxonomy'], function( $request, $view, $taxonomy ) {
    // $taxonomy is passed after $request and $view which are always passed to handlers
} );
```

This works with anonymous functions as well, which can be used to reduce duplication:

```php
Router::get( [function( $foo, $bar ) {
    // $foo and $bar are available here
    return true;
}, 'foo', 'bar'], function( $request, $view, $foo, $bar ) {
    // ... and here!
} );
// you may notice this use-case is a bit hard to read - exact same usage is not advisable
```

## Multiple

The multiple condition allows you to specify an array of conditions which must ALL match:

```php
Router::get( ['multiple', [
    ['is_tax', 'app_custom_taxonomy'],
    [function() {
        return true;
    }],
]], $handler );
```

The multiple condition will also pass ALL arguments that its child conditions provide to the handler following the child conditions definition order.

The syntax can also be simplified by directly passing an array of conditions:

```php
Router::get( [
    ['is_tax', 'app_custom_taxonomy'],
    [function() {
        return true;
    }],
], $handler );
```

## Negate

The negate condition allows you to negate another condition's result. The following example will match any request as long as it is not for the singular view of the post with id of 3:

```php
Router::get( ['!post_id', 3], $handler ); // notice the exclamation mark
```

?> The negate condition will also pass whatever arguments its child condition passes to the handler.

---

Since not all conditions are defined using strings, here's the full syntax which you can use for any condition:

```php
Router::get( ['negate', 'post_id', 3], $handler );
```

You can also use it with any of the simplified syntaxes of other conditions:

```php
Router::get( ['negate', function() {
    return false;
], $handler );

Router::get( ['negate', [
    ['is_tax', 'app_custom_taxonomy'],
    [function() {
        return true;
    }],
], $handler );
```

## Extending Conditions

You can define your own custom condition classes that you can reuse throughout your project. For more information on 
how to do this please refer to the [Extending](framework/extending/overview.md) section.
