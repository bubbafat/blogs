---
author: robert
comments: true
date: 2012-08-26 00:31:24+00:00
layout: post
link: https://roberthorvick.com/2012/08/25/azure-worker-role-connecting-to-sql-error-providerincompatibleexception/
slug: azure-worker-role-connecting-to-sql-error-providerincompatibleexception
title: Azure Worker Role connecting to SQL error ProviderIncompatibleException
wordpress_id: 147
---

I ran into an issue where connecting my Azure worker role to my Azure SQL database was failing with the exception type ProviderIncompatibleException and the message “An error occurred while getting provider information from the database. This can be caused by Entity Framework using an incorrect connection string.”

 

The inner exception was of type ProviderIncompatibleException with the message “The provider did not return a ProviderManifestToken string.”

 

I discovered two things.

 

  
  1. [Slow Cheetah](http://visualstudiogallery.msdn.microsoft.com/69023d00-a4f9-4a34-a6cd-7e854ba318b5) was not transforming my Worker Role app.config file so the wrong connection string was in the app.config file
   
  2. I only had the DefaultConnection string – I needed to two others (well – probably only one but I’m using automatic migrations right now)
 

I had:

 

<connectionStrings>       
<add name="DefaultConnection" connectionString="…" providerName="System.Data.SqlClient" />        
</connectionStrings>

 

I needed

 

<connectionStrings>       
<add name="DefaultConnection" connectionString="…" providerName="System.Data.SqlClient" />        
<add name="Fully.Qualified.ContextDb.Class" connectionString="…" providerName="System.Data.SqlClient" />        
<add name="Fully.Qualified.ContextDb.Class_DatabasePublish" connectionString="…" providerName="System.Data.SqlClient" />        
</connectionStrings>      


 

 

With that change to my app.config the next publish worked out just grand.
