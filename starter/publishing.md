# Publishing

Here are the general steps you need to follow when you are ready to publish your plugin or theme.

1. Make sure you've ran `yarn rebrand` to replace all `MyApp` placeholders with ones that match your project name.
2. Edit your `package.json` file and make sure you have replaced the `wpemerge-release` release name for your project in the following command:
    ```json
    "release:zip": "node resources/build/release/release.js wpemerge-release"
    ```
3. Run `yarn i18n` to update all usages of i18n functions (`__()`, `_e()` etc.) in your source files and to update your language files with all strings referenced in your source files.
4. If you have any files outside of the default directories that you wish to publish with your plugin or theme make sure they are present in the `release.include` key of your `config.json` file:
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
5. Run [`yarn release`](starter/scripts/overview#yarn-release). This will produce:
  - `wpemerge-release/` - Directory only containing your release files.
  - `wpemerge-release.zip` - Zip of the above directory ready for distribution.
