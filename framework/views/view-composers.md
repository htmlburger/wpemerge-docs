# View Composers

View composers prepare context for a view, partial, or layout whenever it is rendered. A view composer can be one of the following:
- An anonymous function.
- A reference in the `[ ClassName::class, 'METHOD_NAME' ]` format.
- A reference in the `'CLASS_NAME@METHOD_NAME'` format.

!> Default View Engine WARNING: Due to the nature of how `get_template_part()` works, you __MUST__ render partials using `\App::render()` instead of `get_template_part()` in order to support composition.

!> If you wish to compose core partials (e.g. `header.php`, `footer.php`) that are rendered using a `get_*()` function call (e.g. `get_header()`) you will have to use `\App::render( 'name' )` (e.g. `\App::render( 'header' )`) instead.

?> If the specified class name does not exist, by default, `\App\ViewComposers\` is automatically prepended for convenience.

?> More information on how to use `\App::render()` is available at the end of this article.

## Example

In this example we want to pass the latest posts to the `templates/partials/latest-news.php` partial.

First, we need to create a service provider class:
```php
<?php

namespace App\ViewComposers;

use WPEmerge\ServiceProviders\ServiceProviderInterface;

class ViewComposersServiceProvider implements ServiceProviderInterface {
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
        \App::views()->addComposer( 'templates/partials/latest-news', function( $view ) {
            $view->with( [
                'news' => new WP_Query( [
                    'posts_per_page' => 3,
                ] ),
            ] );
        } );
    }
}
```

Then, we need to register that service provider in the configuration:
```php
\App::make()->bootstrap( [
    'providers' => [
        // ...
        \App\ViewComposers\ViewComposersServiceProvider::class,
    ],
    // ...
] );
```

With this, whenever the `latest-news.php` partial is loaded, we will have the `$news` variable automatically available:
```php
// Inside latest-news.php:
<?php while ( $news->have_posts() ) : $news->the_post() ?>
  ...
<?php endwhile; ?>
<?php wp_reset_postdata(); ?>
```

Here's the same example, but using a class:

```php
<?php

namespace App\ViewComposers;

class LatestNewsViewComposer {
    public function compose( $view ) {
        $view->with( [
            'news' => new WP_Query( [
                'posts_per_page' => 3,
            ] ),
        ] );
    }
} );
```
And in our service provider we refer to the class instead of the anonymous function:
```php
// ...
public function bootstrap( $container ) {
    \App::views()->addComposer(
      'templates/partials/latest-news',
      \App\ViewComposers\LatestNewsViewComposer::class
    );
}
// ...
```

The expected method name by default is `compose` but you can use a custom one as well:
```php
// ...
public function bootstrap( $container ) {
    \App::views()->addComposer(
      'templates/partials/latest-news',
      'LatestNewsViewComposer@customMethodName'
    );
}
// ...
```

By default, WP Emerge will instantiate your class directly. However, if your class is registered in the service container with its class name as the key, then the class will be resolved from the service container instead:

```php
// Run this inside the register() method of a service provider.
$container[ LatestNewsViewComposer::class ] = function( $container ) {
    // your custom instantiation code here, e.g.:
    return new LatestNewsViewComposer();
}
```

## \App::render( $views, $context = [] )

Why you would use `\App::render()`:

1. You are using the default default view engine. You do not need it when using Blade or Twig, for example, as they have composition built-in.
2. Partials rendered using `include`, `require`, `get_template_part()` etc. __DO NOT__ support composition, `\App::render()` does.
3. `\App::render()` optionally provides context to the partial through the `$context` parameter.

For example, instead of using
```php
<?php get_template_part( 'latest-news' ); ?>
```
you would use
```php
<?php \App::render( 'latest-news' ); ?>
```
or instead of using
```php
<?php get_template_part( 'post', $post_type ); ?>
```
you would use
```php
<?php \App::render( ['post-' . $post_type, 'post'] ); ?>
```

If you do not need composition or context for a given partial, feel free to use `get_template_part()` instead.
