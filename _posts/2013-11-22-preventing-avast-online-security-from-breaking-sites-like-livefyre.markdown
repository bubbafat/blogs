---
author: admin
comments: false
date: 2013-11-22 16:19:31+00:00
layout: post
link: https://roberthorvick.com/2013/11/22/preventing-avast-online-security-from-breaking-sites-like-livefyre/
slug: preventing-avast-online-security-from-breaking-sites-like-livefyre
title: Preventing avast! Online Security from breaking sites like LiveFyre
wordpress_id: 320
---

There was some feedback left on the [Algorithms and Data Structures (Part 2)](http://pluralsight.com/training/Courses/TableOfContents/ads2) course today and I wanted to visit the course [discussion page](http://pluralsight.com/training/Courses/Discussion/ads2) to respond.

The problem was that when I got to the page this is what I saw:

[![livefyre](http://www.roberthorvick.com/wp-content/uploads/2013/11/livefyre.png)](http://www.roberthorvick.com/wp-content/uploads/2013/11/livefyre.png)

Stalled.

So I hit F12 to look at the console errors and saw these:

`GET https://apis.google.com/js/plusone.js  ads2:621
GET http://connect.facebook.net/en_US/all.js  ads2:639
GET http://platform.linkedin.com/in.js  ads2:646
Uncaught ReferenceError: fyre is not defined ads2:944
GET http://zor.livefyre.com/wjs/v3.0/javascripts/livefyre.js  ads2:925
GET http://widget.uservoice.com/MgRuFTmArYOqweeXBEbikg.js  ads2:918
GET http://a.adroll.com/j/roundtrip.js  ads2:980
`

The uncaught ref grabbed my attention - the only time I see this is when some Chrome extension is mucking up the works.

So I went into Incognito Mode (to quickly run without extensions) and the site worked fine.

Now it's time for a binary search.  I disable half my extensions and found the problem went away.  Then I enabled half of those and the problem came back.

Since it was between Buffer and avast! - I figured it must be avast trying to be helpful.

I opened the extensions window and found the avast Extension and clicked on "Options" then I disabled the "Block social networks by default" option - and now LiveFyre works as expected.

[![avast-off](http://www.roberthorvick.com/wp-content/uploads/2013/11/avast-off.png)](http://www.roberthorvick.com/wp-content/uploads/2013/11/avast-off.png)
