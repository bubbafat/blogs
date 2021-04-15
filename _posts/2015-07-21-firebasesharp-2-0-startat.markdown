---
author: admin
comments: true
date: 2015-07-21 00:19:08+00:00
layout: post
link: https://roberthorvick.com/2015/07/20/firebasesharp-2-0-startat/
slug: firebasesharp-2-0-startat
title: FirebaseSharp 2.0 - StartAt
wordpress_id: 486
categories:
- Firebase
- Programming
---

Today we'll look at the StartAt method on the new FirebaseSharp 2.0 library.

The [startAt](https://www.firebase.com/docs/web/api/query/startat.html) function provides a mechanism to limit sorted query results to values which meet or exceed the provided value.  For example - here we're using the Javascript API to get all the dinosaurs that are at least 3 meters tall:


    
    
    var ref = new Firebase("https://dinosaur-facts.firebaseio.com/dinosaurs");
    ref.orderByChild("height").startAt(3).on("child_added", function(snapshot) {
      console.log(snapshot.key())
    });
    




    
    
    triceratops
    stegosaurus
    bruhathkayosaurus
    



FirebaseSharp 2.0 has the same basic syntax (note: creating the FirebaseApp instance has been removed from this sample and won't be shown in future posts unless the topic is specifically about the FirebaseApp type).


    
    
    var sref = app.Child("dinosaurs").OrderByChild("height").StartAt(3).On("child_added",
        (snapshot, child, context) => {
            Console.WriteLine(snapshot.Key);
        });
    }



The output is the same.

What's important to understand about this is how the filters are related.

Think about if the dinosaur JSON looked like this:


    
    
    {
        'dino1': {
            weight: 200,
            height: 2
        },
        'dino2': {
            weight: 500,
            height: 8
        },
        'dino3': {
            weight: 350,
            height: 4
        },
    }
    



How does "StartAt(3)" know whether to start at the height of 3 or the weight of 3?  Well - we know from the context of the OrderByChild("height") that precedes it.  When you begin a query you get back an instance of a FirebaseQuery (not a public type).  When the query is executed, filters that apply to specific parts of the model (e.g., a child, value, key or priority), set the query context that flows to the filter.  When the filter executes it passes the context through (this is necessary so that StartAt and EndAt can be chained, both applying to the same context).

This area is going to have a lot of churn in the next while - but the ideas are coming together and the tests are working.

If you are thinking of doing work in Firebase using C# please let me know.  I'd love to get feedback on the API.  There are times I think I should make it more ".NET-y" instead of modeling it after the Firebase API ...but then I decide to not worry about it for now.
