---
author: robert
comments: true
date: 2009-06-30 00:30:12+00:00
layout: post
link: https://roberthorvick.com/2009/06/29/converting-the-erlang-directory-walker-to-multi-process-design/
slug: converting-the-erlang-directory-walker-to-multi-process-design
title: Converting the Erlang directory walker to multi-process design
wordpress_id: 16
categories:
- Erlang
tags:
- Erlang
---

In my [previous post](http://www.roberthorvick.com/2009/06/29/walking-the-directory-tree-in-erlang/) I wrote a simple file system walker using Erlang for the purposes of getting my head around the syntax.  To start thinking more in an Erlang mindset (though in a _very_ contrived manner) in this post I convert the file system walker to use two processes.  The first process ("walk") performs the file system walking and the second ("visit") processes each found item (i.e. prints the full item path name).

Something along the lines of this...

![walk-visit](http://www.roberthorvick.com/wp-content/uploads/2009/06/walk-visit1.png)

Since walk and visit are the process entry points they need to be in the export list (by the way - forgetting to do this does not cause a compiler error in erl - why is that?) and we need a new entry function to create the new processes (start/1).


    
    
    start(Path) ->
    	Visit_PID = spawn(walkproc, visit, []),
    	spawn(walkproc, walk, [Path, Visit_PID]).
    



On line 2 we create the process whose pid is assigned to Visit_PID.  This process calls the visit() function with 0 parameters.  At this point visit is running and waiting to receive a message.

On line 3 we spawn off another process.  This process calls the walk/2 function as walk(Path, Visit_PID).  Since the walk function is doing the file system walking it makes sense that it will be the one sending the first message.  Because of this it needs to know the PID of the process to send the message to.

visit is a very straight forward function.  It receives a message, processes it, sends a response and recurses (rinse and repeat).


    
    
    visit() ->
    	receive
    		{Path, Walk_PID} ->
    			io:format("~s~n", [Path]),
    			Walk_PID ! next,
    			visit()
    	end.
    



walk is very similar (and has not changed substantially from our previous version.  it starts by firing a message off to visit with the Path passed as a parameter.  Next it waits for the "next" message.  Once it receives that it gets the next file system entry and recurses.


    
    
    walk(Path, Visit_PID) ->
    	Visit_PID ! {Path, self()},
    	receive
    		next ->
    			FileType = file_type(Path),
    			case FileType of
    				file ->
    					ok;
    				symlink ->
    					ok;
    				directory ->
    					Children = filelib:wildcard(Path ++ "/*"),
    					lists:foreach(fun(P) -> walk(P, Visit_PID) end, Children)
    			end
    	end.
    



The other methods (file_type and is_symlink) have not changed.

I enjoyed how easy it was to convert to a multi-process approach and am looking forward to moving to a solution that uses RabbitMQ.
