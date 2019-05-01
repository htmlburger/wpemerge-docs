# Yarn Scripts

## `yarn dev`

Run the build process in development mode and enable [Browsersync](#browsersync).

## `yarn build`

Run the build process in production mode with all optimizations enabled.

## `yarn release`

Creates a production-ready zip of your theme following these steps:
1. Run `yarn build`.
2. Install production-only Composer dependencies.
3. Create a `/wp-content/themes/your-theme-name.zip` archive containing all files and directories added to `release.include` of your `config.json` file.
   - By default, this list contains all necessary files for your theme.
   - If you have any custom files/directories outside of the standard directories of the theme make sure to add them to this list.
4. Restore development Composer dependencies.

## `yarn lint`

Run the php, scripts and styles linters (`WPCS`, `eslint` and `stylelint` respectively), reporting any lint rule violations.

## `yarn lint-fix`

Run the php, scripts and styles linters (`WPCS`, `eslint` and `stylelint` respectively), fixing any fixable lint rule violations.

## `yarn i18n`

Runs both `yarn i18n:textdomain` and `i18n:pot`.

#### `yarn i18n:textdomain`

Runs the `textdomain` command of the [node-wp-i18n](https://www.npmjs.com/package/node-wp-i18n) package, adding a text domain to all gettext function calls throughout your code.

#### `yarn i18n:pot`

Runs the `makepot` command of the [node-wp-i18n](https://www.npmjs.com/package/node-wp-i18n) package, generating your `languages/app.pot` file based on all gettext function calls throughout your code.

## Browsersync

By default, Browsersync will setup a simple web server and serve your files through a custom port in order to establish a communication channel between the build process and your browser like this:
`http://localhost:3000/`

This is not ideal when working on WordPress projects that are setup in a subdirectory, for example.
To let Browsersync know your site's url, open up `config.json` from the root theme directory and edit the `development.url` key like this:
```json
{
    "development": {
        "url": "http://localhost/my/nested/subdirectory/wordpress/"
        // ...
    }
    // ...
}
```
Save the file and restart your development build process by running `yarn dev`.
