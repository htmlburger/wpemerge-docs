# <a href="http://wpemerge.com"><img src="https://docs.wpemerge.com/_images/wpemerge-logo-bar.png" height="61" alt="WP Emerge Logo" aria-label='WPEmerge.com' /></a>

[![Packagist](https://img.shields.io/packagist/vpre/htmlburger/wpemerge.svg?style=flat-square&colorB=0366d6)](https://packagist.org/packages/htmlburger/wpemerge) [![Build](https://img.shields.io/github/workflow/status/htmlburger/wpemerge/PHPUnit%20Tests?style=flat-square)](https://github.com/htmlburger/wpemerge/actions/workflows/phpunit.yml) [![Scrutinizer](https://img.shields.io/scrutinizer/g/htmlburger/wpemerge.svg?style=flat-square)](https://scrutinizer-ci.com/g/htmlburger/wpemerge/) [![Scrutinizer Coverage](https://img.shields.io/scrutinizer/coverage/g/htmlburger/wpemerge.svg?style=flat-square)](https://scrutinizer-ci.com/g/htmlburger/wpemerge/code-structure/master/code-coverage) [![Gitter](https://img.shields.io/gitter/room/nwjs/nw.js.svg?style=flat-square&colorB=7d07d1)](https://gitter.im/wpemerge/Lobby)


📦 A micro framework which modernizes WordPress as a CMS development by providing tools to implement MVC and more.

🔥 Integration is incremental - you can use it only on the pages you want or on all pages.

❤ If you've used frameworks such as Laravel, Slim or Symfony you will love WP Emerge.

🚀 Also, make sure you check out the WP Emerge [Starter Plugin](https://github.com/htmlburger/wpemerge-plugin) and [Starter Theme](https://github.com/htmlburger/wpemerge-theme) projects.

## Summary

- [Documentation](#documentation)
- [API Reference](#api-reference)
- [Development Team](#development-team)
- [Comparison Table](#comparison-table-¹-²)
- [Non-goals](#non-goals)
- [Requirements](#requirements)
- [Features](#features)
- [Contributing](#contributing)

## Documentation

[http://docs.wpemerge.com/#/framework/overview](http://docs.wpemerge.com/#/framework/overview)

[http://docs.wpemerge.com/#/framework/quickstart](http://docs.wpemerge.com/#/framework/quickstart)

## API Reference

[https://api.wpemerge.com/](https://api.wpemerge.com/)

## Development Team

Brought to you by [Atanas Angelov](https://atanas.dev/) and the lovely folks at [htmlBurger](http://htmlburger.com).

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

_¹ We are comparing frameworks and themes - style, build tools etc. are not mentioned. For a full comparison check out the [WP Emerge Starter Theme](/starter/theme/overview)._

_² WP Emerge is theme agnostic - you can use it in any theme._

_³ Sage's Controller is more of a View Composer than a Controller._

_⁴ Sage's Controller provides similar functionality but is limited to 1 composer (controller) per view and vice versa._

_Email any factual inaccuracies to [hi@atanas.dev](mailto:hi@atanas.dev) so they can be corrected._

## Non-goals

- Taking over the WordPress main query.

  WP Emerge does __not__ take over the main query - it actively works with it.
- Taking over WordPress routing.

  WP Emerge does __not__ take over WordPress' routing - it actively works with it. The only exception to this are hardcoded URLs explicitly added by a user.
- Reinventing WordPress APIs using object-oriented interfaces.

  WP Emerge does not provide alternative APIs for registering post types, taxonomies or the like for little added benefit. Instead, it provides logical and handy places for developers to use core APIs.
- Using a third party engine by default.

  WP Emerge uses PHP by default in the same way WordPress does but with added features. Using a third party engine is entirely optional and requires installing an extension.
- Include most of Laravel or another framework.

  WP Emerge is lean and tuned for WordPress. While inspired by Laravel, it does not come with any `illuminate/*` packages. There are only 2 third party production dependencies:
  - `pimple/pimple` - The single-file PHP service container.
  - `guzzlehttp/psr7` - A PSR-7 Request and ServerRequest implementation.

## Requirements

- [PHP](http://php.net/) >= 5.5
- [WordPress](https://wordpress.org/) >= 4.7
- [Composer](https://getcomposer.org/)

## Features

#### Named routes with custom URLs or dynamic conditions

```php
\App::route()->get()->url( '/' )->handle( 'HomeController@index' );

\App::route()->get()
    ->url( '/custom' )
    ->query( function ( $query_vars ) {
        return [
            // WP_Query query vars go here ...
        ];
    } )
    ->handle( 'CustomController@custom' );

\App::route()->get()
    ->where( 'post_id', get_option( 'page_on_front' ) )
    ->handle( 'HomeController@index' );

\App::route()->get()
    ->where( function() {
        return is_front_page();
    } );
    ->handle( 'HomeController@index' );
```

- Enable the use of controllers to compartmentalize your business logic away from your presentation.
- Use existing routes or add new ones.
- Use built-in dynamic route conditions or define your own custom ones.
- Use anonymous functions for quick one-off conditions.
- Use `\App::routeUrl()` to get the URL of a named route to avoid hard-coding URLs.

---

#### Controllers

```php
class HomeController {
    public function index( $request ) {
        $name = $request->query( 'name' );
        return \App::view( 'templates/home.php' )
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
\App::route()->get()
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
        return \App::response()
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
\App::views()->addComposer( 'templates/about-us', function( $view ) {
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
    \App::render( ['loop-single', 'loop'] );
} else {
    \App::render( 'loop' );
}
```
`layouts/app.php`
```php
<?php
\App::render( 'header' );

if ( ! is_singular() ) {
	app_the_title( '<h2 class="post-title">', '</h2>' );
}

\App::layoutContent();

\App::render( 'sidebar' );

\App::render( 'footer' );
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
