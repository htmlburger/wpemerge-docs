# Quickstart

1. Browse to `/wp-content/plugins`.
2. Run `composer create-project htmlburger/wpemerge-plugin wpemerge`.
3. Run `cd wpemerge && php wpemerge install`.
4. Activate your new plugin.

?> Your development plugin is not required to have the name `wpemerge`, but it's a useful convention. The built-in release script will create an optimized copy of the plugin with your desired final name.

During the installation process you will have the option to:

1. Remove author information from `composer.json`.
2. Install Carbon Fields.
3. Install a CSS framework (Normalize.css, Bootstrap, Bulma, Foundation, Tachyons, Tailwind CSS or Spectre.css).
4. Install Font Awesome.

?> The starter plugin comes with a placeholder namespace, functions and constants prefix - `\MyApp`, `my_app` and `MY_APP` respectively.

To change these prefixes, you can run `yarn rebrand` inside the plugin directory - it will ask you a couple of questions and automatically rename everything for you.

Let's say you've chosen `AwesomePlugin` as your namespace during the `yarn rebrand` process. What this means is that instead of `\MyApp` you must now use `\AwesomePlugin`:
- `\MyApp::render()` becomes `\AwesomePlugin::render()`.
- `my_app_get_title()` becomes `awesome_plugin_get_title()`.
- etc.

## Quickstart on Bedrock

1. Browse to your [Bedrock](https://roots.io/bedrock/) root directory.
2. Run `composer require htmlburger/wpemerge-plugin:0.16.2`.
3. Run `composer require --dev htmlburger/wpemerge-cli`.
4. Run `(cd web/app/plugins/wpemerge-plugin && composer run install-dev-env && php wpemerge install)`.
5. Activate your new plugin.
