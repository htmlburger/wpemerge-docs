# Views

WP Emerge comes with a default view engine built-in - `\WPEmerge\View\PhpViewEngine`.
This view engine uses `extract()` for the view context and then includes the view file. The resulting output is then passed as the rendered view string.
Essentially, this engine loads views in the same way WordPress does, but with a couple extra features:

1. Context variable passing.
1. Ability to specify layouts for views.

## External view engines

### WP Emerge Blade

Renders your views using Blade: https://github.com/htmlburger/wpemerge-blade

### WP Emerge Twig

Renders your views using Twig: https://github.com/htmlburger/wpemerge-twig

## Other built-in view engines

### NameProxyViewEngine

WP Emerge also comes with a small utility view engine which delegates view rendering to other engines depending on the view name suffix.
The main use-case for it is to allow you to use multiple view engines so you can migrate to a new one on a view-by-view basis instead of forcing you to rewrite all of your views.

Replacing the default view engine:
```php
// Run this inside the register() method of a service provider.
$container[ WPEMERGE_VIEW_ENGINE_KEY ] = function( $container ) {
    return new \WPEmerge\View\NameProxyViewEngine(
        $container[ WPEMERGE_APPLICATION_KEY ],
        [
            // Format: view name suffix => service container key for alternative engine.
    
            // Use Twig for twig.php views.
            '.twig.php' => WPEMERGETWIG_VIEW_TWIG_VIEW_ENGINE_KEY,
    
            // Use Blade for .blade.php views.
            '.blade.php' => WPEMERGEBLADE_VIEW_BLADE_VIEW_ENGINE_KEY,
    
            // Use default PHP engine for .php views. 
            '.php' => WPEMERGE_VIEW_PHP_VIEW_ENGINE_KEY,
    
            // Use Blade for all other cases as blade views can be referenced
            // in blade.format.as.well without an extension.
        ],
        WPEMERGEBLADE_VIEW_BLADE_VIEW_ENGINE_KEY
    );
};
```
!> The example above assumes you have included both WP Emerge Twig and WP Emerge Blade composer packages. 
`WPEMERGETWIG_VIEW_TWIG_VIEW_ENGINE_KEY` and `WPEMERGEBLADE_VIEW_BLADE_VIEW_ENGINE_KEY` are not provided by default.

## Implementing other View Engines

Implementing your own or a third-party engine is straightforward - there are only a couple requirements:

1. Your class must implement the `\WPEmerge\View\ViewEngineInterface` interface
    ```php
    use \WPEmerge\View\ViewEngineInterface;

    class MyCustomViewEngine implements ViewEngineInterface {
        // ...
    }
    ```

2. You must use a service provider class to register your new view engine:
    ```php
    use \WPEmerge\ServiceProviders\ServiceProviderInterface;

    class MyCustomServiceProvider implements ServiceProviderInterface {
        public function register( $container ) {
            $container['my_custom_view_engine'] = function ( $c ) {
                return new MyCustomViewEngine();
            };
        }
        // ...
    }
    ```
3. ... and to replace the built-in engine:
    ```php
    use \WPEmerge\ServiceProviders\ServiceProviderInterface;

    class MyCustomServiceProvider implements ServiceProviderInterface {
        public function register( $container ) {
            $container['my_custom_view_engine'] = function ( $c ) {
                return new MyCustomViewEngine();
            };

            $container[ WPEMERGE_VIEW_ENGINE_KEY ] = function ( $c ) {
                return $c['my_custom_view_engine'];
            };
        }
        // ...
    }
    ```
4. Finally, register your service provider with WP Emerge:
    ```php
    WPEmerge::bootstrap( [
        'providers' => [
            // ... other providers
            MyCustomServiceProvider::class,
        ],
        // ... other options
    ] );
    ```
