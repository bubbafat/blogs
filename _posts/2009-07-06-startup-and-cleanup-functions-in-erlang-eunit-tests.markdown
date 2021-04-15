---
author: robert
comments: true
date: 2009-07-06 04:22:16+00:00
layout: post
link: https://roberthorvick.com/2009/07/06/startup-and-cleanup-functions-in-erlang-eunit-tests/
slug: startup-and-cleanup-functions-in-erlang-eunit-tests
title: Startup and Cleanup functions in Erlang EUnit tests
wordpress_id: 62
categories:
- Erlang
tags:
- Erlang
- eunit
---

I've got the basic grasp on Erlang but before I can do anything meaningful I need to learn how to test my code  EUnit is the de-facto test framework.  There are a ton of great sites that talk about testing with EUnit - I have linked to several at the end of this post.  I'm going to focus on the problem I ran into - needing to run some startup and cleanup code around the test suite.

I started by writing a very simple dictionary that lived in it's own process.  The interface is:


    
    
    start() -> ok
    stop() -> ok
    write(Key, Element) -> ok
    delete(Key) -> ok
    read(Key) -> {ok, Value} | not_found
    



Calling start() spawns and registers the process the dictionary lives in.  Calling stop ends the process.  The rest do what you expect.

I started by writing some basic tests for each function but I didn't really think it through.  The start test ran somewhere in the middle so all the tests prior to start either failed or timed out (because I was waiting on a receive that would never come since the process wasn't started).  Very quickly I smacked my forehead and said "I need the test suite to have a pre and post function to start and stop the process".

It took about 15 minutes of reading to realize that I needed to create a test generator with a setup tuple that defined the startup, cleanup and list of tests to run.

Conceptually it was easy to understand but getting the syntax right was a royal pain.  Basically you do this (I'm not sure if giving the function arity is the preferred method but it saved some keystrokes so I went with it):


    
    
    my_db_test_() ->
      {spawn,
        {setup,
          fun start/0,
          fun stop/1,
          [
            fun write_new/0,
            fun write_existing/0,
            fun read_existing/0,
            fun read_missing/0,
            fun delete_existing/0,
            fun delete_missing/0
          ]
        }
      }.
    



The test methods are exactly what you'd expect so I won't repeat them.  The syntax feels a little foreign and uncomfortable but it's very easy to understand and I totally get why it's written this way.  I'm just so used to [MSTest](http://en.wikipedia.org/wiki/MSTest) attributes and [RSpec](http://rspec.info/) that this new way is going to take a night or two to feel like an old friend.


## Some Eunit resources


[Prag Dave - Test-First Word Wrap in Erlang](http://pragdave.pragprog.com/pragdave/2007/04/testfirst_word_.html)

[EUnit - a Lightweight Unit Testing Framework for Erlang](http://svn.process-one.net/contribs/trunk/eunit/doc/overview-summary.html)

[EUnit Reference Manual](http://erlang.org/doc/apps/eunit/index.html)

[Getting Started: No Consoles!](http://salientblue.com/codenotes/?name=erl_start)
