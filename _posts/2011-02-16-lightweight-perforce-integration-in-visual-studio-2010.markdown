---
author: admin
comments: true
date: 2011-02-16 18:47:40+00:00
layout: post
link: https://roberthorvick.com/2011/02/16/lightweight-perforce-integration-in-visual-studio-2010/
slug: lightweight-perforce-integration-in-visual-studio-2010
title: Lightweight Perforce integration in Visual Studio 2010
wordpress_id: 114
categories:
- Programming
tags:
- Perforce
- VS2010
---

This is something I’ve been doing to add very light-weight Perforce integration in Visual Studio 2010 without having to use source control bindings in the projects or solutions.

 

Basically I wanted a way to reduce context switches caused by needing to go to the command line to check out or add files in Perforce.

 

I ended up creating a few external commands (Tools -> External Tools) for perforce commands “Add” and “Edit” …

 

[![clip_image002](http://www.roberthorvick.com/wp-content/uploads/2011/02/clip_image002_thumb.jpg)](http://www.roberthorvick.com/wp-content/uploads/2011/02/clip_image002.jpg)

 

Then I created a new toolbar (Tools -> Customize)

 

[![clip_image004](http://www.roberthorvick.com/wp-content/uploads/2011/02/clip_image004_thumb.jpg)](http://www.roberthorvick.com/wp-content/uploads/2011/02/clip_image004.jpg)

 

And then from the toolbar in the IDE shell (which is empty so may be hard to find if you have many toolbars open) I clicked on the little down arrow and choose “Add or Remove Buttons” -> Customize.

 

From there I selected the “My Tools” toolbar I created:

 

[![clip_image006](http://www.roberthorvick.com/wp-content/uploads/2011/02/clip_image006_thumb.jpg)](http://www.roberthorvick.com/wp-content/uploads/2011/02/clip_image006.jpg)

 

And clicked “Add Command”

 

From there I picked “Tools” from Categories and “External Command 1” (this is the first item in the External Tools list – so “Edit” in this case)

 

[![clip_image008](http://www.roberthorvick.com/wp-content/uploads/2011/02/clip_image008_thumb.jpg)](http://www.roberthorvick.com/wp-content/uploads/2011/02/clip_image008.jpg)

 

And then back at the Customize window I clicked “Modify Selection” and renamed “External Command 1” to “Edit” to match the command.

 

Now when I have a file open in VS I can just click the “Edit” button and the file is checked out for edit and the output of the command line goes to the Output window (notice I had that option selected above when defining the External Tool).

 

[![image](http://www.roberthorvick.com/wp-content/uploads/2011/02/image_thumb.png)](http://www.roberthorvick.com/wp-content/uploads/2011/02/image.png)

 

It saves me from having to bounce back and forth between VS and the command line when I need to add/edit a file – this keeps my fingers on the keyboard and off the mouse.
