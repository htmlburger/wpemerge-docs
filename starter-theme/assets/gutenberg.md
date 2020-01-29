# Gutenberg

Gutenberg support is included - here's how to customize it:

## Color palette

The color palette is available as shared `config.json` variables which you can edit:

`config.json`
```json
{
  "variables": {
    "color": {
      "material-red": "#f44336",
      "material-pink": "#e91e63",
      "material-purple": "#9c27b0",
      // ...
    },
    // ...
  },
  // ...
}
```

### PHP

To access shared variables in PHP, use `Config::get()`:

`app/src/WordPress/ThemeServiceProvider.php`
```php
add_theme_support( 'editor-color-palette', [
	[
		'name'  => __( 'Red', 'app' ),
		'slug'  => 'material-red',
		'color' => App::theme()->config()->get( 'variables.color.material-red', '#000000' ),
	],
	[
		'name'  => __( 'Pink', 'app' ),
		'slug'  => 'material-pink',
		'color' => App::theme()->config()->get( 'variables.color.material-pink', '#000000' ),
	],
	[
		'name'  => __( 'Purple', 'app' ),
		'slug'  => 'material-purple',
		'color' => App::theme()->config()->get( 'variables.color.material-purple', '#000000' ),
	],
	// ...
] );
```

### SASS

To access shared variables in SASS, use the automatically generated variables directly:

`resources/styles/shared/_editor.scss`
```scss
.has-material-red-color {
  color: $color-material-red;
}
// ...
```

### JS

To access shared variables in JS, import the config file:
```js
import config from '@config';

console.log(config.variables.color['material-red']);
```

## Font sizes

Works in the same way as colors:

`config.json`
```json
{
  "variables": {
    "font-size": {
      "xs": "12px",
      "s": "16px",
      "m": "20px",
      "l": "28px",
      "xl": "36px"
    },
    // ...
  },
  // ...
}
```

### PHP

`app/src/WordPress/ThemeServiceProvider.php`
```php
add_theme_support( 'editor-font-sizes', [
	[
        'name'      => __( 'extra small', 'app' ),
        'shortName' => __( 'XS', 'app' ),
        'size'      => App::theme()->config()->get( 'variables.font-size.xs', 12 ),
        'slug'      => 'xs',
    ],
    [
        'name'      => __( 'small', 'app' ),
        'shortName' => __( 'S', 'app' ),
        'size'      => App::theme()->config()->get( 'variables.font-size.s', 16 ),
        'slug'      => 's',
    ],
	// ...
] );
```

`resources/styles/shared/_editor.scss`
```scss
.has-xs-font-size {
  font-size: $font-size-xs;
}
// ...
```
