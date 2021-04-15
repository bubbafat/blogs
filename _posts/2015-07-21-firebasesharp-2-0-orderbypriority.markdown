---
author: admin
comments: true
date: 2015-07-21 13:00:55+00:00
layout: post
link: https://roberthorvick.com/2015/07/21/firebasesharp-2-0-orderbypriority/
slug: firebasesharp-2-0-orderbypriority
title: FirebaseSharp 2.0 - orderByPriority
wordpress_id: 491
categories:
- Firebase
- Programming
---

Using Firebase you can provide a priority for a data item.  The JSON would look like this:


    
    
    {
        'item1': {
            'data': 'foo',
            '.priority': 1
        },
        'item2': {
            'data': 'bar',
            '.priority': 3
        },
        'item3': {
            'data': 'baz',
            '.priority': 2
        },
    }
    



Notice how the priority is embedded in the JSON.  _If you are using the JavaScript API the priority is not returned in the data snapshot JSON object, but you can get it from the snapshot.  FirebaseSharp 2.0 (at least for now) does include it in the returned snapshot._ I plan to fix that but not yet.

With this JSON example, if we used ref.orderByPriority we should get the items back in ascending priority order item1 -> item3 -> item2.

This is actually pretty powerful.  You could drain a priority queue like this:


    
    
    var root = app.Child("/");
    var query = root.OrderByPriority().LimitToFirst(1).On("value", 
        (snap, child, context) => {
            // this is the highest priority item
            // remove it when processed and you'll get the next one
        });
    



Note: I haven't tried this in the C# library because I'm pretty sure it would not work right now.  I think the subscription events need to fire from a background thread to avoid re-entrancy issues in the On handler.  Think about it ... when On("value") fires, if you then called root.Delete(snap.Path) to remove node you just processed, that would end up calling On with the new high-priority value ... but it would be a recursive call.  Given a sufficiently large queue you'd blow your stack space.  In Node we'd just schedule the event with nextTick, setTimeout or setImmediate (I'd need to think about it more) - but in C# I'll need to think about other options.  Probably a blocking queue and a long-running task.

When sorting priorities you just need to know a few things:





  1. A missing (null) priority sorts first


  2. numeric priorities sort next (ascending)


  3. string's that are int32 numerics are treated as numeric


  4. strings (not int32) are sorted in lexicographical order (ascending)


  5. any matches (including null) are sorted by key name



Now I just need to get SetPriority and SetWithPriority working so we can actually use them!
