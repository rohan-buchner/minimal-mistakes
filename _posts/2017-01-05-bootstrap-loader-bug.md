---
layout: single
section-type: post
title: Bootstrap Loader Bug
category: development
---

As of Webpack 2 and some of the plugin updates that go along with it there has been an issue with bootstrap-loader.

~~~ javascript
Module not found: Error: Cannot resolve module '[object Object]./lib/bootstrap.styles.loader.js' in /home/user/work/project/node_modules/bootstrap-loader
 @ ./~/bootstrap-loader/lib/bootstrap.loader.js!./~/bootstrap-loader/no-op.js 1:21-1087
~~~

There are some breaking changes to the ExtractTextPlugin where the output format has changed from:

Old:
~~~ javascript
[ 'style-loader', 'css-loader', '...']
~~~

New:
~~~ javascript
[{ loader: 'style-loader', options: {...} }, { loader: 'css-loader', options: {...} } ]
~~~

The temporaty fix for this is to run the script (I can't claim credit for it though...)

~~~ javascript
var fs = require('fs');

var theFile = './node_modules/bootstrap-loader/lib/utils/buildExtractStylesLoader.js';

fs.readFile(theFile, 'utf-8', function(err, data){
  if (err) throw err; // do something here: console.log for example

  var result = data.replace("return ExtractTextPlugin.extract({ fallbackLoader: fallbackLoader, loader: restLoaders });", "return [ ExtractTextPlugin.loader().loader + '?omit\&remove',    fallbackLoader,    restLoaders  ].join('!');");

  fs.writeFile(theFile, result, 'utf8', function (err) {
    if (err) throw err; // do something her: console.log for example
  });
});
~~~


 In summary the script alters the buildExtractStylesLoader.js output to return the old format. Read more about the issue on this [thread](https://github.com/shakacode/bootstrap-loader/issues/238) 
