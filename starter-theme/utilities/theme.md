# \App::theme()

This utility provides a couple tools to help out with common tasks.

## \App::theme()->partial( $partial, $child = '', $context = [] )

Include a partial view file in a very similar fashion to how `get_template_part()` works, but with a couple of differences:

1. Automatically look for the partial in the `theme/partials/` directory as well.
2. Variables can be passed by utilizing the `$context` parameter.
3. Internally uses WP Emerge's `\App::render()` to enable support for View Composers.

## \App::theme()->uri()

Return the public URI of the theme root directory.
This method is an alias of `\App::theme()->assets()->getThemeUri()`.
