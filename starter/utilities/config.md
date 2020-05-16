# \MyApp::core()->config()

This utility provides tools to work with the project's `config.json` file.

`\MyApp::core()->config()->get( $key, $default = null )`

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
To get `baz` you would use `\MyApp::core()->config()->get( 'foo.bar.baz' )`.
