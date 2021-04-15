---
author: admin
comments: true
date: 2016-11-22 19:52:15+00:00
layout: post
link: https://roberthorvick.com/2016/11/22/sbt-exclude-slf4j-transitive-dependency/
slug: sbt-exclude-slf4j-transitive-dependency
title: Excluding Spark SLF4J transitive dependencies in sbt
wordpress_id: 518
categories:
- Scala
- Spark
---

If you've written any Spark code using Scala and SBT there s a good chance you've come across this warning about SLF4J:

<!-- more -->

    
    SLF4J: Class path contains multiple SLF4J bindings.
    SLF4J: Found binding in [jar:file:/C:/Users/RHorvick/.ivy2/cache/org.slf4j/slf4j-simple/jars/slf4j-simple-1.7.13.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: Found binding in [jar:file:/C:/Users/RHorvick/.ivy2/cache/org.slf4j/slf4j-log4j12/jars/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    
    SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
    SLF4J: Actual binding is of type [org.slf4j.impl.SimpleLoggerFactory]
    


And even if you have a conf/log4j.properties file, you might be getting INFO level logging when all you want is WARN or ERROR. So when your tests run your console is riddled with this nonsense:

    
    [Executor task launch worker-0] INFO org.apache.spark.executor.Executor - Running task 30.0 in stage 3.0 (TID 35)
    [task-result-getter-0] INFO org.apache.spark.scheduler.TaskSetManager - Finished task 26.0 in stage 3.0 (TID 31) in 39 ms on localhost (27/199)
    [Executor task launch worker-0] INFO org.apache.spark.storage.ShuffleBlockFetcherIterator - Getting 3 non-empty blocks out of 4 blocks
    [Executor task launch worker-0] INFO org.apache.spark.storage.ShuffleBlockFetcherIterator - Started 0 remote fetches in 1 ms
    [Executor task launch worker-2] INFO org.apache.spark.executor.Executor - Finished task 27.0 in stage 3.0 (TID 32). 1652 bytes result sent to driver
    [dispatcher-event-loop-2] INFO org.apache.spark.scheduler.TaskSetManager - Starting task 31.0 in stage 3.0 (TID 36, localhost, partition 32,NODE_LOCAL, 1999 bytes)
    [task-result-getter-3] INFO org.apache.spark.scheduler.TaskSetManager - Finished task 27.0 in stage 3.0 (TID 32) in 37 ms on localhost (28/199)
    [Executor task launch worker-2] INFO org.apache.spark.executor.Executor - Running task 31.0 in stage 3.0 (TID 36)
    [Executor task launch worker-3] INFO org.apache.spark.executor.Executor - Finished task 28.0 in stage 3.0 (TID 33). 1652 bytes result sent to driver
    [Executor task launch worker-2] INFO org.apache.spark.storage.ShuffleBlockFetcherIterator - Getting 3 non-empty blocks out of 4 blocks
    [Executor task launch worker-2] INFO org.apache.spark.storage.ShuffleBlockFetcherIterator - Started 0 remote fetches in 2 ms
    [dispatcher-event-loop-0] INFO org.apache.spark.scheduler.TaskSetManager - Starting task 32.0 in stage 3.0 (TID 37, localhost, partition 33,NODE_LOCAL, 1999 bytes)
    [Executor task launch worker-3] INFO org.apache.spark.executor.Executor - Running task 32.0 in stage 3.0 (TID 37)
    [Executor task launch worker-3] INFO org.apache.spark.storage.ShuffleBlockFetcherIterator - Getting 3 non-empty blocks out of 4 blocks
    


Thousands of lines of it for a simple test.

The common solution is to add excludes to the Spark dependencies in the sbt file - like this:

    
    libraryDependencies ++= Seq(
      "com.holdenkarau" %% "spark-testing-base" % sparkTestingVersion % "test" exclude("org.slf4j", "*")
    )
    


And that works for a while. But then you bring in another dependency that has a transitive dependency ... so you track that down and do it again. And again. And pretty soon you've got about 20% of your dependencies excluding slf4j - and the reality is you don't want any of them to bring it in!

That sucks - so let's do it the easy way.

    
    libraryDependencies ++= Seq(
      "org.apache.spark" %% "spark-core" % sparkVersion % "provided" ,
      "org.apache.spark" %% "spark-sql" % sparkVersion % "provided" ,
      "com.holdenkarau" %% "spark-testing-base" % sparkTestingVersion % "test" ,
    ).map(_.exclude("org.slf4j", "*"))
    
    libraryDependencies ++= Seq(
      "org.slf4j" % "slf4j-api" % slf4jVersion % "provided",
      "org.slf4j" % "slf4j-nop" % slf4jVersion % "test"
    )
    


See that? We separate the slf4j dependencies away from the rest and then use map to apply the SLF4J exclusion to all of the dependencies.

Next we add the SLF4J dependencies back - but this time we indicate that in test builds we want the nop version used and in non-test builds we will use the provided version.

And now our unit tests which used to look like this:

![Before the NOP logger and SLF4J logging change](http://www.roberthorvick.com/wp-content/uploads/2016/11/sbt-before.png)

Now look like this:

![After the NOP logger and SLF4J logging change](http://www.roberthorvick.com/wp-content/uploads/2016/11/sbt-after.png)
