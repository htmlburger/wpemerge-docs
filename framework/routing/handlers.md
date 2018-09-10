# Route Handlers

A route handler can be an anonymous function or a reference in the `'CONTROLLER_CLASS@CONTROLLER_METHOD'` format.

The following example will create a new instance of the `HomeController` class and call its `index` method:

```php
Router::get( '/', 'HomeController@index' );

Router::get( '/', function( $request, $view ) {
    return app_view( $view );
} );
```

Refer to the [Controllers](framework/routing/controllers.md) section for more information on route handlers.
