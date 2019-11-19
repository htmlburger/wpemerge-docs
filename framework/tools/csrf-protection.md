# CSRF Protection

WP Emerge comes with an experimental CSRF protection middleware (disabled by default) which employs WordPress nonces.

The middleware will check for a CSRF token in the following places:
- `__wpemergeCsrfToken` in `$_GET`.
- `__wpemergeCsrfToken` in `$_POST`.
- The `X-CSRF-TOKEN` header.

If the middleware cannot find a valid token in a non-read request (e.g. `POST`, `PUT`, `PATCH`, `DELETE`) it will show the default WordPress ["Are you sure?"](https://codex.wordpress.org/Function_Reference/wp_nonce_ays) screen.

## Adding the middleware

Here's how to add the middleware to a route:
```php
Route::post()->middleware( 'csrf' )->...
```

Here's how to add the middleware with a custom nonce action:
```php
Route::post()->middleware( 'csrf:custom_action_goes_here' )->...
```

It is not advisable to use a single nonce for your entire site but here's how to do it if the need arises:
```php
WPEmerge::bootstrap( [
    // ...
    'middleware_groups' => [
        'global' => ['csrf:custom_action'],
        // ...
    ],
    // ...
] );
```

## Using the token

To output a hidden CSRF token field to your forms:
```html
<form>
    <?php Csrf::field(); ?>
    <!-- or -->
    <?php Csrf::field( 'custom_action' ); ?>
</form>
```

To add the token to a url:
```php
$url = Csrf::url( $url );
// or
$url = Csrf::url( $url, 'custom_action' );
```

To get the token directly:
```php
$token = Csrf::getToken();
// or
$token = Csrf::getToken( 'custom_action' );
```
