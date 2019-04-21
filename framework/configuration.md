# Configuration

When booting WP Emerge you can specify a number of configuration options:
```php
WPEmerge::boot( [
    /**
     * Array of classes that implement \WPEmerge\ServiceProviders\ServiceProviderInterface.
     */
    'providers' => [
        // Examples:
        MyServiceProviders::class,
        // Blade example for htmlburger/wpemerge-blade:
        WPEmergeBlade\View\ServiceProvider::class,
    ],

    /**
     * Register middleware class aliases.
     * Use fully qualified middleware class names.
     */
    'middleware' => [
        // Examples:
        'mymiddleware' => \App\Middleware\MyMiddleware::class,
    ],

    /**
     * Register middleware groups.
     * Use fully qualified middleware class names or registered aliases.
     * There are a couple built-in groups that you may override:
     * - 'web'    - Automatically applied to web routes.
     * - 'admin'  - Automatically applied to admin routes.
     * - 'ajax'   - Automatically applied to ajax routes.
     * - 'global' - Automatically applied to all of the above.
     *
     * Warning: The 'global' group contains some internal WP Emerge
     * middleware which you should avoid overriding.
     */
    'middleware_groups'   => [
        'web'   => [
            // Examples:
            'mymiddleware',
        ],
        'ajax'  => [],
        'admin' => [],
    ],

    /**
     * Optionally specify middleware execution order.
     * Use fully qualified middleware class names.
     * Middlewares not in this list will execute after all middlewares in
     * it in the order they were assigned.
     */
    'middleware_priority' => [
        // Examples:
        \App\Middleware\MyMiddlewareThatShouldRunFirst::class,
        \App\Middleware\MyMiddlewareThatShouldRunSecond::class,
    ],

    /**
     * Custom directory to search for views.
     * Use an absolute path or leave blank to enable default.
     * Applies only to the default PhpViewEngine as other engines
     * have their own setting.
     */
    'views' => '',

    /**
     * Debug settings.
     */
    'debug' => [
        // Enable the use of filp/whoops for an enhanced error interface. Defaults to true.
        'pretty_errors' => true,
    ],
] )
```
