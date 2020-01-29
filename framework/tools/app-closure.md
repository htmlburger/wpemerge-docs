# \App::closure()

This utility provides a couple tools to help out with using the container outside of the normal WP Emerge flow.

## \App::closure()->value( $key )

Make a closure that resolves a value from the container:
```php
$closure = \App::closure()->value( 'my_service' );
$my_service = $closure();
```

## \App::closure()->method( $key, $method )

Make a closure that resolves a class instance from the container and calls one of its methods.
Useful if you need to pass a callable to an API without container support such as the REST API:
```php
register_rest_route( 'myplugin/v1', '/author/(?P<id>\d+)', [
    'methods' => 'GET',
    'callback' => \App::closure()->method( 'my_service', 'my_method' ),
] );
```
