---
author: admin
comments: false
date: 2013-07-05 06:58:38+00:00
layout: post
link: https://roberthorvick.com/2013/07/05/fixed-camtasia-7-1-crashing-during-startup-splash-screen/
slug: fixed-camtasia-7-1-crashing-during-startup-splash-screen
title: 'Fixed: Camtasia 7.1 crashing during startup splash screen'
wordpress_id: 240
categories:
- '#ifixedit'
---

I was having an issue where I would start Camtasia, the splash screen would come up and, about 90% of the time, the application would terminate.  Sometimes it worked.  Usually it didn't.

I started my search and found this support post at Techsmith: [https://support.techsmith.com/entries/22891787-Camtasia-Studio-Crashes-Right-After-Splash-Screen-Main-Window-Never-Opens](https://support.techsmith.com/entries/22891787-Camtasia-Studio-Crashes-Right-After-Splash-Screen-Main-Window-Never-Opens).

_It did not help._

So I looked for a log.  I didn't see anything in the %programfiles%\techsmith folder so I moved on to %temp% (which on my machine was C:\Users\Robert\AppData\Local\Temp).

In the temp folder I found a file that had been created at the same time as the crash: G2MCodec.txt

I open it up, scroll to the bottom and find this gem:


    
    
    [CamtasiaStudio] ...
    First chance EXCEPTION_ACCESS_VIOLATION at 5D1FD578
    *** Crash ***
    Exception: C0000005 at 5D1FD578 
    in C:\Program Files (x86)\Citrix\GoToMeeting\969\G2M.dll
    ACCESS_VIOLATION while reading 00000038
    Loaded dbghelp.dll ver. 6.01.7601.17514
    EBP walk -> 5D1FD578; ...
    



Now I may just be a small town [country chicken](http://www.youtube.com/watch?v=gQpaoLpKrMg) trying to make it in this crazy mixed up world, but I know a smoking gun when I see one.

Apparently GoToMeeting is injecting a little bit of suck into Camtasia.

Only one thing to do in a case like this.

Uninstall it. Plain and simple.

And now Camtasia works great, [every time](http://www.youtube.com/watch?v=pjvQFtlNQ-M).
