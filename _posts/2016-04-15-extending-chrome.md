---
layout: single
section-type: post
title: how deep does the rabbit hole go?
category: development
tags: [ 'javascript', 'injection' ]
---

We have an internal news feel like system with various other features that's crucial to our companies way of life.

I've started toying with the idea of doing a small CSS alterations via a Chrome extension called Stylish purely for aesthetic purposes, and one specific text-box that id like to have resizable (which was locked down). A colleague noticed my changes this and asked if we can team up to do some other smaller tweaks as well, in came grease(tamper)monkey... because CSS wont cut it all the way

1 week later we have a chrome plugin, that uses gulp to build our sass files and concatenate and minify the plugin JavaScript... talk about scope creep.

Long story short, one of the quirks of this application is that it's written in GWT utilizing a portlet style system.

In terms of code being delivered to the client, all the dependent libraries get pushed down, CSS, etc.. then post dependency delivery, the rest of the DOM gets constructed via GWT deciphering its payload and then constructing sections using JavaScript.

One of the problems (in terms of DOM alteration via injection) the above scenario causes, is that none of the DOM elements are ready by the time that you'd want to look up a specific element to alter it.

In comes the [Mutation Observer](https://hacks.mozilla.org/2012/05/dom-mutationobserver-reacting-to-dom-changes-without-killing-browser-performance/). Introduced in September 2011, this piece of awesomeness is just that. Awesome.

<pre><code data-trim class="javascript">
var MutationObserver = window.MutationObserver || window.WebKitMutationObserver;

// select the target parent node to monitor. 
var target = document.querySelector('.page-column-2');

// I noticed that we have the [Showdown]()https://github.com/showdownjs/showdown parser that being delivered, but not utilized.
// lets create a new instance of the parser and see what we can get up to
var showdown = new Markdown.Converter().makeHtml;                  

// create an observer instance
var observer = new MutationObserver(function(mutations, observer) {
    mutations.forEach(function(mutation) {
    
        // find a message item inside the news feed
        if(mutation.target.className == 'message-container' ) {

            // get the inner nested GWT wrapper object
            if(mutation.addedNodes[0] && mutation.addedNodes[0].className == 'gwt-HTML') {    

                // markdown :D 
                var newText = mutation.addedNodes[0].innerHTML.replace(/&lt;br&gt;/g,'\n');
                mutation.addedNodes[0].innerHTML = showdown(newText);                    
            }         
        }

    });    
});

// configuration of the observer:
var config = { childList: true, subtree: true };

// pass in the target node, as well as the observer options
observer.observe(document, config);	

// at this stage, we'd disconnect the observer, but as the feed gets refreshed. we need to keep watching.

</code></pre>


.. and just like that. My news feed is able to render markdown... now just to get everyone else to buy into the idea.