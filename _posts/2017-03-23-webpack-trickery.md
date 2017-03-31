---
layout: single
section-type: post
title: Webpack Trickery
category: development
---

Over the last few months I've been delving deeper into the world of webpack.

Below I'll demonstrate a few cool tricks that I've learned that really isn't as obvious as it should be. 

 **Context:**
   - I'm using webpack for an angular 2 project thus Im using TypeScript... buuuut, the items I'll highlight will be usefull for anyone


**Serving 3rd party js libraries such as jQuery, momentjs etc:**

In your webpack.js file
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

In the example above, moment was installed using npm and located in my node_modules folder.
In my app code the above allows me "access" the moment foldr as such via:

~~~ javascript
import * as moment from 'moment/moment';
~~~

**Adding Bootstrap or FontAwesome**

There are 2 amazing opensource projects: [bootstrap-loader](https://github.com/shakacode/bootstrap-loader) and [font-awesome-loader](https://github.com/shakacode/font-awesome-loader), both made by the [ShakaCode](https://github.com/shakacode) team.

To implment them install via npm and add the following to your webpack entry

~~~ javascript
  entry: {
    vendor: [
      'font-awesome-loader',
      bootstrapEntryPoints.dev   //or .prod
    ]
  }
~~~

 Setup a helper service or point to the config path manually:

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

~~~ javascript
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

