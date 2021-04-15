---
author: admin
comments: true
date: 2015-07-27 17:08:46+00:00
layout: post
link: https://roberthorvick.com/2015/07/27/firebasesharp-2-goes-alpha/
slug: firebasesharp-2-goes-alpha
title: FirebaseSharp 2 goes alpha
wordpress_id: 500
categories:
- Firebase
- Programming
---

FirebaseSharp 2 has gone into an early alpha.  This post is using version [2.0.1-alpha](https://www.nuget.org/packages/FirebaseSharp/2.0.1-alpha) - and the latest package is always available [here](https://www.nuget.org/packages/FirebaseSharp/). Just remember that because it is an alpha release you have to instruct nuget to get [pre-release packages](https://docs.nuget.org/create/versioning#installing-prerelease-packages).

Here are some of the latest changes...



## Strongly Type Snapshot Values



Using the Firebase [crypto currencies data set](https://www.firebase.com/docs/open-data/cryptocurrencies.html) we could model a class in C#:


    
    
    struct currency
    {
        public float last;
        public float ask;
        public float bid;
        public string _credits;
        public string _updated;
    }
    



And now we can get an instance of that directly from our snapshot - for example here we'll use the child_changed event off the crypto root to get changed events for the three support currencies - bitcoin, litecoin and dogecoin.


    
    
    using (var app = new FirebaseApp(new Uri("https://publicdata-cryptocurrency.firebaseio.com/")))
    {
        var currencyRef = app.Child("/");
    
        SnapshotCallback showPrice = (snap, child, context) =>
        {
            var c = snap.Value<currency>();
            Console.WriteLine("{0}: {1} / {2} / {3}", snap.Key, c.bid, c.ask, c.last);
        };
    
        currencyRef.On("child_changed", showPrice);
    
        Console.ReadKey();
    }
    



Notice the call to snap.Value() - it will perform the conversion for you.



## Adding Strong Types Too!



Let's flip that example a bit - what if I wanted to add something that was a strongly type object?

No problem!  Let's define some types...


    
    
    struct Name
    {
        public string first;
        public string last;
    }
    
    struct Person
    {
        public Name name;
        public int age;
    }
    



And now use them.


    
    
    using (var app = new FirebaseApp(new Uri("https://yourfirebase.url/")))
    {
        var people = app.Child("people");
        var robert = new Person
        {
            name = new Name
            {
                first = "Robert",
                last = "Horvick",
            },
            age = 38,
        };
    
        people.Push(robert);
    
        Thread.Sleep(TimeSpan.FromSeconds(10));
    }
    



And what did that push?  Exactly what you'd expect:

![addobjectresult](http://www.roberthorvick.com/wp-content/uploads/2015/07/addobjectresult.png)



## Server Timestamps



The syntax here is still being worked out - but basically you can now use the [server timestamp](https://www.firebase.com/docs/web/api/servervalue/timestamp.html) value.

Let's extend our Person example to include the server timestamp that the record was created.  We'll start by modifying our Person struct to include a timestamp member:


    
    
    struct Person
    {
        public Name name;
        public int age;
        public ServerValue.ServerTimestamp created;
    }
    



I'm not thrilled with how this looks - but let's roll with it for now.

And now we modify our creation to initialize the member:


    
    
    var robert = new Person
    {
        name = new Name
        {
            first = "Robert",
            last = "Horvick",
        },
        age = 38,
        created = new ServerValue.ServerTimestamp(),
    };
    



What happens now is that we send up a JSON object that looks like this:


    
    
    {  
      "-JvFOCRHPFLy34gG4Fof":{  
        "age":38,
        "created": { '.sv': 'timestamp' },
        "name":{  
          "first":"Robert",
          "last":"Horvick"
        }
      }
    }
    



And when the server sees the timestamp placeholder is replaces it with the exact server time.



## Callbacks are Now Fired Async



All callbacks (including the initial On and Once callbacks) are now called from background threads.  This means that you can now do things like:


    
    
    query.On("value", (snap, child, context) => {
        snap.Ref().Remove();
    });
    



Before this _could_ have ended up causing problems because (a) the callback might be fired under a locked context causing a deadlock if you did anything async in the callback and, (b) On/Once might not return in a timely manner (or at all) if the callback (either it's - or one in front of it) was blocking or threw an exception.

This should be transparent to you but is something you might care about if you are worried about the timeliness of return values.

Oh ... and you should also know that the initial On/Once subscriptions won't fire at all until the initial data download completes.  This is especially important if you create the FirebaseApp and immediately create a Once subscription.  If the library does not wait for the data to populate them you would almost assuredly get back nothing.



## A slew of differences



There are a lot of difference between this library and the official APIs - some are things I can't easily work around (no access to the Web Sockets API) and some are things I could resolve but choose another path.

Some examples:





  * No transactions (Web Sockets)


  * No OnDisconnect handlers (Web Sockets)


  * Limit auth options (REST only support tokens)


  * You can do more than one EqualTo filter


  * StartAtKey/EndAtKey - they exist here as query filters, not as parameters to other filters


  * Mega-cache.  The entire tree at your FirebaseApp is cached (Web Sockets)


  * More connections.  One connection for streaming.  One for queries.  (Web Sockets)



There are more - I will attempt to document them later.

Please install it - or better yet, [fork the source](https://github.com/bubbafat/FirebaseSharp) and help out!
