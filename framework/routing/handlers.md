# Route Handlers

A route handler can be one of the following:
- An anonymous function.
- A reference in the `[ ClassName::class, 'METHOD_NAME' ]` format.
- A reference in the `'CLASS_NAME@METHOD_NAME'` format.

?> If the specified class name does not exist, by default, `\App\Controllers\Web\` is automatically prepended for convenience.

The following example will create a new instance of the `\App\Controllers\Web\HomeController` class and call its `index` method:

```php
\App::route()->get()->url( '/' )->handle( [ HomeController::class, 'index' ] );
// or ...
\App::route()->get()->url( '/' )->handle( 'HomeController@index' );
```

Refer to the [Controllers](/framework/routing/controllers) section for more information on route handlers.
