# Configuration

When booting WP Emerge you can specify a number of configuration options:
```php
App::make()->bootstrap( [
    /**
     * Array of service providers you wish to enable.
     */
    'providers' => [
        // Examples:
        MyServiceProviders::class,
        // Blade example for htmlburger/wpemerge-blade:
        WPEmergeBlade\View\ServiceProvider::class,
    ],

    /**
     * Array of route group definitions and default attributes.
     * All of these are optional so if we are not using
     * a certain group of routes we can skip it.
     * If we are not using routing at all we can skip
     * the entire 'routes' option.
     */
    'routes' => [
        'web'   => [
            'definitions' => get_template_directory() . '/routes/web.php',
            'attributes'  => [],
        ],
        'admin' => [
            'definitions' => get_template_directory() . '/routes/admin.php',
            'attributes'  => [],
        ],
        'ajax'  => [
            'definitions' => get_template_directory() . '/routes/ajax.php',
            'attributes'  => [],
        ],
    ],

    /**
     * Register middleware class aliases.
     * Use fully qualified middleware class names.
     *
     * Internal aliases that you should avoid overriding:
     * - 'flash'
     * - 'old_input'
     * - 'csrf'
     * - 'user.logged_in'
     * - 'user.logged_out'
     * - 'user.can'
     */
    'middleware' => [
        // Examples:
        'mymiddleware' => \App\Middleware\MyMiddleware::class,
    ],

    /**
     * Register middleware groups.
     * Use fully qualified middleware class names or registered aliases.
     * There are a couple built-in groups that you may override:
     * - 'web'      - Automatically applied to web routes.
     * - 'admin'    - Automatically applied to admin routes.
     * - 'ajax'     - Automatically applied to ajax routes.
     * - 'global'   - Automatically applied to all of the above.
     * - 'wpemerge' - Internal group applied the same way 'global' is.
     *
     * Warning: The 'wpemerge' group contains some internal WP Emerge
     * middleware which you should avoid overriding.
     */
    'middleware_groups' => [
        'global' => [],
        'web'    => [
            // Examples:
            'mymiddleware',
        ],
        'ajax'   => [],
        'admin'  => [],
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
     * Custom directories to search for views.
     * Use absolute paths or leave blank to disable.
     * Applies only to the default PhpViewEngine.
     */
    'views' => [get_stylesheet_directory(), get_template_directory()],

    /**
     * View Composers settings.
     */
    'view_composers' => [
        // Default namespace.
        'namespace' => 'App\\ViewComposers\\',
    ],

    /**
     * Cache settings.
     */
    'cache' => [
        // Absolute path to custom default cache directory.
        // Extensions like Blade and Twig will use this value for their views cache
        // unless one has been specified for them.
        // Defaults to ROOT_DIR/wp-content/uploads/wpemerge/cache.
        'path' =>'/some/cache/directory/',
    ],

    /**
     * Debug settings.
     */
    'debug' => [
        // Enable debug mode. Defaults to the value of WP_DEBUG.
        'enable' => true,
        // Enable the use of filp/whoops for an enhanced error interface. Defaults to true.
        'pretty_errors' => true,
    ],
] )
```
