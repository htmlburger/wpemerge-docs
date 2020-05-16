# Configuration

The starter theme comes with a configuration file which gives you control over shared variables, development and release settings to mention a few.

The configuration comes in the form of a `config.json.dist` file which you should copy into a new `config.json` file and customize as needed. Here's the full list of configuration options available:
```json
{
  // An array of asset bundles that should be generated.
  // To create a new bundle, add its desired name into this array
  // and create a resources/scripts/{NAME_HERE}/index.js entry file.
  "bundles": [
    "frontend",
    "admin",
    "login",
    "editor"
  ],

  // Shared variables between PHP, JavaScript and SCSS.
  // PHP:
  //   $color = \MyApp::core()->config()->get( 'variables.color.material-red', '#000000' );
  //   $size  = \MyApp::core()->config()->get( 'variables.font-size.xs', '12px' );
  //
  // JS:
  //   import config from '@config';
  //   const color = config.variables.color['material-red'];
  //   const size  = config.variables['font-size'].xs;
  //
  // SCSS:
  //   a { color: $color-material-red; font-size: $font-size-xs; }
  "variables": {
    "color": {
      "material-red": "#f44336",
      "material-pink": "#e91e63",
      "material-purple": "#9c27b0",
      "material-deep-purple": "#673ab7",
      "material-indigo": "#3f51b5",
      "material-blue": "#2196f3",
      "material-light-blue": "#03a9f4",
      "material-cyan": "#00bcd4",
      "material-teal": "#009688",
      "material-green": "#4caf50",
      "material-light-green": "#8bc34a",
      "material-lime": "#cddc39",
      "material-yellow": "#ffeb3b",
      "material-amber": "#ffc107",
      "material-orange": "#ff9800",
      "material-deep-orange": "#ff5722",
      "material-brown": "#795548",
      "material-grey": "#9e9e9e",
      "material-blue-grey": "#607d8b"
    },
    "font-size": {
      "xs": "12px",
      "s": "16px",
      "m": "20px",
      "l": "28px",
      "xl": "36px"
    }
  },

  // Options related to development mode.
  "development": {
    // URL where your local environment is served.
    "url": "http://localhost/",

    // URL where your asset build process is served in hot mode.
    // Useful when you run `yarn hot` locally but your website
    // is served by a virtual machine (or vice-versa).
    // Defaults to the value of development.url
    "hotUrl": "http://localhost/",

    // Port to use in hot mode.
    "port": 3000
  },

  // Options related to the `yarn release` command.
  "release": {
    // Configuration options to keep in the release version of this file
    // when building a release zip. Since variables are shared we want
    // to keep them but development and release options are irrelevant
    // in the release zip.
    "configWhitelist": [
      "variables"
    ],

    // Files and directories to include in the release zip. Accepts globs.
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
}
```
