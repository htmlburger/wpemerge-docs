# Publishing

Here are the general steps you need to follow when you are ready to publish your theme.

1. Edit your `package.json` file and make sure you have replaced the `app` textdomain referenced in the following commands:
    ```json
    "i18n:textdomain": "wpi18n ... --textdomain=app",
    "i18n:pot": "wpi18n ... --textdomain=app",
    ```
2. Run `yarn i18n` to update all usages of i18n functions (`__()`, `_e()` etc.) in your source files and to update your language files with all strings referenced in your source files.
3. If you have any files outside of the default directories that you wish to publish with your theme make sure they are present in the `release.include` key of your `config.json` file:
    ```json
    "release": {
      "configWhitelist": [
        "variables"
      ],
      "include": [
        "app/",
        "dist/",
        "languages/",
        "vendor/",
        "views/",
        "config.json",
        "LICENSE",
        "README.md",
        "readme.txt",
        "screenshot.png",
        "style.css",
        "*.php"
      ]
    }
    ```
4. Run [`yarn release`](starter-theme/scripts/overview#yarn-release) to build your final distributable theme `.zip` archive.
