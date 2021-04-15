---
author: robert
comments: true
date: 2009-06-29 13:43:14+00:00
layout: post
link: https://roberthorvick.com/2009/06/29/walking-the-directory-tree-in-erlang/
slug: walking-the-directory-tree-in-erlang
title: Walking the directory tree in Erlang
wordpress_id: 3
categories:
- Erlang
tags:
- Erlang
---

I'm learning Erlang.  I'll get into "why" in some other post - the purpose here is to share my first sample program and solicit feedback.  The purpose of the program is to start print the contents of a file system from the indicated point downwards (ignoring symlinks).

The application has a single module, walker, which exports walk/1.  The argument to walk/1 is the starting path.  For example:
`
> walker:walk("/home").
`

This method prints the path name, determines the type of the current path (file, directory or symlink), and then IFF the path is a directory it calls filelib:wildcard to get the children of the path and repeats the process on them.

    
    -module(walker).
    
    -include_lib("kernel/include/file.hrl").
    
    -export([walk/1]).
    
    is_symlink(Path) ->
    	case file:read_link_info(Path) of
    		{ok, #file_info{type = symlink}} ->
    			true;
    		_ ->
    			false
    	end.
    
    file_type(Path) ->
    	IsRegular = filelib:is_regular(Path),
    	case IsRegular of
    		true ->
    			file;
    		false ->
    			case is_symlink(Path) of
    				true ->
    					symlink;
    				false ->
    					directory
    			end
    	end.
    
    walk(Path) ->
    	io:format("~s~n", [Path]),
    
    	FileType = file_type(Path),
    	case FileType of
    		file ->
    			ok;
    		symlink ->
    			ok;
    		directory ->
    			Children = filelib:wildcard(Path ++ "/*"),
    			lists:foreach(fun(P) -> walk(P) end, Children)
    	end.


My questions about this module are:



	
  1. Does calling walk(P) in a foreach prevent tail recursion optimizations?

	
  2. Where I have "case FileType of" (in walk/1) is there a more succinct way to express that?

	
  3. Why doesn't read_file_info ever return file_info#type==symlink?

	
  4. How should this have really been done?


I'll be working on answering #1-3 on my way to learning #4 - but if you have any feedback I would love to hear it.

The next step is to make this message based and have the walk/1 method send messages to a consumer who will do the printing.

The next-next step is to  RabbitMQ and setup one producer and three consumers - one for files, one for directories and one for symlinks.  The walk/1 method will no longer print the file info but rather send the appropriate message and let the consumers print the messages.
