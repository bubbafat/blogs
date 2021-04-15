---
author: robert
comments: true
date: 2009-07-02 21:28:10+00:00
layout: post
link: https://roberthorvick.com/2009/07/02/word-frequency-redux-erlang-list-comprehension-regex-and-list-folding/
slug: word-frequency-redux-erlang-list-comprehension-regex-and-list-folding
title: Word frequency redux - Erlang list comprehension, regex and list folding
wordpress_id: 41
categories:
- Erlang
tags:
- Erlang
---

[Sean Cribbs](http://seancribbs.com/) was nice enough to [point out](http://www.roberthorvick.com/2009/07/02/finding-word-frequencies-using-erlang/#comments) a pair of changes I could make to my [word frequency counter](http://www.roberthorvick.com/2009/07/02/finding-word-frequencies-using-erlang/) from last time.

Based on his feedback I made three changes.  First - the regular expression code has changed from this:


    
    
    matches(H,{match,M}) -> matches(H,M,[]).
    matches(_,[],Acc) -> Acc;
    matches(H,[{I,L}|T],Acc) ->
        matches(H,T,[lists:sublist(H,I,L)|Acc]).
     
    words(String) -> matches(String,regexp:matches(String, "[A-Za-z0-1]+")).
    



to this:


    
    
    words(String) ->
      {match, Captures} = re:run(String, "\\b\\w+\\b", [global,{capture,first,list}]),
      [hd(C) || C<-Captures].
    



That last line took me a bit to grok.  It's a [list comprehension](http://wiki.trapexit.org/List_Comprehension) (if you are reading Joe Armstrong's [thesis](http://www.sics.se/~joe/thesis/armstrong_thesis_2003.pdf) it is section 3.3.13.  In Erlang Programming it is chapter 9.3).  Basically it's saying "for each list in the list of matches take the head of the list" - a-gigga-wah?

Ok.  Let's go to erl.
`7> re:run("foo foo bar", "\\b\\w+\\b", [global,{capture,first,list}]).
{match,[["foo"],["foo"],["bar"]]}`
Observe that re:run returns a nested list (i.e. a list of lists) - and each list has exactly one element (the string [which is itself a list but I'll cal them strings]).  What we want to do is take that list-of-lists-of-strings and turn it into a list-of-strings.

That's what "[hd(C) || C<-Captures]." does  - it pulls every capture (a word wrapped in a list) from the match list and runs it through erlang:hd which pulls the word from the list - then it gets added to the resulting list.  So we end up with a list strings.

It's un-nesting the list.

Next Sean suggested _"Then I’d probably use some kind of key-value structure, like a proplist or dict, to count the words using a lists:foldX function."_

so I fired up "erl -man lists" to learn what foldX meant (actually "foldl" "foldr" depending on whether you want to fold from the left or right.

In a nut shell folding is iterates over a list calling a fun that takes the current value and an accumulator and which returns the new accumulator.  An example from the man page is:

`
> lists:foldl(fun(X, Sum) -> X + Sum end, 0, [1,2,3,4,5]).
15
`

I spent some time thinking and after some trial and error came up with this:


    
    
    lists:foldl(fun(W, Dict) -> 
        dict:update(W, fun(C) -> C + 1 end, 1, Dict) end, dict:new(), 
        ["foo", "foo", "bar"]).  %% sample input
    



In a nutshell - for every word in the list update the dictionary by calling the fun which increments the count value, setting the initial count to 1 if the value does not already exist in the dictionary (and starting with an empty dictionary).

After these three changes the new program is about half the size of the previous and really only has a few interesting lines surround by nearly error and flow control.

**Thanks Sean!**

The new code ...


    
    
    -module(wordlist).
    
    -export([print_word_counts/1]).
    
    words(String) ->
      {match, Captures} = re:run(String, "\\b\\w+\\b", [global,{capture,first,list}]),
      [hd(C) || C<-Captures].
    
    %% reads the next line from the file.  If there is data then...
    %% split the data into a list of words and add those to the word dict
    process_each_line(IoDevice, Dict) ->
        case io:get_line(IoDevice, "") of
            eof -> 
                file:close(IoDevice),
                Dict;
            {error, Reason} ->
                file:close(IoDevice),
                throw(Reason);
            Data ->
                NewDict = lists:foldl(
                            fun(W, D) -> dict:update(W, fun(C) -> C + 1 end, 1, D) end, 
                            dict:new(), 
                            words(Data)),
                process_each_line(IoDevice, NewDict)
        end.
    
    print_dict(Dict) ->
        dict:fold(fun(Word, Count, AccIn) -> 
            io:format("~s: ~w~n", [Word, Count]), AccIn end, void, Dict).
    
    %% opens the indicated file, processes the contents and prints
    %% out the word/count pairs to stdout
    print_word_counts(Filename) ->
        case file:open(Filename, read) of
            {ok, IoDevice} ->
                Dict = process_each_line(IoDevice, dict:new()),
                print_dict(Dict);
        end.
    
