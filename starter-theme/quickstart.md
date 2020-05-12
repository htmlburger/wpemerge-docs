# Quickstart

1. Browse to `/wp-content/themes`.
2. Run `composer create-project htmlburger/wpemerge-theme wpemerge`.
3. If you receive an error stating `TTY mode is not supported on Windows platform.`, run `php wpemerge install` inside the newly created `wpemerge` directory.
4. Activate your new theme.

?> Your development theme is not required to have the name `wpemerge`, but it's a useful convention. The built-in release script will create an optimized copy of the theme with your desired final name.

?> Note that starter theme uses `\MyApp` by default instead of `\App` as its main application class. Keep that in mind when following framework examples which refer to `\App` (e.g. `\App::output()` becomes `\MyApp::output()`).

During the installation process you will have the option to:

1. Remove author information from `composer.json`.
2. Install Carbon Fields.
3. Install a CSS framework (Normalize.css, Bootstrap, Bulma, Foundation, Tachyons, Tailwind CSS or Spectre.css).
4. Install Font Awesome.

## Bedrock Quickstart

1. Browse to your project root directory.
2. Run `composer require htmlburger/wpemerge-theme`.
3. Run `composer require --dev htmlburger/wpemerge-cli`.
4. Run `(cd web/app/themes/wpemerge-theme/ && php wpemerge config:create && php wpemerge install:dependencies && php wpemerge assets:build && php wpemerge install)`.
