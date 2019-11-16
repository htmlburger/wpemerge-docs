# Route Methods

The method you call when you create a route defines which request method the route will match:

```php
Route::[get|post|put|patch|delete|options|any]();
```

If you wish to match a specific set of methods you can also use the generic `Route::methods()` method:

```php
Route::methods( ['GET', 'HEAD', 'POST'] );
```

## Handling all requests

By default, WP Emerge will only handle the requests which match its routes so you can implement it only where you need to (even on legacy projects). However, you can also handle all requests if you wish to do so:
```php
// Add this AFTER all of your route definitions
Route::all();
```

Adding this will initially seem like it makes no difference because WP Emerge will make sure requests produce the same results as normal WordPress requests would, however, there are a couple notable differences:

1. Global middleware will be applied to ALL requests (since this declaration will match all requests)
2. All views will be rendered using WP Emerge's current view engine. By default there will be no difference, however, if you use a different view engine all WordPress templates will be rendered through it.

    _Check out [NameProxy](/framework/views/overview#nameproxyviewengine) if you wish to have mixed views._
