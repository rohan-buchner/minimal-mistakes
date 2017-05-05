---
published: true
layout: single
section-type: post
category: development
---
#How to setup your PHPStorm environment so that it sucks less... 

##Make it fast:

Go to:
`Help > Edit Custom VM Options`

Then add or modify the following, as per your systems available resources.
~~~ text
# custom PhpStorm VM options

-Xms512m
-Xmx2048m
-XX:MaxPermSize=350m 
-XX:ReservedCodeCacheSize=225m
-XX:+UseCompressedOops
-XX:+UseCodeCacheFlushing

-Dawt.useSystemAAFontSettings=lcd
-Dawt.java2d.opengl=true
~~~


Go to:
`Help > Edit Custom Properties`

Then add:
~~~ text
# custom PhpStorm properties

editor.zero.latency.typing=true
~~~

Time to disable stuff. Rinse and repeat for all the following menu paths. Navigate to, then disable all the plugins/tooling/completions etc. you **DON'T** need. 

* `PHPStorm > Preferences > Inspections`
* `PHPStorm > Pugins`
* `PHPStorm > Editor > Language Injections`

> Be warned, wharever you remove will effectively disable IDE assiting you with whatever feature you've disabled.

Next is to exclude the project items you dont need. These will generally be folders what either have external dependencies you dont need to know the source of, such as node_modules, or output files and folders such as .bin / public that contain your compiled or minified code.

To do the above, in your project explorer, right click on any folder, and then select:
`Mark Directory as > Excluded` 

##Give it context:

Make sure PHPStorm knows here your local tooling and frameworks are.. 
(because it might not)

Go to:
`PHPStorm > Preferences > PHP`

* Choose your PHP version
* Add CLI Interpreter
    * If none is present, add one. If your PHP was added via brew, point to the location withing Cellar as to have access to the php.ini file (and not the sysmlink in `/usr/local/bin`)

Go to:
`PHPStorm > Preferences > PHP > Composer`

* Setup interpreter to point to previously setup interpreter
* Point to your local composer (mine is symlinked to `/usr/local/bin/composer`)

Go to:
`PHPStorm > Preferences > Tools > Terminal`

* Set your shell path to whatever shell you may or may not be using (ZSH, fish, or leave as default)
* For some plugins or themes for ZSH / fish you need to disable shell integration. (Try both and see what works for you)

##Make it smart:
* Setup [PHP IDE Helper](https://github.com/barryvdh/laravel-ide-helper) if using Laravel

* Install PHPMD, and CodeSniffer in the terminal *(I hope you have [brew](https://brew.sh/) installed already)*
    * `brew install php-code-sniffer`
    * `brew install phpcs`
    * `brew install phpmd`

Back to the IDE now, link up all the utils via the preferences pane:

Go to:
`Languages & Prefrences > PHP`

 * `Code Sniffer`
     - Update the path to point to your local `phpcs` path
        + In any terminal run `which phpcs`
* `Mess Detector`
     - Update the path to point to your local `phpmd` path
        + In any terminal run `which phpmd`
            
> All thats left to do it setup code sniffer and mess detector rules so that you don't code like a monkey, and that everyone can see what a smarty pants you are
