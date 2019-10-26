# [![WPEmerge.com](../_images/wpemerge-logo-bar.png ':size=autox61')](http://wpemerge.com)

[![Packagist](https://img.shields.io/packagist/vpre/htmlburger/wpemerge.svg?style=flat-square&colorB=0366d6)](https://packagist.org/packages/htmlburger/wpemerge) [![Travis branch](https://img.shields.io/travis/htmlburger/wpemerge/master.svg?style=flat-square)](https://travis-ci.org/htmlburger/wpemerge/builds) [![Scrutinizer](https://img.shields.io/scrutinizer/g/htmlburger/wpemerge.svg?style=flat-square)](https://scrutinizer-ci.com/g/htmlburger/wpemerge/) [![Scrutinizer Coverage](https://img.shields.io/scrutinizer/coverage/g/htmlburger/wpemerge.svg?style=flat-square)](https://scrutinizer-ci.com/g/htmlburger/wpemerge/code-structure/master/code-coverage) [![Gitter](https://img.shields.io/gitter/room/nwjs/nw.js.svg?style=flat-square&colorB=7d07d1)](https://gitter.im/wpemerge/Lobby)

📦 A micro framework which modernizes WordPress as a CMS development by providing tools to implement MVC and more.

🔥 Integration is incremental - you can use it only on the pages you want or on all pages.

❤ If you've used frameworks such as Laravel, Slim or Symfony you will love WP Emerge.

🚀 Also, make sure you check out the [WP Emerge Starter Theme](https://github.com/htmlburger/wpemerge-theme) project.

## Summary

- [Documentation](#documentation)
- [API Reference](#api-reference)
- [Development Team](#development-team)
- [Comparison Table](#comparison-table-¹-²)
- [Requirements](#requirements)
- [Features](#features)
- [Contributing](#contributing)

## Documentation

[http://docs.wpemerge.com/#/framework/overview](http://docs.wpemerge.com/#/framework/overview)

[http://docs.wpemerge.com/#/framework/quickstart](http://docs.wpemerge.com/#/framework/quickstart)

## API Reference

[https://api.wpemerge.com/](https://api.wpemerge.com/)

## Development Team

Brought to you by [Atanas Angelov](https://github.com/atanas-angelov-dev) and the lovely folks at [htmlBurger](http://htmlburger.com).

## Comparison Table ¹ ²

|  | WPEmerge | Sage | Timber |
| --- | --- | --- | --- |
| View Engine | PHP, Blade, Twig, any | PHP, Blade | Twig |
| Routing | ✔ | ✖ | ✔ |
| WP Admin Routing | ✔ | ✖ | ✖ |
| WP AJAX Routing | ✔ | ✖ | ✖ |
| MVC | ✖✔✔ | ✖✔✖³ | ✖✔✖ |
| Middleware | ✔ | ✖ | ✖ |
| View Composers | ✔ | ✔/✖⁴ | ✖ |
| Service Container | ✔ | ✔ | ✖ |
| [Advanced Error Reporting](/framework/routing/error-handling) | ✔ | ✖ | ✖ |

_¹ We are comparing frameworks and themes - style, build tools etc. are not mentioned. For a full comparison check out the [WP Emerge Starter Theme](/starter-theme/overview)._

_² WP Emerge is theme agnostic - you can use it in any theme._

_³ Sage's Controller is more of a View Composer than a Controller._

_⁴ Sage's Controller provides similar functionality but is limited to 1 composer (controller) per view and vice versa._

_Email any factual inaccuracies to [hi@atanas.dev](mailto:hi@atanas.dev) so they can be corrected._

## Requirements

- [PHP](http://php.net/) >= 5.5
- [WordPress](https://wordpress.org/) >= 4.7
- [Composer](https://getcomposer.org/)

## Features

#### Routes with custom URLs or dynamic conditions

```php
Route::get()->url( '/' )->handle( 'HomeController@index' );

Route::get()
    ->url( '/custom' )
    ->query( function ( $query_vars ) {
        return [
            // WP_Query query vars go here ...
        ];
    } )
    ->handle( 'CustomController@custom' );

Route::get()
    ->where( 'post_id', get_option( 'page_on_front' ) )
    ->handle( 'HomeController@index' );

Route::get()
    ->where( function() {
        return is_front_page();
    } );
    ->handle( 'HomeController@index' );
```

- Enable the use of controllers to compartmentalize your business logic away from your presentation.
- Use existing routes or add new ones.
- Use built-in dynamic route conditions or define your own custom ones.
- Use anonymous functions for quick one-off conditions.

---

#### Controllers

```php
class HomeController {
    public function index( $request ) {
        $name = $request->get( 'name' );
        return \WPEmerge\view( 'templates/home.php' )
            ->with( [
                'welcome' => 'Welcome, ' . $name . '!',
            ] );
    }
}
```

- Separate unrelated business logic into controllers, and related business logic into controller methods.
- Receive an object representing the current request and respond with a PSR-7 response.
- Use different methods for different routes.
- Respond with a view, json, a redirect etc.
- Easy to test.

---

#### Middleware

```php
Route::get()
    ->url( '/' )
    ->middleware( ['user.can:manage_options', 'minify'] )
    ->handle( 'HomeController@index' );
```

- Hook before and/or after controller methods.
- Add globally or to specific routes or route groups.
- Powers features such as Flash and OldInput.

---

#### [PSR-7](http://www.php-fig.org/psr/psr-7/) Responses

```php
class HomeController {
    public function index( $request ) {
        return \WPEmerge\response()
            ->withHeader( 'X-Custom-Header', 'foo' );
    }
}
```

- Use PSR-7 objects for your responses.
- Easy to stream and modify before outputting.
- Uses Guzzle's implementation - [read more](https://github.com/guzzle/psr7).

---

#### View Composers

```php
View::addComposer( 'templates/about-us', function( $view ) {
    $view->with( ['hello' => 'world'] );
} );
```

- Pass generic context to partials regardless of which controller or parent view uses them.
- Work with any View engine (Php, Blade, Twig).

---

#### Service container

```php
// Run this inside the register() method of a service provider.
$container['my_service'] = function( $container ) {
    return new MyService( $container['my_dependency'] );
};
```

- Define your dependencies in a service container.
- Override any and all WP Emerge dependencies when needed.
- Enables dependency injection.
- Uses Pimple - [read more](https://pimple.symfony.com/).

---

#### Service providers

```php
class MyServiceProvider implements ServiceProviderInterface {
    public function register( $container ) {
        $container['my_service'] = function( $container ) {
            return new MyService( $container['my_dependency'] );
        };
    }

    public function boot( $container ) {
        // bootstrap code if needed
    }
}
```

- Register dependencies into the service container and boot them, if needed.
- Enables to split your dependencies logically into separate providers.
- WP Emerge's own dependencies are setup via Service providers.

---

#### PHP View Layouts

`index.php`
```php
<?php
/**
* Layout: layouts/app.php
*/

if ( is_single() ) {
    \WPEmerge\render( ['loop-single', 'loop'] );
} else {
    \WPEmerge\render( 'loop' );
}
```
`layouts/app.php`
```php
<?php
\WPEmerge\render( 'header' );

if ( ! is_singular() ) {
	app_the_title( '<h2 class="post-title">', '</h2>' );
}

\WPEmerge\layout_content();

\WPEmerge\render( 'sidebar' );

\WPEmerge\render( 'footer' );
```

- Eliminate repeating `get_header()`/`get_footer()` calls in every view.
- Reduce boilerplate and improve maintainability of views without sacrificing flexibility.

---

#### Custom view engine support

```php
// Run this inside the register() method of a service provider.
$container[ WPEMERGE_VIEW_ENGINE_KEY ] = function( $container ) {
    return new MyViewEngine();
};
```

- Replace the view engine used in the service container.
- Blade and Twig available as add-on packages.
- You can even write your own view engine and use it seamlessly.

## Contributing

WP Emerge is completely open source and we encourage everybody to participate by:

- Reviewing `.github/CONTRIBUTING.md`.
- ⭐ the project on GitHub \([https://github.com/htmlburger/wpemerge](https://github.com/htmlburger/wpemerge)\)
- Posting bug reports \([https://github.com/htmlburger/wpemerge/issues](https://github.com/htmlburger/wpemerge/issues)\)
- (Emailing security issues to [hi@atanas.dev](mailto:hi@atanas.dev) instead)
- Posting feature suggestions \([https://github.com/htmlburger/wpemerge/issues](https://github.com/htmlburger/wpemerge/issues)\)
- Posting and/or answering questions \([https://github.com/htmlburger/wpemerge/issues](https://github.com/htmlburger/wpemerge/issues)\)
- Submitting pull requests \([https://github.com/htmlburger/wpemerge/pulls](https://github.com/htmlburger/wpemerge/pulls)\)
- Sharing your excitement about WP Emerge with your community
