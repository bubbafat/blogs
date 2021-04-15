---
author: admin
comments: true
date: 2015-07-30 20:32:52+00:00
layout: post
link: https://roberthorvick.com/2015/07/30/using-firebase-rules-for-near-atomic-updates/
slug: using-firebase-rules-for-near-atomic-updates
title: Using Firebase rules to approximate REST API atomic updates
wordpress_id: 508
categories:
- Firebase
- Programming
---

The Firebase REST API has many limitations - one of which is that it has no support for transactions.  But what if I'm (exclusively) using the REST API and want to make sure that if two clients try to change the same object concurrently that only one will win?

Well - a simple way is to add a monotonically incrementing "Version" to your object like this:


    
    
    public class TrxItem
    {
        public string Data;
        public int Version;
    }
    



And then add a Firebase validate rule that says that the if the object already exists that the Version value must be 1 greater than the current version.  For example:


    
    
    {
      "rules": {
        "trx": {
          "$item": {
            ".validate": "!data.exists() || (newData.child('Version').val() === data.child('Version').val() + 1)"
          }
        }
      }
    }
    



Now let's create a new object and save it to Firebase:


    
    
    // in production this requires a using block
    var app = new FirebaseApp(/* your connection details */);
    
    // we're creating the child "item1" under trx
    var item1Ref = app.Child("trx/item1");
    
    // Version defaults to 0
    var item1 = new TrxItem {
        Data = "Initial data",
    };
    
    item1Ref.Set(item1);
    



Now in Firebase we have data like this:


    
    
    {
      "trx": {
        "item1": {
          "Data": "Initial data",
          "Version": 0
        }
      }
    }
    



So what would happen if we tried to change the Data value but leave Version at 0?  Well- let's go to the Firebase simulator and find out:


    
    
    Attempt to write Success({"Data":"Updated data","Version":0}) to /trx/item1 with auth=Success(null)
    	/:.write: "true"
    		=> true
    	/trx/item1:.validate: "!data.exists() || (newData.child('Version').val() === data.child('Version').val() + 1)"
    		=> false
    
    Validation failed.
    Write was denied.
    



We have write permissions but the validation failed.  Why?

Because data.child('Version').val() was equal to 0 (this is the value currently in the database) and newData.child('Version').val() also equalled 0 (this is the new value) - but the rule requires it to be one greater than the previous value (i.e., 1).

We can observe this error like so:


    
    
    item1Ref.Set(item1, error => {
        if (error != null) {
            // handle error using error.Code
        }
    });
    



Specifically the error code is Unauthorized access (HTTP 401).

_Note: the current FirebaseSharp 2.0 implementation will update the local cache before synching and does not recognize the sync failure to rollback the changes - I am thinking of a strategy to deal with this - it probably means creating a "transaction-like" mode where local updates are applied when the server broadcasts them and not during local updates._

To update our value we simply need to bump the version number before saving it.  In this example we read the value with Once and then update it in the callback:


    
    
    item1Ref.Once("value", (snap, child, context) => {
        var newItem1 = snap.Value<trxitem>();
    
        // update the version number and data
        newItem1.Version++;       
        newItem1.Data = "Updated with version";
        
        snap.Ref().Set(newItem1, error => {
            // ensure it worked
        });
    });
    



That query will succeed (assuming no one else already updated the version) and the Firebase database will now look like:


    
    
    {
      "trx": {
        "item1": {
          "Data": "Updated with version",
          "Version": 1
        }
      }
    }
    



But if you really need transactional behaviors then you should probably perform those updates using one of the official libraries that support transactions natively.
