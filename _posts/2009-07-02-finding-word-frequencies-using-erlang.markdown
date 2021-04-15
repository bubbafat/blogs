---
author: robert
comments: true
date: 2009-07-02 04:24:26+00:00
layout: post
link: https://roberthorvick.com/2009/07/02/finding-word-frequencies-using-erlang/
slug: finding-word-frequencies-using-erlang
title: Finding word frequencies using Erlang
wordpress_id: 32
categories:
- Erlang
tags:
- Erlang
---

[Here's a follow-up post that provides a better implementation.](http://www.roberthorvick.com/2009/07/02/word-frequency-redux-erlang-list-comprehension-regex-and-list-folding/)

I wanted to try and bite off something a little larger today hitting a few areas that seem generally useful:



	
  1. Basic text [file](http://www.erlang.org/doc/man/file.html) operations

	
  2. Basic string operations

	
  3. Using the [gb_trees](http://www.erlang.org/doc/man/gb_trees.html) module

	
  4. Avoiding any usage of lists:foreach; instead using tail recursion (that whole "thinking in Erlang" thing).


The problem is to produce the word frequency of a specific text file and to print out the frequency information.  For example if a file overdunn.txt contained the text:

_"Dunn was over Unger and I was over Dunn."_ ([Capt. Oveur](http://www.imdb.com/character/ch0006136/quotes), [Airplane II: The Sequel)](http://www.imdb.com/title/tt0083530/)

The output would be:

`Dunn: 2
I: 1
Unger: 1
and: 1
over: 2
was: 2`

Word frequencies have some practical uses (fuzzy text matching, building tag clouds, etc).  So while it's a bit contrived it is the basis for something useful.

The code doesn't need a lot of explanation - so here you go ...


    
    
    -module(wordlist).
    
    -export([print_word_counts/1]).
    
    %% matches/* and words/1 From: http://www.trapexit.org/Matching_Words
    matches(H,{match,M}) -> matches(H,M,[]).
    matches(_,[],Acc) -> Acc;
    matches(H,[{I,L}|T],Acc) ->
        matches(H,T,[lists:sublist(H,I,L)|Acc]).
    
    words(String) -> matches(String,regexp:matches(String, "[A-Za-z0-1]+")).
    
    
    %% builds a tree of word/count pairs.  If the word does not exist in 
    %% the tree it is added with an initial value of 1.  If the word does
    %% exist the count is retrieved and incremented
    build_word_tree([], Tree) -> Tree;
    build_word_tree([W|R], Tree) ->
    	case gb_trees:is_defined(W, Tree) of
    		true ->
    			Count = gb_trees:get(W, Tree),
    			NewTree = gb_trees:update(W, Count + 1, Tree),
    			build_word_tree(R, NewTree);
    		false ->
    			NewTree = gb_trees:insert(W, 1, Tree),
    			build_word_tree(R, NewTree)
    	end.
    
    %% reads the next line from the file.  If there is data then...
    %% split the data into a list of words and add those to the word tree
    process_each_line(IoDevice, Tree) ->
    	case io:get_line(IoDevice, "") of
    		eof -> 
    			file:close(IoDevice),
    			Tree;
    		{error, Reason} ->
    			file:close(IoDevice),
    			throw(Reason);
    		Data ->
    			NewTree = build_word_tree(words(Data), Tree),
    			process_each_line(IoDevice, NewTree)
    	end.
    
    %% walks the gb_tree and prints each word/count pair
    print_tree(Iter) ->
    	case gb_trees:next(Iter) of
    		none -> ok;
    		{Key, Val, NewIter} ->
    			io:format("~s: ~w~n", [Key,Val]),
    			print_tree(NewIter)
    	end.
    
    %% opens the indicated file, processes the contents and prints
    %% out the word/count pairs to stdout
    print_word_counts(Filename) ->
    	case file:open(Filename, read) of
    		{ok, IoDevice} ->
    			Tree = process_each_line(IoDevice, gb_trees:empty()),
    			print_tree(gb_trees:iterator(Tree));
    		{error, Reason} ->
    			io:format("~s~n", [Reason])
    	end.
    



As usual - I'm just getting started with Erlang.  What is the good, bad and ugly with this code?
