---
author: admin
comments: false
date: 2013-07-03 05:24:29+00:00
layout: post
link: https://roberthorvick.com/2013/07/03/optimizing-site-behavior-based-on-observed-performance/
slug: optimizing-site-behavior-based-on-observed-performance
title: Optimizing site behavior based on observed performance
wordpress_id: 226
categories:
- Programming
---

![Waiting](http://www.roberthorvick.com/wp-content/uploads/2013/07/waiting.jpg)


## Ready for a terrible idea?


Is there gem/library/etc that monitors how long each request in a session takes in order to optimize the site behavior for observed client performance?

For example - let's say I'm sitting in my car waiting for my son. I have 10 minutes to kill so I pull out my phone. I browse twitter and find an article that sounds interesting. I click the link. I wait ... and then in about 45 seconds the site finally pops up. When it finally renders I am presented with about 1/10th of the article and a next button. So now I read for 45 seconds and have to hit "next" ... waiting another 45 seconds.

I spent half of my time waiting. Frustrated. Disappointed.

The site was optimized for mobile. It had a responsive design that rendered nicely and was easy to read. That wasn't the problem.

_The problem was that I was on a highly latent 2G connection._


### What if they had known that?


Perhaps they could have delivered twice as much of the article text at a time (to reduce round-trips) and none of the images (or smaller images I could click on to see a more detailed version). Images. Fonts. Content. Layout. Behaviors. All of them could be adjusted.

**Netflix knows**. They adjust video quality based on current performance.

Why don't content-heavy websites do this?

It wouldn't need to be difficult. When the first call comes in note the time and render a little javascript that runs on $(document).ready that does a simple get back to the server, for this the use of server monitoring is important and you can get these services from sites as [https://blog.servermania.com/10gbps-dedicated-servers-for-business/](https://blog.servermania.com/10gbps-dedicated-servers-for-business/). The time between the two is roughly what it took to get the page. Yeah, I get that caching will help on subsequent requests and that the person might move from 2G to 4G to wifi ... but continued sampling would make it possible to up/downgrade (just like Netflix).

I made a little app to do the first request tracking here: [http://192.241.199.15:3000/](http://192.241.199.15:3000/).

Do me a solid and click on the link?

Oh, and not that it is really interesting, but the code is [here](https://github.com/bubbafat/loadtime).

photo credit: [TheeErin](http://www.flickr.com/photos/theeerin/3719561835/) via [photopin](http://photopin.com) [cc](http://creativecommons.org/licenses/by-sa/2.0/)
