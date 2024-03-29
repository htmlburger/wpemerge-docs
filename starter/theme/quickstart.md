# Quickstart

1. Browse to `/wp-content/themes`.
2. Run `composer create-project htmlburger/wpemerge-theme THEME_NAME` replacing `THEME_NAME` with your desired name.
3. Run `cd wpemerge && php wpemerge install`.
4. Activate your new theme.

During the installation process you will have the option to:

1. Remove author information from `composer.json`.
2. Install Carbon Fields.
3. Install a CSS framework (Normalize.css, Bootstrap, Bulma, Foundation, Tachyons, Tailwind CSS or Spectre.css).
4. Install Font Awesome.

?> The starter theme comes with a placeholder namespace, functions and constants prefix - `\MyApp`, `my_app` and `MY_APP` respectively.

To change these prefixes, you can run `yarn rebrand` inside the theme directory - it will ask you a couple of questions and automatically rename everything for you.

Let's say you've chosen `AwesomeTheme` as your namespace during the `yarn rebrand` process. What this means is that instead of `\MyApp` you must now use `\AwesomeTheme`:
- `\MyApp::render()` becomes `\AwesomeTheme::render()`.
- `my_app_get_title()` becomes `awesome_theme_get_title()`.
- etc.

## Quickstart on Bedrock

1. Browse to your [Bedrock](https://roots.io/bedrock/) root directory.
2. Run `composer require htmlburger/wpemerge-theme:0.17.1`.
3. Run `composer require --dev htmlburger/wpemerge-cli`.
4. Run `(cd web/app/themes/wpemerge-theme && composer run install-dev-env && php wpemerge install)`.
5. Activate your new theme.
