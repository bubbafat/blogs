---
author: admin
comments: false
date: 2014-03-25 13:00:01+00:00
layout: post
link: https://roberthorvick.com/2014/03/25/pluralsight-case-powershell/
slug: pluralsight-case-powershell
title: The Case for PowerShell (Reviewed)
wordpress_id: 381
categories:
- Pluralsight
- Training Tuesday
---

[The Case for PowerShell](http://pluralsight.com/training/Courses/TableOfContents/case-for-powershell) is a new Pluralsight course by [Mark Minasi](http://pluralsight.com/training/Authors/Details/mark-minasi) where he, not surprisingly, makes a case for PowerShell.

The very first thing I noticed about this course was that it is using a very different structure than most Pluralsight courses.  Mark is presenting this live to an audience in what I assume is Ballroom A of the Schenectady Airport Hilton.

I think this approach worked well for this topic.  The course is short, you could cram it into a lunch hour if you have a loose definition of "hour".  His personality and interest in the topic come through very naturally.  Mark is clearly very comfortable giving this talk.

_Fair Warning:_ there are a few bad jokes.



## My Dirty Secret...



I have intentionally avoided PowerShell for years.

I'm used to the command shell and bash.  I know how to do what I need.  I'm resistant to change.

But then something happened... a client required PowerShell.

Their whole build environment is based around it.

Ok - fine.  I can use dir, right?  Indeed I can.

So let's go and find that file RenderingEngine.cs ...


    
    
    PS > dir /s RenderingEngine.cs
    
    Get-ChildItem : Cannot find path 'C:\Source\s' 
                    because it does not exist.
    





## The Hell?



They took away my dir!

For a long time I just went to the command shell (cmd /K) when I needed to do things like that.

But I'm done with that.  I've made a commitment to not using the command shell for things like that.  I'm going to learn the PowerShell way and start embracing it.

As Mark said: "learn-powershell or leave-industry"



## (My) Key Lesson from The Case for PowerShell



Mark's course really isn't a lesson in PowerShell.  He is making a case for why you should care and, along the way, teaching a few basics to make his point.

The basics I cared most about were:





  * Command Structure (verb-singularnoun)


  * Get-Command



Now that I understand the structure and I understand how to find commands, there really is no excuse for dropping back to the command shell.

It's not about knowing all the cmdlets - it's about knowing enough to know how to find the cmdlet you need at the moment you need it.

Yeah - basic, I know.  This is Day-1 stuff.

But it's Day 1 so cut me some slack.
