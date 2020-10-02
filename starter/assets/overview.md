# Overview

?> This article assumes you are using the default OS terminal on MacOS and Linux or GitBash on Windows.

The WP Emerge starter plugin and theme come with a powerful asset build process which covers a multitude of use cases to save you time and resources.

## Requirements

- [NodeJS](https://nodejs.org/en/): >=6.9.1
- [NPM](https://www.npmjs.com/) >= 5.3.0 or [Yarn](https://yarnpkg.com/en/) >= 1.0.0

## Setting up the environment

1. To install all required packages run `yarn install`
2. Run your desired build process:
    - To run the build process in production mode run `yarn build`
    - To run the build process in development mode run `yarn start`. This will also rebuild assets on changes.
    - To run the build process in development mode run `yarn hot`. This will also rebuild assets on changes and automatically reload your browser.
    - For more information, check out the [Yarn Scripts](/starter/scripts/overview) article.

## Importing assets

In order to process any asset (images, fonts, scripts, styles etc.) that asset must be at least one of the following:
- imported in the main `index.js` or any other file imported into it.
- referenced in the main `index.scss` or any other file imported into it.

This way only assets that are used will be processed, rather than every single file.

To help with importing assets, the webpack configuration supports a number of resolve aliases and module directories for easier asset referencing:

| Alias in JavaScript | Alias in SASS | Resolve |
| --- | --- |--- |
| `~/` | `~` | `node_modules` |
| `@scripts/` | `~@scripts/` | `resources/scripts/` |
| `@styles/` | `~@styles/` | `resources/styles/` |
| `@images/` | `~@images/` | `resources/images/` |
| `@fonts/` | `~@fonts/` | `resources/fonts/` |
| `@vendor/` | `~@vendor/` | `resources/vendor/` |
| `@dist/` | `~@dist/` | `dist/` |

?> All aliases resolve to absolute paths so you can use them in any file, regardless of its location.

### Examples

| File | Import |
| --- | --- |
| `node_modules/foo/bar.js` | `import foo from '~/foo/bar';` |
| `node_modules/foo/index.js` | `import foo from '~/foo';` |
| `resources/vendor/foo/bar.js` | `import foo from '@vendor/foo/bar';` |
| `resources/scripts/frontend/foo.js` | `import foo from '@scripts/frontend/foo';` |
| `resources/scripts/frontend/foo/bar.js` | `import foo from '@scripts/frontend/foo/bar';` |

In the last two examples you can even omit the `@scripts/` portion as your `resources/scripts/` directory is considered a module root:

| File | Import |
| --- | --- |
| `resources/scripts/foo.js` | `import foo from 'foo';` |
| `resources/scripts/foo/bar.js` | `import foo from 'foo/bar';` |

Similar patterns apply to SASS as well:

| File | Import |
| --- | --- |
| `node_modules/foo/bar.scss` | `@import '~foo/bar';` |
| `node_modules/foo/index.scss` | `@import '~foo';` |
| `resources/vendor/foo/bar.scss` | `@import '~@vendor/foo/bar';` |
| `resources/styles/foo.scss` | `@import '~@styles/foo';` |
| `resources/styles/foo/bar.scss` | `@import '~@styles/foo/bar';` |

Unlike `scripts/`, the `styles/` directory is __NOT__ considered a module root to avoid name clashes.
