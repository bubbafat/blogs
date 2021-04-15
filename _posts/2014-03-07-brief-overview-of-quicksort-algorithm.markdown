---
author: admin
comments: false
date: 2014-03-07 14:13:56+00:00
layout: post
link: https://roberthorvick.com/2014/03/07/brief-overview-of-quicksort-algorithm/
slug: brief-overview-of-quicksort-algorithm
title: 'Quicksort: A Brief Overview'
wordpress_id: 343
categories:
- Programming
---

Quicksort is pretty much _the_ general purpose sorting algorithm.  Sure, it's not the best choice in all cases but it is a great starting point in most cases.

The thing is - a lot of people don't know how it works.  Either they never learned or they are so far removed from school that they have forgotten.  Sure, this is OK.  The folks writing your library of choice probably know how to do it.  

I am currently working on a new course for [Pluralsight](http://pluralsight.com/training/Authors/Details/robert-horvick) where I am covering algorithms and data structures in C++.  Module 3 covers some common searching and sorting algorithms including quicksort.  I wanted to share a little preview of what you can learn from this course (and also from my previous [algorithms and data structures](http://pluralsight.com/training/Courses/TableOfContents/ads-part1) course).



## Quicksort Refresher



Pre-condition: a range of unsorted data.





  1. Pick a partition point somewhere in the sortable range.


  2. Move the smaller values left and larger values right (of the partition point)


  3. Repeat this process for the unsorted range to the left and right



It's really that simple!



## Prove it!



No problem.  Let's work through the first iteration of the algorithm.

We start with a range of unsorted data.

[![unsorted data width=](http://www.roberthorvick.com/wp-content/uploads/2014/03/quicksort-start.png)](http://www.roberthorvick.com/wp-content/uploads/2014/03/quicksort-start.png)

The first thing we do is pick a partition point.

Pick anything.  The left most value.  The right most value.  The one in the middle.  A random one.  It doesn't matter.  Sure, there are partition points that are more ideal than others - but let's keep it simple and just pick a value.

_We'll pick 4._

[![quicksort partitions the unsorted range](http://www.roberthorvick.com/wp-content/uploads/2014/03/quicksort-part.png)](http://www.roberthorvick.com/wp-content/uploads/2014/03/quicksort-part.png)

Now we are going to move all the values less than 4 to the left of the partition point, and all the values greater than 4 to the right of the partition point.  In this case, we are going to move the 8 and 5 to the right of the partition point.

[![quicksort swaps the values](http://www.roberthorvick.com/wp-content/uploads/2014/03/quicksort-swap.png)](http://www.roberthorvick.com/wp-content/uploads/2014/03/quicksort-swap.png)

Think about what we now know about our partition point.





  1. Every value to the left is less than, or equal to, it


  2. Every value to the right is greater than, or equal to, it



That one value, the partition point, is exactly where it belongs.  Sure, things to the left and right are still unsorted but that one value is correct.

To commemorate this occasion, let's paint it green.

[![quicksort is complete](http://www.roberthorvick.com/wp-content/uploads/2014/03/quicksort-done.png)](http://www.roberthorvick.com/wp-content/uploads/2014/03/quicksort-done.png)

Exciting, right?



## But wait ... now we have two unsorted ranges!



Uh, yeah ... that's the whole point!  Quicksort is a divide and conquer algorithm.  We divided (partitioned) and conquered (sorted the one value).  Now we can repeat that exact same process on the remaining sets ([3, 2, 1] and [5, 8, 6, 7]).

Here's the whole process:



[Animated Quicksort](http://vimeo.com/88438734) from [Robert Horvick](http://vimeo.com/user25733081) on [Vimeo](https://vimeo.com).
