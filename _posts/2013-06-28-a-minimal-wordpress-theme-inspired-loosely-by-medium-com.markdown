---
author: admin
comments: true
date: 2013-06-28 18:40:01+00:00
layout: post
link: https://roberthorvick.com/2013/06/28/a-minimal-wordpress-theme-inspired-loosely-by-medium-com/
slug: a-minimal-wordpress-theme-inspired-loosely-by-medium-com
title: A minimal WordPress theme inspired (loosely) by Medium.com
wordpress_id: 186
categories:
- Programming
---

![The bad artists imitate, the great artists steal](http://www.roberthorvick.com/wp-content/uploads/2013/06/great-artists-steal.jpg)

_Update:_ The theme code (such as it is) is available on [GitHub](https://github.com/bubbafat/SimplyReadable).


### Medium is beautiful. This is not.


When I first saw [Medium.com](http://medium.com) I was blown away. The large, readable, fonts on an uncluttered canvas were not just aesthetically pleasing but much easier to read than most other blogs. Looking deeper, though, it did not take long to realize that the true value of Medium was not simply the design but the quality of the content. Not just anyone can write for Medium. The bar has been set high and it shows. The signal/noise ratio is exceptional.

When linked from Twitter, I am significantly more likely to read something on Medium than most other sites.


#### I know my place in this relationship


My relationship with Medium is, and will likely remain, that of an information consumer. However, that does not mean I shouldn't create content nor does it mean my content must be ugly or hard to read. To that end I spent a little time last night working on a very (VERY) basic WordPress theme that is somewhat Medium-ish but not really. Prior to last night I had never looked at the structure of a WP template and I am not a php developer (in fact, this is probably the most I've ever used it). Needless to say this is not meant to be a general purpose theme. It is something that I am working on for me.

I started with a few goals I borrowed from looking at Medium:



 	
  * Large, readable, fonts. 22pts specifically.

 	
  * Simple layout

 	
  * Looks reasonable on mobile

 	
  * No comments (let’s talk on Twitter)


For fonts I choose a combination of [Source Sans Pro](http://www.google.com/fonts/specimen/Source+Sans+Pro) and [Open Sans](http://www.google.com/fonts/specimen/Open+Sans). I plan to tweak this a little over time. They might be a little too similar – but they are easy for my eyes to follow and that’s important to me.

The layout is attempting to optimize for the pattern of “picture up front, single column, not a lot of markup”.

For mobile, I’m just using screen width specific CSS to force max widths and a few sizing tweaks. I will probably end up doing more as I discover bugs and learn better practices. I also spent a little time making print look OK. No great, but I wanted something that would look ok if it was imported in to Pocket or Readability.

As for comments – I have removed them for now while I figure out what I want to do. More than 99% of the comments I got last year were flagged as spam. Of the remaining 1%, very few were anything but “Thanks!” – so … why not just take it to Twitter? I might regret this decision. If I do, I’ll add them back.

_EDIT: I added comments back but this time using Disqus._

The 10 Step process I used was:



 	
  1. Mock up basically what I wanted in a static HTML and CSS file

 	
  2. Download the [Blank](http://themeclubhouse.digwp.com/index.php?wptheme=BLANK%20Theme) template from [DiW](http://digwp.com/).

 	
  3. Move my style ideas into the theme [style.css](http://www.roberthorvick.com/wp-content/themes/simplyreadable/style.css).

 	
  4. Remove comments

 	
  5. Add some custom navigation

 	
  6. Rinse and repeat on style changes until I was minimally happy

 	
  7. Publish to RobertHorvick.com

 	
  8. Notice a bunch of things that were broken

 	
  9. Step 6-9 a few times

 	
  10. Drink a [Barrel Aged Bourbon Vortex II](http://www.pisgahbrewing.com/beer/seasonal) from [Pisgah](http://www.pisgahbrewing.com/).


And now I’m at what I consider a minimally acceptable result. The text is readable, the mobile reading is much better than what I had before, the navigation is terrible but the 404 page (which is actually what the [Archive](http://roberthorvick.com/archive) link above is – hackity-hack-hack).

Check out how you can [get custom acrylic prints](https://www.bumblejax.com/custom-acrylic-prints/) for your photos and renovate your web pages.

A bunch more needs to be done but it can wait until I have more beer.


photo credit: [khrawlings](http://www.flickr.com/photos/khrawlings/3622143862/) via [photopin](http://photopin.com) [cc](http://creativecommons.org/licenses/by/2.0/)
