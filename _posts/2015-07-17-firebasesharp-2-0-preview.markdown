---
author: admin
comments: true
date: 2015-07-17 19:25:41+00:00
layout: post
link: https://roberthorvick.com/2015/07/17/firebasesharp-2-0-preview/
slug: firebasesharp-2-0-preview
title: FirebaseSharp 2.0 - Preview
wordpress_id: 476
categories:
- Firebase
- Programming
---

I've been working on a complete rewrite of the FirebaseSharp API for a while now - plugging away here and there as time permitted - and I think I've finally hit a point where I should share some of the progress.

I set out with the goal of replicating, as best as possible, the existing Node API.  I don't just mean class or method naming (though that is important).  In addition I meant things like caching, offline updates, re-synching when back online, reconnecting on failure, etc.

Ultimately the API should be very unsurprising to anyone who has used the Node API.

Here's an example that works today. The Node code is:


    
    
    var scoresRef = new Firebase("https://dinosaur-facts.firebaseio.com/scores");
    scoresRef.orderByValue().limitToLast(3).on("value", function(snapshot) {
      snapshot.forEach(function(data) {
        console.log("The " + data.key() + " dinosaur's score is " + data.val());
      });
    });
    



And the comparable FirebaseSharp code is


    
    
    FirebaseApp app = new FirebaseApp(new Uri("https://dinosaur-facts.firebaseio.com/"));
    
    var scoresRef = app.Child("scores");
    scoresRef.OrderByValue<int>().LimitToLast(3).On("value", (snapshot, child, context) => {
      foreach (var data in snapshot.Children) {
        Console.WriteLine("The {0} dinosaur\'s score is {1}",
                            data.Key, data.Value<int>());
       }
    });
    



In both cases the output is the same:


    
    
    The bruhathkayosaurus dinosaur's score is 55
    The linhenykus dinosaur's score is 80
    The pterodactyl dinosaur's score is 93
    



The code is pretty similar.  Create the ref, make the query ordering by value, limiting to the last 3 and then listening for the value update (which fires as soon as the cache is initially populated).

The big difference is that I have the FirebaseApp class - this is basically the front-end around the connections to firebaseio.com and the shared cache.  Why do I need that?

The problem is that Firebase has a proprietary Websocket protocol which they use both to limit the number of connections (no matter how many query refs you create) and to only receive data for subscribed areas (to keep the cache as small as possible).  I can't use that.  I'm stuck with the REST protocol.  The REST protocol is basically all-or-nothing.

This means that when you create your FirebaseApp you need to decide what is more important ... less data transfer or fewer connections.  In our example I connected to the root of dinosaur-facts and then subscribed to the "scores" child.  This means our cache will be at the root of dinosaur-facts and will include the entire tree.  On the flip-side, I'll reuse HTTP connections to limit the number of open connections to Firebase.  Since the dino data is tiny - I'm OK with this.

But what if I had created a second child using the app.Child(...) method?  Don't worry, it will share the same cache as the first child and will use the same socket connections.  What it won't share is the query filters (order by value, limit to last, etc).

And what if I had made a second FirebaseApp instance?  New cache, new connections.

_Remember: Firebase billing considers transfer and open connections._

If you have a huge dataset you don't want to transfer, you will need more FirebaseApp's (at targeted locations) to minimize your transfer - this will come at the cost of more connections (and slower synchronization between caches since Firebase will be the intermediary).  However if you have a small dataset then using a single FirebaseApp is probably your best bet.

Alright - that's enough for now.  

If you want to see more you can check out the [project on GitHub](https://github.com/bubbafat/FirebaseSharp/tree/v2cache).  If you really are interested feel free to fork and start digging in.

As I enable other scenarios I'll post more about them.
