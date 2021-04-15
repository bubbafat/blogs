---
author: robert
comments: true
date: 2009-07-04 01:11:43+00:00
layout: post
link: https://roberthorvick.com/2009/07/03/adding-a-custom-protocol-to-firefox-for-easy-erlang-docs/
slug: adding-a-custom-protocol-to-firefox-for-easy-erlang-docs
title: Adding a custom protocol to Firefox for easy Erlang docs
wordpress_id: 50
categories:
- Erlang
tags:
- Erlang
---

To make getting to the Erlang docs a bit quicker I added a custom protocol handler to firefox so when I go to:

[erlang://lists](erlang://lists)

I get redirected to

[http://www.erlang.org/doc/man/lists.html](http://www.erlang.org/doc/man/lists.html)

To do this I first created a little shell script that would take the address and spawn firefox with the appropriate url.  (insert caveat about this being the first bash script I've written in 10 years here ...).

    
    #!/bin/bash
    ERLANG_TOPIC=`echo $1 | sed 's/erlang:\/\///'`
    exec firefox http://www.erlang.org/doc/man/$ERLANG_TOPIC.html


Next I marked it as executable:

    
    chmod +x erlang.protocol


Finally I followed [these directions](http://kb.mozillazine.org/Register_protocol) and added the following config settings in firefox:

![Adding custom protocol to firefox](http://www.roberthorvick.com/wp-content/uploads/2009/07/aboutconfig.png)

Since the Erlang/OTP doc pages match their module names this works pretty well.

For queries where I'm not sure what I want I added a Erlang specific google search to my [Erlang Resources](http://www.roberthorvick.com/erlang-resources/) page.
