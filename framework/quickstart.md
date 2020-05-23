# Quickstart

1. Run the following in your theme directory:
    ```bash
    composer require htmlburger/wpemerge
    ```
2. Create a new file for your main Application class, a useful convention is to use `app/src/App.php`:
    ```php
    <?php

    use WPEmerge\Application\ApplicationTrait;

    class App {
       use ApplicationTrait;
    }
    ```
    This class will serve as a shortcut to our application when we're configuring it.
3. Create a new file for your routes, a useful convention is to use `app/routes/web.php`:
    ```php
    <?php
    /**
     * Web Routes.
     */

    \App::route()->get()->url( '/' )->handle( function() {
        return 'Hello World!';
    } );
    ```
4. Add the following to the **beginning** of your `functions.php`:
    ```php
    // Load our composer dependencies.
    require_once 'vendor/autoload.php';

    // Load our App class.
    require_once 'app/src/App.php';

    // Bootstrap our Application.
    \App::make()->bootstrap( [
        'routes' => [
            'web' => [
                'definitions' => __DIR__ . '/app/routes/web.php',
            ],
        ],
    ] );
    ```
5. If you open your website's homepage in your browser you will now be greeted with `Hello World!`.

## Optional: Setting up autoloading for your own classes

1. Add the following to your `composer.json`:
    ```js
    "autoload": {
      "psr-4": {
        "App\\": "app/"
      }
    }
    ```
    - `App` represents the base namespace for your classes
    - `app/` represents the base path for your classes relative to your theme (i.e. `twentyseventeen/app`)

    With this change any class in the `App\` namespace will be autoloaded from the `app/` directory relative to your `composer.json`.
2. Run `composer dump-autoload` so your changes take effect

Here are a few example classes (and their filepaths) that will be autoloaded:

| Class                         | File                              |
|-----------------------------  |---------------------------------- |
| `App\MyClass`                 | `app/MyClass.php`                 |
| `App\Controllers\Web\Home`    | `app/Controllers/Web/Home.php`    |
| `App\ViewComposers\UserBadge` | `app/ViewComposers/UserBadge.php` |

You can find more information about PSR-4 autoloading at http://www.php-fig.org/psr/psr-4/
