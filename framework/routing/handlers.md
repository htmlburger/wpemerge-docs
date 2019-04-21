# Route Handlers

A route handler can be an anonymous function or a reference in the `'CLASS_NAME@METHOD_NAME'` format with automatically prepended `\App\Controllers\` to the class name for convenience.

The following example will create a new instance of the `HomeController` class and call its `index` method:

```php
Route::get()->url( '/' )->handle( 'HomeController@index' );
```

Refer to the [Controllers](framework/routing/controllers.md) section for more information on route handlers.
