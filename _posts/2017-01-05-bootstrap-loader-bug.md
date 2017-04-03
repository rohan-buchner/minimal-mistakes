---
layout: single
section-type: post
title: Bootstrap Loader Bug
category: development
---

As of Webpack 2 and some of the plugin updates that go along with it there has been an issue with bootstrap-loader.

~~~ javascrip
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

The temporaty fix for this is to do the following post build fix. In summaray the script alters the ExtractTextPlugins output to return the old format. Read more about the issue on this [thread](https://github.com/shakacode/bootstrap-loader/issues/238) 
