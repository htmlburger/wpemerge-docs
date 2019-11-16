# CLI

WP Emerge Theme comes with a minimal CLI utility which can help you out with tasks such as writing boilerplate code.

## Commands

Call `php wpemerge list` to get a list of all commands and options.

## WordPress Unit Testing

### `php wpemerge install:php-tests`

This command will scaffold a WordPress-enabled unit testing directory called `tests` inside your theme root directory.

Refer to the generated `tests/php/README.md` file for further instructions.

## Boilerplate

The CLI comes with a couple of boilerplate generating commands.

### `php wpemerge make:controller ControllerNameHere`

1. Creates a file: `app/Controllers/ControllerNameHere.php`.
1. Adds an example controller class definition with namespace and class name filled in.

### `php wpemerge make:facade FacadeNameHere`

1. Creates a file: `app/Facades/FacadeNameHere.php`.
1. Adds an example facade class definition with namespace and class name filled in.

### `php wpemerge make:view-composer ViewComposerNameHere`

1. Creates a file: `app/Facades/ViewComposerNameHere.php`.
1. Adds an example view composer class definition with namespace and class name filled in.
