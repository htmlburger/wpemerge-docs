# \MyTheme::theme()->config()

This utility provides tools to work with the theme's `config.json` file.

## \MyTheme::theme()->config()->get( $key, $default = null )

Return the specified config key using dot notation or the specified default if the key is not set.

Example:
```json
{
  foo: {
    bar: {
      baz: 'foobarbaz'
    }
  }
}
```
To get `baz` you would use `\MyTheme::theme()->config()->get( 'foo.bar.baz' )`.
