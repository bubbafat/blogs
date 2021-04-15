---
author: admin
comments: false
date: 2014-03-28 15:27:43+00:00
layout: post
link: https://roberthorvick.com/2014/03/28/firebase-events-in-the-net-client/
slug: firebase-events-in-the-net-client
title: Adding Firebase events to the .NET client
wordpress_id: 413
---

Version 1.0.0.2 of the Firebase .NET client library [FirebaseSharp](https://www.nuget.org/packages/FirebaseSharp/) has been released and includes a significant improvement - actual events when monitoring a location.

Firebase events now fire when an item is added, removed or updated and the update is received from the streaming get operation.



## Firebase Events in Action




    
    
    Firebase fb = new Firebase("https://[YOUR FIREBASE URL].firebaseio.com/");
    
    fb.GetStreaming("path/to/monitor", 
        added: (s, args) => AddedItem(args),
        changed: (s, args) => UpdatedItem(args),
        removed: (s, args) => RemovedItem(args));
    
    
    private void AddedItem(ValueAddedEventArgs args)
    {
        // process addition
    }
    
    private void RemovedItem(ValueRemovedEventArgs args)
    {
        // process removal
    }
    
    private void UpdatedItem(ValueChangedEventArgs args)
    {
        // process update
    }
    



The add event includes the path and new item (json), the updated event includes the path both the new and old data, and the removed event includes only the path of the removed item.



* * *

And a sample



I mentioned a drawing sample [yesterday ](http://www.roberthorvick.com/2014/03/26/firebase-net-client-library/) - I have added this sample to the [FirebaseSharp repo](https://github.com/bubbafat/FirebaseSharp/) right [here](https://github.com/bubbafat/FirebaseSharp/tree/master/samples/draw).

It has a few updates.





  * It [uses firebase events](https://github.com/bubbafat/FirebaseSharp/blob/master/samples/draw/MainWindow.xaml.cs#L70) to get the add/remove/update notifications


  * It now [handles deletes](https://github.com/bubbafat/FirebaseSharp/blob/master/samples/draw/MainWindow.xaml.cs#L98)


  * It [performs updates](https://github.com/bubbafat/FirebaseSharp/blob/master/samples/draw/MainWindow.xaml.cs#L124) in-place (rather than drawing a new rect)



Not major changed on the surface - but meaningful ones under the covers.



## The caching layer



Firebase streaming GETs don't tell you things like "this items was removed" or "this item was updated" - rather they simply send the update to apply.  To figure out what this means, you need to have a cache that represents the world as it appears to that streaming get.

So - I added a [cache](https://github.com/bubbafat/FirebaseSharp/blob/master/src/FirebaseSharp.Portable/FirebaseCache.cs).

Is is a good cache?  Almost certainly not.  It's a simple tree structure that approximates the JSON objects that stream back and kicks off event.

As firebase events come in, the cache updates itself as appropriate and then fires the necessary event.

I haven't spent much time making it pretty but the basic testing is promising.  It is pretty thin, makes reasonable efforts to minimize allocations, and since only the single response object ever writes to it, I can get away with serializing access to it ... or I claim I can.

The drawing app shows real-time updates still occurring and some basic profiling shows that less than 1% of the drawing app time is spent managing the cache so that seems like a good starting point to me.

I could do a bit more to be aggressive about the allocations of cache nodes (reuse would be easy) but I'd rather find bugs before making it any faster.



## Cached Writes?



The official Firebase clients also cache writes before synchronizing to the server.  Long term I would need to do this for parity and event performance, but short-term, I'm letting server-provided events drive the cache updates.

it's good enough for me.  If it is not good enough for you ... well ... help out or wait until they release a proper library. :)
