---
layout: splash
section-type: post
title: Webpack Tips and Tricks
category: development
published: true
header: null
overlay_image: /assets/img/webpack.png
---

Over the last few months I've been delving deeper into the world of webpack.

Below I'll demonstrate a few cool tricks that I've learned that really isn't as obvious as it should be, which I believe any dev that using Webpack will encounter at some stage or another.

 > Context: I'm using Webpack for an Angular2 project, thus I'm using TypeScript, and some of the examples are pertaining to TypeScript only solutions.

--------------------------------------

<div style="margin: 0;"><strong>How to: Serve 3rd party JavaScript libraries</strong></div>
<sub><sup>that do not have TypeScript wrapped modules (yet)... if working in TypeScript</sup></sub>

Install the 3rd party module via npm, then expose it via an alias:

<div style="margin: 0;"><sub><sup>webpack.conf.js:</sup></sub></div>
~~~ javascript
module.exports = {
  module: {
     resolve: {
        alias: {
            moment$: path.join(__dirname, '../node_modules/moment')
        }
     }
  } 
}
~~~

<div style="margin: 0;"><sub><sup>Somewhere in your app</sup></sub></div>
~~~ javascript
import * as moment from 'moment/moment';
~~~

--------------------------------------

**How to: Import Bootstrap and FontAwesome.**

There are 2 amazing opensource projects: [bootstrap-loader](https://github.com/shakacode/bootstrap-loader) and [font-awesome-loader](https://github.com/shakacode/font-awesome-loader), both made by the [ShakaCode](https://github.com/shakacode) team.

To implement them, install via npm, and add the following to your webpack entry point:

<div style="margin: 0;"><sub><sup>webpack.conf.js:</sup></sub></div>
~~~ javascript
  entry: {
    vendor: [
      'font-awesome-loader',
      bootstrapEntryPoints.dev   // <=== bootstrapEntryPoints.helper.js
    ]
  }
~~~

Then setup a helper service or point to the config path manually:

<div style="margin: 0;"><sub><sup>bootstrapEntryPoints.helper.js:</sup></sub></div>
~~~ javascript
'use strict';

require('dotenv').config();

const fs = require('fs');

function getBootstraprcCustomLocation() {
  return process.env.BOOTSTRAPRC_LOCATION;
}

const bootstraprcCustomLocation = getBootstraprcCustomLocation();

let defaultBootstraprcFileExists;

try {
  fs.statSync('./.bootstraprc');
  defaultBootstraprcFileExists = true;
} catch (e) {
  defaultBootstraprcFileExists = false;
}

if (!bootstraprcCustomLocation && !defaultBootstraprcFileExists) {
  /* eslint no-console: 0 */
  console.log('You did not specify a \'bootstraprc-location\' ' +
    'arg or a ./.boostraprc file in the root.');
  console.log('Using the bootstrap-loader default configuration.');
}

// DEV and PROD have slightly different configurations
let bootstrapEntryPoint;
if (bootstraprcCustomLocation) {
  bootstrapDevEntryPoint = 'bootstrap-loader/lib/bootstrap.loader?' +
    `configFilePath=${__dirname}/${bootstraprcCustomLocation}` +
    '!bootstrap-loader/no-op.js';    // for prod add loader?extractStyles
} else {
  bootstrapDevEntryPoint = 'bootstrap-loader';  // for prod replace with 'bootstrap-loader/extractStyles'
}

module.exports = {
  dev: bootstrapEntryPoint
};
~~~

 ...and add a *.bootstraprc* file. This will allow you to manually configure and cherry pick features for your bootstap build.

<div style="margin: 0;"><sub><sup>.bootstraprc:</sup></sub></div>
~~~ yml
# Output debugging info
# loglevel: debug

# Major version of Bootstrap: 3 or 4
bootstrapVersion: 3

# If Bootstrap version 3 is used - turn on/off custom icon font path
useCustomIconFontPath: false

# Webpack loaders, order matters
styleLoaders:
  - style-loader
  - css-loader
  - sass-loader

# Extract styles to stand-alone css file
# Different settings for different environments can be used,
# It depends on value of NODE_ENV environment variable
# This param can also be set in webpack config:
#   entry: 'bootstrap-loader/extractStyles'
# extractStyles: false
# env:
#   development:
#     extractStyles: false
#   production:
#     extractStyles: true

# Customize Bootstrap variables that get imported before the original Bootstrap variables.
# Thus original Bootstrap variables can depend on values from here. All the bootstrap
# variables are configured with !default, and thus, if you define the variable here, then
# that value is used, rather than the default. However, many bootstrap variables are derived
# from other bootstrap variables, and thus, you want to set this up before we load the
# official bootstrap versions.
# For example, _variables.scss contains:
# $input-color: $gray !default;
# This means you can define $input-color before we load _variables.scss
# preBootstrapCustomizations: ./src/scss/bootstrap/pre-customizations.scss

# This gets loaded after bootstrap/variables is loaded and before bootstrap is loaded.
# A good example of this is when you want to override a bootstrap variable to be based
# on the default value of bootstrap. This is pretty specialized case. Thus, you normally
# just override bootrap variables in preBootstrapCustomizations so that derived
# variables will use your definition.
#
# For example, in _variables.scss:
# $input-height: (($font-size-base * $line-height) + ($input-padding-y * 2) + ($border-width * 2)) !default;
# This means that you could define this yourself in preBootstrapCustomizations. Or you can do
# this in bootstrapCustomizations to make the input height 10% bigger than the default calculation.
# Thus you can leverage the default calculations.
# $input-height: $input-height * 1.10;
# bootstrapCustomizations: ./src/scss/bootstrap/customizations.scss

# Import your custom styles here. You have access to all the bootstrap variables. If you require
# your sass files separately, you will not have access to the bootstrap variables, mixins, clases, etc.
# Usually this endpoint-file contains list of @imports of your application styles.
appStyles:
# ./src/scss/_custom.scss

### Bootstrap styles
styles:

  # Mixins
  mixins: true

  # Reset and dependencies
  normalize: true
  print: false
  glyphicons: true

  # Core CSS
  scaffolding: true
  type: true
  code: true
  grid: true
  tables: true
  forms: true
  buttons: true

  # Components
  component-animations: true
  dropdowns: true
  button-groups: true
  input-groups: true
  navs: true
  navbar: true
  breadcrumbs: true
  pagination: true
  pager: true
  labels: true
  badges: true
  jumbotron: true
  thumbnails: true
  alerts: true
  progress-bars: true
  media: true
  list-group: true
  panels: true
  wells: true
  responsive-embed: true
  close: true

  # Components w/ JavaScript
  modals: true
  tooltip: true
  popovers: true
  carousel: true

  # Utility classes
  utilities: true
  responsive-utilities: true

### Bootstrap scripts
scripts:
  transition: true
  alert: true
  button: true
  carousel: true
  collapse: true
  dropdown: true
  modal: true
  tooltip: true
  popover: true
  scrollspy: true
  tab: true
  affix: true
~~~


--------------------------------------

<div style="margin: 0;"><strong>How to: Pass environmental variables.</strong></div>
<sub><sup>like API paths, or environment specfic static properties</sup></sub>

Webpack had various ways of doing this but the route that worked the best for myself was to have each environent have its own .env file, and have webpack map the properties inside the .env to its appropriate property within the app.

<div style="margin: 0;"><sub><sup>webpack.conf.js:</sup></sub></div>
~~~ javascript
// npm install dotenv --save-dev
// dotenv is a super simple way to access .env files
require('dotenv').config();

new webpack.DefinePlugin({
    "process.env": {
       "KEY": process.env.KEY,    // <--- process.env in this instance does not equal the runtime process.env of the app.
       "FOO": {                   //      think of webpack and the running application of having 2 seperate contexes 
         "BAR": "yolo",
         "BOOLEAN_PROPERTY": true
      }
    }
})
~~~


--------------------------------------

**How to: Make your build faster.**

Once your solution become more sizable you might notice that it can get a bit slower from time to time, especially when using something like TypeScript. The below are a few options that you can make use of, depending on your use case.

* Use [HappyPack](https://github.com/amireh/happypack) to multithread certain loaders.

<div style="margin: 0;"><sub><sup>webpack.conf.js:</sup></sub></div>
~~~ javascript
var HappyPack = require('happypack');
var happyThreadPool = HappyPack.ThreadPool({size: 5});

module.exports = {
  module: {
    loaders: [
      {
        test: /.html$/,
        use: ['happypack/loader?id=html'],   // <---- loader?id=BUNDLE_ID
        exclude: /node_modules/
      }
    ],
    plugins: [
      new HappyPack({
        id: 'html',                          // <----- BUNDLE_ID
        threadPool: happyThreadPool,
        loaders: ['html-loader']             // loaders for this specific bundle
      })
    }
}
~~~

For TypeScript:

* Have specific dev build options to toggle source maps on or off (depeding on the task at hand)

<div style="margin: 0;"><sub><sup>webpack.conf.js:</sup></sub></div>
~~~ javascript
 // eval === fast, but not very useful, source-maps === useful, but slower (in larger projects)
 devtool: process.env.ENVIRONMENT === 'dev' ? 'eval' : 'source-maps'     
~~~

*  Set transpileOnly to true. If you want to speed up compilation a lot you can use this option, but you end up losing a lot of benefits. [docs](https://github.com/TypeStrong/ts-loader)

<div style="margin: 0;"><sub><sup>webpack.conf.js:</sup></sub></div>
~~~ javascript
 loaders: [
      { 
        test: /\.ts?$/,
        loader: 'ts-loader?' + JSON.stringify({
          transpileOnly: true
        }) 
      }
    ]
~~~
