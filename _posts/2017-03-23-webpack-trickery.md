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
<pre>
<code data-trim class="javascript">

module.exports = {
  module: {
     resolve: {
        alias: {
            moment$: path.join(__dirname, '../node_modules/moment')
        }
     }
  } 
}
</code></pre>

In the example above, moment was installed using npm and located in my node_modules folder.
In my app code the above allows me "access" the moment foldr as such via:

<pre><code data-trim class="typescript">

import * as moment from 'moment/moment';

</code></pre>