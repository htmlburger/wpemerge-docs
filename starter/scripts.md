# Scripts

## `yarn start`

Run the build process in development mode:
- Transpile modern JavaScript syntax based on Stage 2 proposals and the `browserslist` key in `package.json`.
- Compile SCSS into CSS.
- Combine CSS media queries to reduce stylesheet size.
- Process CSS through Autoprefixer.
- Generate a `manifest.json` file with automatic filename-based asset cache busting.
- Bundle images in `resources/images/sprite/` into a single sprite image, with optional @2x version.

## `yarn hot`

Run the build process in hot mode which is identical to development mode but also automatically reloads your browser as you make changes. Modules processed by a Webpack loader that supports HMR (e.g. CSS via `style-loader`) will be injected via HMR avoiding a full page reload.

## `yarn build`

Run the build process in production mode with Webpack optimizations enabled:
- Transpile modern JavaScript syntax based on Stage 2 proposals and the `browserslist` key in `package.json`.
- **Minify JavaScript.**
- Compile SCSS into CSS.
- Combine CSS media queries to reduce stylesheet size.
- Process CSS through Autoprefixer.
- **Minify CSS.**
- Generate a `manifest.json` file with automatic filename-based asset cache busting.
- Bundle images in `resources/images/sprite/` into a single sprite image, with optional @2x version.
- **Optimize images to reduce filesize.**

?> Non-minified versions of JavaScript and CSS assets will also be generated in build mode in order to support WordPress' SCRIPT_DEBUG mode.

## `yarn rebrand`

Rewrites your files to use a new project name, namespace, constant and function prefix. For example:
 - `namespace \MyApp\*;`
 - `function my_app_*() {};`
 - `define( 'MY_APP_*', '*' );`

?> The `rebrand` command is rudimentary and only meant to be used once per project.

!> The `rebrand` command is potentially destructive - make sure you have committed and backed up your files before running it.

## `yarn release`

Creates a production-ready directory and zip of your plugin or theme. Running `yarn release` will:
1. Run `yarn build`.
2. Create a new `wpemerge-release` plugin or theme directory.
3. Copy all files and directories specified in the `release.include` key of your `config.json` file.
   - By default, this list contains all necessary files for your plugin or theme.
   - If you have any custom files/directories outside of the standard directories make sure to add them to this list.
4. Install production-only Composer dependencies with an authoritative classmap for improved autoloading performance.
5. Create a zip file of the directory created in step 2.

?> To change the output directory and zip name adjust `release:zip` in your `package.json` file.

## `yarn lint`

Run the php, scripts and styles linters (`WPCS`, `eslint` and `stylelint` respectively), reporting any lint rule violations.

## `yarn lint-fix`

Run the php, scripts and styles linters (`WPCS`, `eslint` and `stylelint` respectively), fixing any fixable lint rule violations.

## `yarn i18n`

Runs both `yarn i18n:textdomain` and `i18n:pot`.

#### `yarn i18n:textdomain`

Runs the `textdomain` command of the [node-wp-i18n](https://www.npmjs.com/package/node-wp-i18n) package, adding a text domain to all gettext function calls throughout your code.

#### `yarn i18n:pot`

Runs the `makepot` command of the [node-wp-i18n](https://www.npmjs.com/package/node-wp-i18n) package, generating your `languages/my_app.pot` file based on all gettext function calls throughout your code.

## Webpack Dev Server

In `yarn hot` mode, Webpack Dev Server will setup a simple web server and serve changes to your assets as you work on them. If your website is setup inside a virtual machine and you are running `yarn hot` inside that virtual machine you will have to change your `development.hotUrl` option in your `config.json` file as otherwise WP Emerge will assume you are running it on your localhost.

To let WP Emerge know where you run `yarn hot`, open up `config.json` from the root directory of your plugin or theme and edit the `development.hotUrl` key like this:
```json
{
    "development": {
        "hotUrl": "http://my-virtual-machine.test/"
        // ...
    }
    // ...
}
```
Save the file and restart your development build process by running `yarn hot`.
