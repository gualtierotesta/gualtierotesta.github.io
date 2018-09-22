---
author: gualtierotesta
comments: true
date: 2015-11-29 19:00:55+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2015/11/29/the-5-java-logging-rules/
slug: the-5-java-logging-rules
title: The 5 Java logging rules
wordpress_id: 464
categories:
- Java
tags:
- apache commons logging
- coding
- execution environments
- integration
- java util logging
- log levels
- loggin engine
- logging
- produtction
- slf4j
- test
---

_Logging_ is a critical factor that should be always kept into account during the software development.

When something bad happens in production, the log files are usually the starting point of our fault analysis. And, often, they are the _only_ information in our hands to understand what is happened and which is the root cause of the problem.

It is so very important to have the required information logged properly.

The following five logging rules are a way to check and, possibly, improve how we handle the logging in our code.

Please note that we will not discuss how to configure a logging engine nor we will compare them to each other.



### Rule 1. Logging is for readers



The logging messages should be meaningful to who will **read the log files**, not only to who wrote the (logging) code.

It seem a very obvious rule but it is often violated.

For example, let's consider a log message like the following

`ERROR: Save failure - SQLException .....`

Saving what? This message **could** mean something for the developer but it is completely useless for the poor guy which is looking at the production problem.

Much better message is

`ERROR: Save failure- Entity=Person, Data=[id=123 surname="Mario"] - SQLException....`

which explains what you wanted to save (here a Person, a JPA entity) and the **relevant** contents of the Person instance. Please note the word _relevant_, instead of the world _all_: we should not clutter log files with useless info like the complete print of all entity fields. Entity name and its logical keys are usually enough to identify a record in a table.



### Rule 2. Match the logging levels with the execution environment



All logging facades and engines available in the Java ecosystem have the concept of logging level (ERROR, INFO...), with the possibility to filter out messages with too low level.

For example, [Java util logging](https://docs.oracle.com/javase/8/docs/api/java/util/logging/Level.html) uses the following levels: SEVERE, WARN, INFO, FINE, FINER, FINEST (+ CONFIG and OFF).

On the contrary, the two most popular logging facade, [Apache Commons Logging](https://commons.apache.org/proper/commons-logging/javadocs/api-release/index.html) and [SLFJ](http://www.slf4j.org/api/org/apache/log4j/Level.html), prefer the following levels: FATAL, ERROR, WARN, INFO, DEBUG, TRACE.

Logging level **filtering** should depends on which stage of the development is your code: logging level in the production should not be the same as in test/integrations environments.

Moreover, logging level should also depends on the code _owner_. In general our own application code should have a more detailed logging compared to any third party library we are using. There is no big meaning to see, for example, Apache Commons debug messages in our log files.

I usually configure the logging as following:





  * _Production_: INFO level for my code and WARN for third party libraries.


  * _Test / Integration_: DEBUG level for my code and WARN (or INFO if needed) for third party libraries.


  * _Development_: whatever make sense



Note: I personally discourage the use of the TRACE/FINEST level (and I'm not alone, see for example [here](http://slf4j.org/faq.html#trace)). I don't see big difference between DEBUG and TRACE and it is usually difficult for the young team members to decide which one, DEBUG or TRACE, to use. Following the [Kiss principle](https://en.wikipedia.org/wiki/KISS_principle), I suggest to use ERROR, WARN, INFO and DEBUG levels only.



### Rule 3. Remove coding help logging before the commit.



While coding, we usually add logging messages, using the `logger` or the `System.out`, in our code for a better understanding of what it is happening in our application during execution /debugging sessions.

Something like:

[code lang=java]
    void aMethod(String aParam) {
        LOGGER.debug(“Enter in aMethod”);
        if (“no”.equals(aParam)) {
           LOGGER.debug(“User says no”);
          ….
```

The main purpose of these messages is to trace application behaviour by showing which method is invoked and by dumping internal variables and method parameters values. Quite popular among non TDD devotes.

Unfortunately these messages do not have usually big meaning once the code has been released (to test and then production).

So this rule simply says: once you have finished to develop, remove all temporary and unnecessary logging messages just before committing the code to the SCM system (git, svn..) in use.

This rule does not require to remove all DEBUG messages but only the ones that do not have meaning once the application is completed and released; in other words when we are reasonably sure that the application is working properly.



### Rule 4: Check log level before logging DEBUG messages



According to Rule 2, in production logs we will show ERROR, WARN, INFO messages only but in our code we can have many DEBUG messages that should not affect production execution.

Every time you want to log a DEBUG message (all the ones which remain after rule 3), add in front a check if DEBUG logging is enabled:

[code lang=java]
    if ( LOGGER.isDebugEnabled() ) {
        LOGGER.debug (…….)
    }
```

This will prevent you code to build the log messages and call the logger. It is for efficiency in the program execution at production.



### Rule 5: Know your logger



How we use the logger methods can have a significant cost:





  * To build the message string


  * to collect the data to be included in the message string



We should review the _javadoc_ of the selected logging facade/engine and understand the most efficient way to use its logger.

For example, we could create a message like this:

[code lang=java]
    LOGGER.info(“Person name is “ + person.getName());
```

which creates unnecessary strings instances.

Using SLF4J, the correct use is :

[code lang=java]
    LOGGER.info(“Person name is {}“, person.getName());
```

where format string is constant and the final message is build only if logging is enabled. See [here](http://slf4j.org/faq.html#logging_performance) for more details.
