# Theme\Config

This class (it's actually a facade) provides tools to work with the theme's `config.json` file.

## Theme\Config::get( $key, $default = null )

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
To get `baz` you would use `Theme\Config::get( 'foo.bar.baz' )`.
