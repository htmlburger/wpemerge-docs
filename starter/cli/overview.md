# CLI

WP Emerge starter plugin and theme come with a minimal CLI utility which can help you out with tasks such as writing boilerplate code.

## Commands

Call `php wpemerge list` to get a list of all commands and options.

## WordPress Unit Testing

### `php wpemerge install:php-tests`

This command will scaffold a WordPress-enabled unit testing directory called `tests` inside your plugin or theme root directory.

Refer to the generated `tests/php/README.md` file for further instructions.

## Boilerplate

The CLI comes with a couple of boilerplate generating commands. Specifying a namespace is optional.

### `php wpemerge make:controller MyNamespace\ControllerNameHere`

Creates the following file: `app/src/Controllers/MyNamespace/ControllerNameHere.php`.

### `php wpemerge make:service-provider MyNamespace\ServiceProviderNameHere`

Creates the following file: `app/src/MyNamespace/ServiceProviderNameHere.php`.

### `php wpemerge make:view-composer MyNamespace\ViewComposerNameHere`

Creates the following file: `app/src/ViewComposers/MyNamespace/ViewComposerNameHere.php`.
