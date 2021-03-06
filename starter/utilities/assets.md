# \MyApp::core()->assets()

This utility provides tools to work with assets.

`\MyApp::core()->assets()->getUrl()`

Return the public URL of the plugin or theme root directory.

`\MyApp::core()->assets()->getAssetUrl( $asset )`

Get the real absolute URL to an asset that was built using the Webpack pipeline.

Assets such as images and fonts which pass through the build pipeline have their content hashed and appended to their filenames to facilitate cache busting. Since filenames change depending on the contents, a `dist/manifest.json` file is **automatically** generated to act as a map between the source filename and the final filename which includes said hash.

Having this `dist/manifest.json`:
```json
{
  // ...
  "images/favicon.ico": "images/favicon.3bb5e12219.ico",
  // ...
}
```
... calling the following in your theme:
```php
\MyApp::core()->assets()->getAssetUrl( 'images/favicon.ico' )
```
... will return:
```
https://example.org/wp-content/themes/my-theme/dist/images/favicon.3bb5e12219.ico
```

You can see this exact scenario used for the `\MyApp::core()->assets()->addFavicon()` method here:

https://github.com/htmlburger/wpemerge-app-core/blob/master/src/Assets/Assets.php#L159

`\MyApp::core()->assets()->getBundleUrl( $name, $extension )`

Get the URL for the given JavaScript or CSS bundle.
This utility takes care of returning the correct bundle URL based on whether a production bundle exists, SCRIPT_DEBUG is enabled or the bundles are currently being served in hot mode.

Example:
```php
// Enqueue scripts.
\MyApp::core()->assets()->enqueueScript(
    'my-js-bundle',
    \MyApp::core()->assets()->getBundleUrl( 'frontend', '.js' ),
    [ 'jquery' ],
    true
);

// Enqueue styles.
$style = \MyApp::core()->assets()->getBundleUrl( 'frontend', '.css' );

if ( $style ) {
    // A style bundle is not created in hot mode as it is injected via JavaScript
    // so we skip enqueueing it if it does not exist.
    \MyApp::core()->assets()->enqueueStyle(
        'my-css-bundle',
        $style
    );
}
```

`\MyApp::core()->assets()->enqueueStyle( $handle, $src, $dependencies = [], $media = 'all' )`

Enqueue a style like `wp_enqueue_style()` does but provide an automatic version number derived from the file's modified time.

Essentially provide a built-in, automatic and fool-proof cache buster for the enqueued style.

`\MyApp::core()->assets()->enqueueScript( $handle, $src, $dependencies = [], $in_footer = false )`

Enqueue a script like `wp_enqueue_script()` does but provide an automatic version number derived from the file's modified time.

Essentially provide a built-in, automatic and fool-proof cache buster for the enqueued script.

`\MyApp::core()->assets()->addFavicon()`

Output a &lt;link /&gt; tag for the site favicon pointing to `dist/images/favicon.ico` which is generated by default from `resources/images/favicon.ico`.

Will not output anything if you have already added a favicon through WordPress' Customizer so it does not override it.
