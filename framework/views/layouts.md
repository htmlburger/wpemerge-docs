# Layouts

It is a common pattern in WordPress themes to see `get_header()` and `get_footer()` called in every template. In addition, these calls are often followed or preceded by related markup.
In WP Emerge, all of this repetition can be avoided by utilizing layouts.

!> The layout functionality described below only applies to the default PHP view engine. Other view 
engines such as Blade and Twig have their own layout implementation which should be used instead.

## Example

Here's the main `theme/index.php` view of the WP Emerge Theme:

```php
<?php
/**
 * Layout: layouts/app.php
 */
\App::render( 'loop' );
```

The above is not a snippet - this is the entire file!

### Where are get_header() and get_footer()?

You've probably already noticed the `Layout` declaration in the comment at the top of the file - this declaration uses the WordPress [File Header](https://codex.wordpress.org/File_Header) API to set a layout for the current file.
Whenever the PHP View Engine encounters an `Layout` declaration, it will automatically load that layout and wrap the current view with it.

The `layouts/app.php` layout looks like this:
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

Nothing unusual about this file except the call to `\App::layoutContent()` - this is what defines where the actual view content should be rendered. Essentially, any view can be used as a layout as long as it calls `\App::layoutContent()` to include the "child" view content.
Also like any other view, layouts can declare their own `Layout` to achieve layout nesting and you can even use View Composers on layouts to pass context values.

You no longer need to have the header/footer boilerplate in every view while retaining the flexibility of having different header/footer partials for different templates by declaring your desired layouts when needed.
