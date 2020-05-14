# Publishing

Here are the general steps you need to follow when you are ready to publish your theme.

1. Make sure you replace all instances of the `\MyApp` class and namespace to something that matches your project name.
2. Make sure you replace all instances of `MYAPP_*` constants to something that matches your project name.
3. Edit your `package.json` file and make sure you have replaced the `myapp` textdomain with something that matches your project name in the following commands:
    ```json
    "i18n:textdomain": "wpi18n ... --textdomain=myapp",
    "i18n:pot": "wpi18n ... --textdomain=myapp",
    ```
4. Edit your `package.json` file and make sure you have replaced the `wpemerge-release` release name for your project in the following command:
    ```json
    "release:zip": "node resources/build/release/release.js wpemerge-release"
    ```
5. Run `yarn i18n` to update all usages of i18n functions (`__()`, `_e()` etc.) in your source files and to update your language files with all strings referenced in your source files.
6. If you have any files outside of the default directories that you wish to publish with your theme make sure they are present in the `release.include` key of your `config.json` file:
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
7. Run [`yarn release`](starter-theme/scripts/overview#yarn-release). This will produce:
  - `wpemerge-release/` - Directory only containing your release files.
  - `wpemerge-release.zip` - Zip of the above directory ready for distribution.
