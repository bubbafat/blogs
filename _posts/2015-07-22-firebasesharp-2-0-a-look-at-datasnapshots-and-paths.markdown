---
author: admin
comments: true
date: 2015-07-22 06:22:04+00:00
layout: post
link: https://roberthorvick.com/2015/07/22/firebasesharp-2-0-a-look-at-datasnapshots-and-paths/
slug: firebasesharp-2-0-a-look-at-datasnapshots-and-paths
title: FirebaseSharp 2.0 - A look at DataSnapshots (and paths)
wordpress_id: 494
categories:
- Firebase
- Programming
---

## Data Snapshots



When a On or Once callback is fired the callback is provided a snapshot of the data   returned from the query.

The [Firebase docs](https://www.firebase.com/docs/web/api/datasnapshot/) for data snapshots show the various properties and functions.  You can get the current key, create a new query ref from the current location, get the snapshot value (i.e.,JSON), and several other things.  Go read about them - it's good stuff.

I'll repeat and example they give in the docs - we start with this JSON:


    
    
    {
      "messages": {
        "-JqpIO567aKezufthrn8" {
          "uid": "barney",
          "text": "Welcome to Bedrock City!"
        },
        "-JqpIP5tIy-gMbdTmIg7": {
          "uid": "fred",
          "text": "Yabba dabba doo!"
        }
      }
    }
    



Then using the Node API you could use once to get the initial snapshot and then loop over all the children, extracting the key and children values for uid and text.


    
    
    var messagesRef = new Firebase("https://docs-examples.firebaseio.com/samplechat/messages");
    messagesRef.once("value", function(allMessagesSnapshot) {
      allMessagesSnapshot.forEach(function(messageSnapshot) {
        // Will be called with a messageSnapshot for each child under the /messages/ node
        var key = messageSnapshot.key();  // e.g. "-JqpIO567aKezufthrn8"
        var uid = messageSnapshot.child("uid").val();  // e.g. "barney"
        var text = messageSnapshot.child("text").val();  // e.g. "Welcome to Bedrock City!"
      });
    });
    



So what does this look like in the C# library?


    
    
    var query = app.Child("messages");
    query.Once("value", (snap, child, context) => {
        foreach(var msg in snap.Children()) {
            var key = msg.Key;
            var uid = msg.Child("uid").Value();
            var text = msg.Child("text").Value();
        }
    });
    



The snapshot usage is basically the same.  Instead of using .forEach you would a traditional (foreach) enumeration over .Children() - but otherwise things should look and feel pretty similar.



## Paths



I also wanted to briefly talk about paths.  When I say "path" I mean the full path from the root of the FirebaseApp.

In this example:


    
    
    FirebaseApp app = new FirebaseApp(new Uri("http://example.com"));
    var query = app.Child("foo/bar/baz");
    



The "path" of the query is "/foo/bar/baz".
The "key" of the query is "baz"
The "parent" of the query has the path "/foo/bar" and the key "bar"
The root of the FirebaseApp has a path of "/" and a null key.

It's important to remember a few things.





  1. Paths and keys are case sensitive


  2. Paths always use the "/" (forward slash) as their separator


  3. Paths start with a forward slash (though the path parsing logic is loose about that)


  4. Keys never contain a forward slash
  5. 

  6. Path parts (between the forward slashes) must confirm to JSON property name rules


  7. Whitespace at the slash boundaries is trimmed (e.g., "  /foo / bar   " becomes "/foo/bar")


  8. Double slashes are ignored (e.g., "//foo//bar" becomes "/foo/bar")



Internally the library never stores paths (directly) as strings - rather it uses a specialized path object that can enforce these rules and perform normalization.  This was a recent change and has helped with a handful of bugs where path, key and "child name" (e.g., snap.Child("foo")) were being used incorrectly.  It would manifest in weird little bugs where everything seemed to work but a snapshot might have a key of "foo/bar" or a path of "//foo/bar".  

If you're at all curious, check [this change](https://github.com/bubbafat/FirebaseSharp/commit/2b7361153726c569901ab2ec082e716b3fd05b97).
