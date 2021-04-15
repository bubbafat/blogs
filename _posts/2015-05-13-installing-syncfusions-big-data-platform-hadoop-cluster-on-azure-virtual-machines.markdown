---
author: admin
comments: true
date: 2015-05-13 20:38:19+00:00
layout: post
link: https://roberthorvick.com/2015/05/13/installing-syncfusions-big-data-platform-hadoop-cluster-on-azure-virtual-machines/
slug: installing-syncfusions-big-data-platform-hadoop-cluster-on-azure-virtual-machines
title: Installing SyncFusion's Big Data Platform (Hadoop cluster) on Azure Virtual
  Machines
wordpress_id: 447
categories:
- Data
- Programming
---

This week SyncFusion announced their new [Big Data platform](http://syncfusion.com/products/big-data) - basically it is Hadoop in an easy to install, configure and monitor format. You can also [go here](https://www.couchbase.com/resources/why-nosql) for more information on Hadoop, Couchbase. MongoDB and other kinds of NoSQL database.

Sounds great, right? So let's give it a go, installing a 3-node cluster on Azure.


## Step 1: Create the Virtual Machines


I created 3 Azure VM's using these basic settings:



 	
  * dlhadoop1 (10.0.0.4), dlhadoop2 (*.5), dlhadoop3 (*.6)

 	
  * Dual core, 14 GB RAM

 	
  * Virtual Network: hadoop

 	
  * Storage Account: hadoop

 	
  * Resource Group: Hadoop


Having them on the same virtual network is mandatory for them to communicate over the internal IP address. Having them in the same storage account and resource group just makes them easier to manage.

If you haven't used the Azure interface to create VM's before - this one minute video shows how it works.




## Step 2: Install the Hadoop Cluster Manager (and deal with a few bugs)


Let's get a few things out of the way. When you first log into your VM you will want to [disable IE ESC](http://www.rackspace.com/knowledge_center/article/disable-ie-enhanced-security-on-windows-server-2012). Next you will want to install [Chrome](https://www.google.com/chrome/).

This isn't some "IE IS TEH SUX!!!" thing ... the problem is that the [SyncFusion page](http://www.syncfusion.com/downloads/bigdata/confirmation) to download the cluster manager and agent is not compatible with the version of IE your VM will have. Not even in compatibility mode. I mean, they look right and they seem OK ... but when you click on the download links for the EXE or ZIP nothing happens. Ever. IE simply doesn't see it as a link so it never does anything with it.

Chrome works fine.

So now with Chrome installed you can click the "Big Data Cluster (EXE)" link to download the 600MB exe. Start the installation.

This is a simple self-extracting zip that starts the installer when it is finished. The installer is simple and doesn't give you any options so just run through it. When it is done you will be able to start the dashboard - you should do that. But don't get too excited. We're about to hit another bug.

When the "dashboard" (which is really just a splash screen with 2 buttons) is loaded, click the green "Cluster Manager" button.

![IIS Express is not installed on your machine.](http://www.roberthorvick.com/wp-content/uploads/2015/05/iis-error.png)

Whoa ... hold on now. What the hell is this? Let's go back to SyncFusion's Big Data [product page](http://www.syncfusion.com/products/big-data). Anyone notice this claim?

![All you need on each node is a small agent that depends on .NET Framework 4.5 or later. There are absolutely no other dependencies.](http://www.roberthorvick.com/wp-content/uploads/2015/05/no-prereqs.png)

Look, I don't mind needing to install a pre-req (though you could have installed this one during your installation process) ... but don't claim there aren't any when there are.

But wait ... it gets worse. Go ahead and install it now. I'll wait.

Did it fix it? No. It didn't. Here's my attempt ...



I spent quite a while digging into this. I ended up debugging and tracing all the API calls to figure out what file and registry probing that it might be doing (in case something changed about IIS Express or Server 2012 that might cause probing to fail). I assumed that this wasn't hit either because this combination of OS and tools is not supported or everyone always tested this with IIS pre-installed. So I fell back to ol' reliable.

Uninstall the cluster manager and then re-install it.

That fixed it.

Having IIS Express installed ahead of time works. Installing it after the fact does not.

I did not try installing the Windows web role ahead of time - but that might work too. I'm following the SyncFusion "It just works" path.


## Step 3: Configure the Hadoop agent nodes (and deal with a DNS issue)


With the cluster manager now working you can open the management dashboard (opens in your default browser) and click the "Create" button to create a new cluster. Setup the cluster to communicate with the other virtual machines (which are all on the same virtual network per the VM configuration section) ...

[
![create-cluster](http://www.roberthorvick.com/wp-content/uploads/2015/05/create-cluster.png)
](http://www.roberthorvick.com/wp-content/uploads/2015/05/create-cluster.png)

Enter the IP addresses (10.0.0.4, 10.0.0.5, 10.0.0.6) and click "Next"

This takes you to a page that, after a minute or two, tells you that the agents are not installed. This is correct. We have not installed them.

![agent-not-installed](http://www.roberthorvick.com/wp-content/uploads/2015/05/agent-not-installed.png)

So now go off and download the agent exe (from where you got the cluster manager) and install it on this VM. There are no options and the installation takes about 30 seconds. Once it is done click the refresh button on the line for the current VM (10.0.0.4 in my case).

Now the client agent is working.

[
![install-agent](http://www.roberthorvick.com/wp-content/uploads/2015/05/install-agent.png)
](http://www.roberthorvick.com/wp-content/uploads/2015/05/install-agent.png)

Now go repeat this step on the other two VM's. Download and install the agent then come back and click "refresh". But don't get all excited yet. It still not going to work. You'll see this:

[
![DNS and reverse DNS are not working](http://www.roberthorvick.com/wp-content/uploads/2015/05/dns-bad.png)
](http://www.roberthorvick.com/wp-content/uploads/2015/05/dns-bad.png)

So what's happening here is that the local VM is able to connect to the remote VM but DNS and reverse DNS are not working because Azure doesn't give you that by default.

Here's where I got lazy. I'm just trying to use Hadoop not solve the world's problems so on the current VM I opened the HOSTS file (%windir%\system32\drivers\etc\hosts) and added these lines:

`
dlhadoop2      10.0.0.5
dlhadoop3      10.0.0.6
`

Then (for my sanity) I ran these commands (from and admin command prompt):

`
ipconfig /flushdns
arp -d *
`

Now try refreshing again.

[
![success](http://www.roberthorvick.com/wp-content/uploads/2015/05/success.png)
](http://www.roberthorvick.com/wp-content/uploads/2015/05/success.png)
Awesome! Click "Next" and wait for the package transfer (this takes several minutes)

[![transfering-packages](http://www.roberthorvick.com/wp-content/uploads/2015/05/transfering-packages.png)](http://www.roberthorvick.com/wp-content/uploads/2015/05/transfering-packages.png)

And then ensuring high availability goes for a while...

[![ensuring-high-availability](http://www.roberthorvick.com/wp-content/uploads/2015/05/ensuring-high-availability.png)](http://www.roberthorvick.com/wp-content/uploads/2015/05/ensuring-high-availability.png)

And finally we're done.

[![all-working](http://www.roberthorvick.com/wp-content/uploads/2015/05/all-working.png)](http://www.roberthorvick.com/wp-content/uploads/2015/05/all-working.png)


## What's Next?


Next we'll actually try and use it, of course!
