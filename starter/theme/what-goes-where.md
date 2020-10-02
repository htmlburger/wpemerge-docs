# What goes where

Starting a new project can be overwhelming as every feature has its cognitive cost. Even the basic act of navigating through a codebase and knowing what goes where can be a challenge. That is why this handy guide will point you to the right place to do everything.

?> If you feel comfortable with service providers, you can split up self-contained logical modules into their own `app/src/*/` directories with their own service providers which register services, post types, taxonomies, actions, filters etc. instead of using the common places listed below. __This approach can make your code more reusable and testable but it is not mandatory__.

## One-off functions

?> `/app/helpers/{relevant-filename}.php`

The best place to add a simple function would be in a suitable file inside the `helpers` directory. If such a file does not exist don't be afraid to add one yourself, just make sure you `require_once` the new file in `app/helpers.php`.

## One-off actions and filters

?> `/app/hooks.php`

The bast place to add simple actions or filters is the dedicated `hooks.php` file. Avoid defining functions here - use `add_action()` and `add_filter()` only.

## Views (templates)

?> `/` (theme root)

WordPress core templates such as `header.php`, `index.php` etc. go here.

?> `/views/`

Named [custom post templates](https://developer.wordpress.org/themes/template-files-section/page-template-files/#creating-custom-page-templates-for-global-use) or templates that don't fit anywhere else go here.

?> `/views/layouts/`

Layouts that other templates extend via the `Layout` file header comment go here.

?> `/views/partials/`

Small template snippets that are meant to be reused throughout other templates go here.

## Styles

?> `/resources/styles/[frontend|admin|login|editor]/`

Styles for the frontend, admin, login and editor go in the respective directory.  
Since the starter theme uses SASS you can add as many files as you need and import them in the main `index.scss` file (or any other file that is imported in it) and they will be bundled up into a single optimized CSS file. You can even include them via a wildcard (e.g. `@import './module-*.scss';`).

## Scripts

?> `/resources/scripts/[frontend|admin|login|editor]/`

Scripts for the frontend, admin, login and editor go in the respective directory.  
Since the starter theme uses Webpack you can add as many files as you need and include them in the main `index.js` file (or any other file that is included in it) and they will be bundled up into a single optimized JS file. You can even include them via a wildcard (e.g. `import './modules/*.js';`).

## Images

Images that are part of your site's content, such as blog post featured images, site logo etc. should be uploaded to your Media library. Other images go into:

?> `/resources/images/`

For images that are necessary for styling purposes, backgrounds etc.

?> `/resources/images/sprite/`

For images that are going to be bundled into a single [raster sprite](/starter/assets/sprites.md?id=raster-sprite).

?> `/resources/images/sprite-svg/`

For SVG files that are going to be bundled into a single [SVG sprite](/starter/assets/sprites.md?id=svg-sprite).

## Fonts

?> `/resources/fonts/`

Self-hosted fonts go here. You can reference a font stored in this directory via the `~@fonts/` [alias](/starter/assets/overview?id=importing-assets) when using a `@font-face` declaration.

## WordPress Admin Pages

?> `/app/src/WordPress/AdminServiceProvider.php`

Any calls to `add_menu_page()`, `add_theme_page()` etc. go here.

## Enqueueing scripts and styles

?> `/app/src/WordPress/AssetsServiceProvider.php`

## Custom post types and taxonomies

?> `/app/src/WordPress/ContentTypesServiceProvider.php`

## Menu locations

?> `/app/src/WordPress/MenusServiceProvider.php`

## Shortcodes

?> `/app/src/WordPress/ShortcodesServiceProvider.php`

Bonus points: You may want to create a new `views/shortcodes/` directory and add your shortcodes as `.php` files inside it which your shortcode service provider can return like this:
```php
function shortcodeExample( $atts, $content ) {
    $atts = shortcode_atts(
        array(
            'example_attribute' => 'example_value',
        ),
        $atts,
        'example'
    );

    return \App::view( 'views/shortcodes/my-shortcode.php' )
        ->with( $atts )
        ->with( 'content', $content )
        ->toString();
}
```

## Theme support

?> `/app/src/WordPress/ThemeServiceProvider.php`

## Widgets and sidebars

?> `/app/src/WordPress/WidgetsServiceProvider.php`

## Custom classes, controllers, middleware etc.

?> `/app/src/*`

Custom classes are autoloaded according to PSR-4 so make sure you name them correctly and place them in the directory that is appropriate to their namespace e.g.:

| Class                         | File                              |
|-----------------------------  |---------------------------------- |
| `App\MyClass`                 | `app/MyClass.php`                 |
| `App\Controllers\Web\Home`    | `app/Controllers/Web/Home.php`    |
| `App\ViewComposers\UserBadge` | `app/ViewComposers/UserBadge.php` |

You can find more information about PSR-4 autoloading at http://www.php-fig.org/psr/psr-4/
