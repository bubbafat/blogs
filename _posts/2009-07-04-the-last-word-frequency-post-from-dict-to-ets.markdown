---
author: robert
comments: true
date: 2009-07-04 03:15:56+00:00
layout: post
link: https://roberthorvick.com/2009/07/03/the-last-word-frequency-post-from-dict-to-ets/
slug: the-last-word-frequency-post-from-dict-to-ets
title: The last word frequency post - from dict to ets
wordpress_id: 57
categories:
- Erlang
tags:
- Erlang
- ets
---

One last iteration through my learning exercise of building a word frequency list.  In this last post I'm moving away from a dict and to an ets table.  I was pleasantly surprised how easy the conversion was.  For example printing the output was just converting from dict:fold to ets:foldl.  The one parity fail was that dict:update can take an initial value when the key is missing but ets:update_counter (nor any other ets function) has this benefit.  This required that I write a little wrapper function to call from the list:foldl (instead of having a multi-line inlined fun).

No point in getting too deep into this - here's the code:


    
    
    -module(wordets).
    
    -export([print_word_counts/1]).
    
    words(String) ->
      {match, Captures} = re:run(String, "\\b\\w+\\b", [global,{capture,first,list}]),
      lists:append(Captures).
    
    %% reads the next line from the file.  If there is data then...
    %% split the data into a list of words and add to the word table
    process_each_line(IoDevice, Table) ->
      case io:get_line(IoDevice, "") of
        eof -> 
          file:close(IoDevice),
          Table;
        {error, Reason} ->
          file:close(IoDevice),
          throw(Reason);
        Data ->
          NewTable = lists:foldl(
            fun(W, T) -> update_word_count(W, T) end, Table, words(Data)),
          process_each_line(IoDevice, NewTable)
      end.
      
    update_word_count(Word, Table) ->
      case ets:lookup(Table, Word) of
        [{Word, _}] ->
          ets:update_counter(Table, Word, 1); 
        [] ->
          ets:insert(Table, {Word, 1})
      end,
      Table.
    
    print_words(Words) ->
      ets:foldl(fun({W,C}, AccIn) -> 
        io:format("~s: ~w~n", [W, C]), AccIn end, void, Words).
    
    %% opens the indicated file, processes the contents and prints
    %% out the word/count pairs to stdout
    print_word_counts(Filename) ->
      {ok, IoDevice} = file:open(Filename, read),
      Words = process_each_line(IoDevice, ets:new(words, [])),
      print_words(Words).
    



The ets implementation feels a bit forced (which it was - the point was to learn another module).  I don't think I'd have gone this way in practice unless I wanted to persist the frequency data to a file or if the word data were more complex (for example if I were storing information about where in the file the word was, word neighbors, etc).

Enough of this sample.  On to something more substantial.
