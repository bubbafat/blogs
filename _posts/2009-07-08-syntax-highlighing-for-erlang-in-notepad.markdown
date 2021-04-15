---
author: robert
comments: true
date: 2009-07-08 18:02:42+00:00
layout: post
link: https://roberthorvick.com/2009/07/08/syntax-highlighing-for-erlang-in-notepad/
slug: syntax-highlighing-for-erlang-in-notepad
title: Syntax Highlighing for Erlang in NotePad++
wordpress_id: 64
categories:
- Erlang
tags:
- Erlang
- Windows
---

_Update: The definition has been updated to include support for atoms, variables and function names as well as additional file extensions.  Screen shot and downloadable content have been updated._

Thus far I've done all of my Erlang development on Fedora using vim or KWrite (which does a decent job in Ruby mode).

But today I found myself on a windows box and wanted a basic syntax highlighting editor for Erlang that was free and worked on Windows.  Oh - and not Eclipse+Erlide.  I wanted something small and fast.

I grabbed the "[free as in beer](http://en.wikipedia.org/wiki/Free_as_in_beer)" and "[free as in speech](http://en.wikipedia.org/wiki/Free_as_in_beer#.22Free_as_in_beer.22_vs_.22Free_as_in_speech.22)" editor [NotePad++](http://notepad-plus.sourceforge.net) and created a simple syntax file that is a bit hokey but will serve my needs fine.

Here's a screen shot ...
![Windows Erlang Syntax Highlighting Editor](http://www.roberthorvick.com/wp-content/uploads/2009/07/screenshot1.jpg)

NotePad++ has pretty weak syntax highlighting but was sufficent to do most of what I wanted.  Some regex based rules would make this a more robust.

Highlighted entities include



	
  * Erlang reserved words (and named operators)

	
  * Variables

	
  * Atoms

	
  * function names (same coloring as atoms)

	
  * Operators

	
  * Comments

	
  * Kernal, stdlib, mnesia and odbc modules.

	
  * Support for *.erl, *.hrl and *.htp extentions


I've probably missed several things.

Looks a lot better than nothing and it took all of 10 15 minutes.

If you are using NotePad++ here is the file:

[http://www.roberthorvick.com/wp-content/uploads/2009/07/erlangSyntaxDefinition.zip](http://www.roberthorvick.com/wp-content/uploads/2009/07/erlangSyntaxDefinition.zip)

And here are the instructions on how to install it:

[http://sourceforge.net/apps/mediawiki/notepad-plus/index.php?title=Syntax_Highlighting_Sharing](http://sourceforge.net/apps/mediawiki/notepad-plus/index.php?title=Syntax_Highlighting_Sharing)

And here's the instructions on modifying or creating your own:

[http://sourceforge.net/apps/mediawiki/notepad-plus/index.php?title=User_Defined_Languages](http://sourceforge.net/apps/mediawiki/notepad-plus/index.php?title=User_Defined_Languages)
