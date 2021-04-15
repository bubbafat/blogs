---
author: admin
comments: false
date: 2013-06-30 00:39:17+00:00
layout: post
link: https://roberthorvick.com/2013/06/29/getting-hoodie-0-2-working-on-windows-7-x64/
slug: getting-hoodie-0-2-working-on-windows-7-x64
title: Getting Hoodie 0.2 working on Windows 7 (x64)
wordpress_id: 197
categories:
- Hoodie
---

![](http://i.imgur.com/ErHLRj7.png)

[Hood.ie](http://hood.ie) looks rad. I'm want to try it out. Problem is that I need to self-host for now and I'm on a Windows machine. Sure, I could go stand up one of my AWS instances or just piggy back on one in production but I'm kind of a masochist.

So here's how I installed Hoodie on Windows 7.



	
  1. Read [this](http://hood.ie)

	
  2. Now go read [this part](http://hood.ie/#installation) again

	
  3. Install Node and CouchDB as the previous step said.

	
  4. Follow the rest of the setup steps on that page

	
  5. Complain that it did not work (here's where I save you some time...)

	
  6. Install [Cygwin coretools](http://cygwin.com/cgi-bin2/package-cat.cgi?file=coreutils%2Fcoreutils-8.15-1) (you need to have a POSIX mkdir.exe)

	
  7. Set the COUCH_BIN environment variable as COUCH_BIN=C:\Program Files (x86)\Apache Software Foundation\CouchDB\bin\erl.exe

	
  8. Set the COUCH_DEFAULT_INI environment variable as COUCH_DEFAULT_INI=C:\Program Files (x86)\Apache Software Foundation\CouchDB\etc\couchdb\default.ini


So what was that all about?


### mkdir is missing




#### Hoodie issue [85](https://github.com/hoodiehq/hoodie-app/issues/85)



    
    Initializing...
    Error: spawn ENOENT
        at errnoException (child_process.js:980:11)
        at Process.ChildProcess._handle.onexit (child_process.js:771:34)
    npm ERR! weird error 1
    npm ERR! not ok code 0


Install Cygwin like I said earlier. Validate that it is in your path by checking with where.

    
    C:\hoodie\testapp>where mkdir.exe
    c:\cygwin\bin\mkdir.exe




### CouchDB cannot be found




#### Hoodie issue [86](https://github.com/hoodiehq/hoodie-app/issues/86)



    
    Initializing...
    
    ERR! Error: No CouchDB binary found
    ERR!     at exports.startMultiCouch (C:\Hoodie\todo\node_modules\hoodie-app\lib\couch.js:39:25)
    ERR!     at C:\Hoodie\todo\node_modules\hoodie-app\node_modules\async\lib\async.js:909:20
    ERR!     at iterate (C:\Hoodie\todo\node_modules\hoodie-app\node_modules\async\lib\async.js:128:13)
    ERR!     at async.eachSeries (C:\Hoodie\todo\node_modules\hoodie-app\node_modules\async\lib\async.js:144:9)
    ERR!     at go (C:\Hoodie\todo\node_modules\hoodie-app\node_modules\async\lib\async.js:908:20)
    ERR!     at C:\Hoodie\todo\node_modules\hoodie-app\node_modules\async\lib\async.js:909:20
    ERR!     at iterate (C:\Hoodie\todo\node_modules\hoodie-app\node_modules\async\lib\async.js:128:13)
    ERR!     at C:\Hoodie\todo\node_modules\hoodie-app\node_modules\async\lib\async.js:139:25
    ERR!     at exports.exitIfSudo (C:\Hoodie\todo\node_modules\hoodie-app\lib\app.js:135:16)
    ERR!     at C:\Hoodie\todo\node_modules\hoodie-app\node_modules\async\lib\async.js:909:20
    npm info todo@1.0.0 Failed to exec start script
    npm ERR! weird error 1
    npm verb exit [ 1, true ]
    npm ERR! not ok code 0


Read the bug for more details, but the bottom line is that you need to set the COUCH_BIN and COUCH_DEFAULT_INI environment variables to the appropriate values. The values are files that were installed when you installed CouchDB so you should be able to easily figure out what the path should be on your machine. Probably the same as mine.

set COUCH_BIN=C:\Program Files (x86)\Apache Software Foundation\CouchDB\bin\erl.exe
set COUCH_DEFAULT_INI=C:\Program Files (x86)\Apache Software Foundation\CouchDB\etc\couchdb\default.ini


### And then there was light!



    
    C:\Hoodie\todo>hoodie start
    npm info it worked if it ends with ok
    npm verb cli [ 'C:\\Program Files (x86)\\nodejs\\\\node.exe',
    npm verb cli   'C:\\Program Files (x86)\\nodejs\\node_modules\\npm\\bin\\npm-cli
    .js',
    npm verb cli   'start' ]
    npm info using npm@1.2.32
    npm info using node@v0.10.12
    npm verb node symlink C:\Program Files (x86)\nodejs\\node.exe
    npm verb read json C:\Hoodie\todo\package.json
    npm WARN package.json todo@1.0.0 No repository field.
    npm verb run-script [ 'prestart', 'start', 'poststart' ]
    npm info prestart todo@1.0.0
    npm info start todo@1.0.0
    npm verb unsafe-perm in lifecycle true
    
    > todo@1.0.0 start C:\Hoodie\todo
    > node node_modules/hoodie-app/bin/start
    
    .d$b.  .d$b.  .d$$$$$$b.    .d$$$$$$b.  .d$$$$$$b.  .d$b..d$$$$$$$$b.
    $$$$$..$$$$$.$$$$$$$$$$$b .$$$$$$$$$$$b $$$$$$$$$$b $$$$$$$$$$$$$$$P'
    $$$$$$$$$$$$d$$$$$$$$$$$$bd$$$$$$$$$$$$b$$$$$$$$$$$b$$$$$$$$$$$$$$$b.
    $$$$$$$$$$$$Q$$$$$$$$$$$$PQ$$$$$$$$$$$$P$$$$$$$$$$$P$$$$$$$$$$$$$$$P'
    $$$$$'`$$$$$'$$$$$$$$$$$$''$$$$$$$$$$$$'$$$$$$$$$$P $$$$$$$$$$$$$$$b.
    'O$P'  'O$P'  'O$$$$$$P'    'O$$$$$$P'  'O$$$$$$$P  'O$P''O$$$$$$$$P'
    
     Hi!
    
    Initializing...
    CouchDB started: http://127.0.0.1:6003
    Waiting for CouchDB [---*--] SUCCESS
    
    WWW:   http://127.0.0.1:6001
    Admin: http://127.0.0.1:6002
    
    [changes] using changes feed fallback
    Starting: 'users'
    [users] [Setup] reading global config from modules/module/appconfig .
    All workers started.
    [users] [Setup] reading user config from modules/module/users .
    
    [www] GET / 304 4ms
