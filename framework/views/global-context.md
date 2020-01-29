# Global Context

You can pass variables to all views and partials by adding them as globals using `View::addGlobal()` or `View::addGlobals()`.

First, we need to create a service provider class:
```php
<?php

namespace App\View;

use WPEmerge\ServiceProviders\ServiceProviderInterface;

class ViewGlobalContextServiceProvider implements ServiceProviderInterface {
    /**
     * {@inheritDoc}
     */
    public function register( $container ) {
        // Nothing to register.
    }
  
    /**
     * {@inheritDoc}
     */
    public function bootstrap( $container ) {
        // Add one variable.
        View::addGlobal( 'foo', 'bar' );
        
        // Add many variables.
        View::addGlobals( [
            'name' => get_bloginfo( 'name' ),
            'url' => home_url( '/' ),
        ] );
    }
}
```

Second, we need to register that service provider in the configuration:
```php
\App::make()->bootstrap( [
    'providers' => [
        // ...
        \App\View\ViewGlobalContextServiceProvider::class,
    ],
    // ...
] );
```

Then, we can use the registered globals in any view:
```php
<?php echo $global['foo']; ?>
<?php echo $global['name']; ?>
<?php echo $global['url']; ?>
```
