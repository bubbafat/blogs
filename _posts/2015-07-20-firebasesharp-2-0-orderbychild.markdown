---
author: admin
comments: true
date: 2015-07-20 00:58:59+00:00
layout: post
link: https://roberthorvick.com/2015/07/19/firebasesharp-2-0-orderbychild/
slug: firebasesharp-2-0-orderbychild
title: FirebaseSharp 2.0 - orderByChild
wordpress_id: 482
categories:
- Firebase
- Programming
---

Firebase [orderByChild](https://www.firebase.com/docs/web/api/query/orderbychild.html) allows your query to be ordered by (no surprises here) - the child value.  It uses a pretty straight-forward [set of rules](https://www.firebase.com/docs/web/guide/retrieving-data.html#section-ordered-data) to make this happen:





  1. Children with a null value for the specified child key come first.


  2. Children with a value of false for the specified child key come next. If multiple children have a value of false, they are sorted lexicographically by key.


  3. Children with a value of true for the specified child key come next. If multiple children have a value of true, they are sorted lexicographically by key.


  4. Children with a numeric value come next, sorted in ascending order. If multiple children have the same numerical value for the specified child node, they are sorted by key.


  5. Strings come after numbers, and are sorted lexicographically in ascending order. If multiple children have the same value for the specified child node, they are ordered lexicographically by key.


  6. Objects come last, and sorted lexicographically by key name in ascending order.



Their node example is:


    
    
    var ref = new Firebase("https://dinosaur-facts.firebaseio.com/dinosaurs");
    ref.orderByChild("height").on("child_added", function(snapshot) {
      console.log(snapshot.key() + " was " + snapshot.val().height + " meters tall");
    });
    



Producing the output:


    
    
    linhenykus was 0.6 meters tall
    pterodactyl was 0.6 meters tall
    lambeosaurus was 2.1 meters tall
    triceratops was 3 meters tall
    stegosaurus was 4 meters tall
    bruhathkayosaurus was 25 meters tall
    



How does this translate to FirebaseSharp 2.0?  Pretty closely.  


    
    
    FirebaseApp app = new FirebaseApp(new Uri("https://dinosaur-facts.firebaseio.com/"));
    
    var scoresRef = app.Child("dinosaurs").OrderByChild("height").On("child_added",
        (snapshot, child, context) => {
            Console.WriteLine("{0} was {1} meters tall", 
                snapshot.Key, snapshot["height"].Value<float>());
        });
    



The main thing to notice is that reading the "height" property uses a string indexer to get the child snapshot and then converts the child to a float.  I'm still thinking through the best way to do this but for now this works.

Oh - and the sample application produces the same output.
