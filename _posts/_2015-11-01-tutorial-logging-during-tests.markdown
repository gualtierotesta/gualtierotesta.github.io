---
author: gualtierotesta
comments: true
date: 2015-11-01 19:00:12+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2015/11/01/tutorial-logging-during-tests/
slug: tutorial-logging-during-tests
title: 'Tutorial: logging during tests'
wordpress_id: 453
categories:
- Java
- Maven
- test
- tutorial
tags:
- coding
- java
- junit
- logging
- maven
- plugin
- slf4j
- unit test
- unit testing
---

Logging is a popular solution to show what the software is doing while it is running.

But what happens to the logging when we are unit testing our application with jUnit/TestNG ?

During the automated tests execution, we are usually **not** interested to see the logging messages because our main interest is the test results.

It would be nice to be able to disable logging messages during _standard_ automated tests.

On the contrary, there are some cases where logging messages can be useful at test time. A typical example is when we are coding missing tests for some legacy code we do not want to touch before having a good test coverage in place. In this case logging messages _on the console_ can help us to understand the code and how it works.

So we can identify three use cases:





  1. **Running mode**, when the application is executed: the logging is enabled and configured as required by the application


  2. **Test execution mode**, when the automated tests are executed all together: the logging messages should be disabled



  3. **Test creation mode**, when we are creating new tests: the logging messages are useful but it would be nice to have them in the console






Let see an example based on [Maven](https://maven.apache.org/) and [SLF4J](http://www.slf4j.org/), the popular logging facade.

Complete project can be found [here](https://github.com/gualtierotesta/logging_during_test).

Typical SLF4J configuration in the project `pom.xml` is the following:

[code lang=xml]
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
            <scope>runtime</scope>
        </dependency>
```

the `slf4-api` dependency is the main SLF4J library while the second one, `slf4j-log4j12`, is referencing one of the possible logging engines ([LOG4J](https://logging.apache.org/log4j/1.2/)) that can work _below_ the SLF4J.

This is the **running mode** configuration. In this example, the project resources will contain a LOG4J properties file which dictates what, how and where LOG4J should log.

The same situation happens when we are using a different logging engine like java.util.logging (JDK) and Logback. See [Slf4J manual](http://www.slf4j.org/manual.html) for more details.

In the **Test execution mode** we do not want logging so we can simply add the following **scope test** dependency

[code lang=xml]
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>${slf4j.version}</version>
            <scope>test</scope>
        </dependency>
```

The [NOP Logger (slf4j-nop)](http://www.slf4j.org/api/org/slf4j/helpers/NOPLogger.html) simply discards all logging.

**Important**: the dependencies order in the `pom.xml` is significant. Put the `slf4j-nop` dependency just after the `slf4-api`dependency so it will be used during the tests even if there is another logging engine in the dependency.

When there are multiple logging engines dependencies in the `pom.xml`, SLF4J will show anyway a message like the following:

[code lang=text]
    SLF4J: Class path contains multiple SLF4J bindings.
    SLF4J: Found binding in [jar:file:.m2/repository/org/slf4j/slf4j-nop/1.7.12/slf4j-nop-1.7.12.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: Found binding in [jar:file:.m2/repository/org/slf4j/slf4j-log4j12/1.7.12/slf4j-log4j12-1.7.12.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
    SLF4J: Actual binding is of type [org.slf4j.helpers.NOPLoggerFactory]
```

To summarize, a complete configuration example is the following:

[code lang=xml]
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>${slf4j.version}</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
            <scope>runtime</scope>
        </dependency>
```

Again, the last dependency should be adjusted to match the desired logging engine.

As I mentioned before, logging can be useful when creating new tests. In this case (**Test creation mode**), we can _temporarily_ replace the `slf4j-nop` with the `slf4j-simple` dependnecy which enables the [SLF4J Simple logger](http://www.slf4j.org/apidocs/org/slf4j/impl/SimpleLogger.html).

The logging messages will now be shown in the console window during tests execution as `System.err` messages. No configuration file is needed.

By default, the Simple logger does not log DEBUG messages. Standard logging level is INFO.

You can customize the Simple logger behaviour using system variables documented [here](http://www.slf4j.org/apidocs/org/slf4j/impl/SimpleLogger.html).

A _smart_ way to define Simple logger configuration is using the [Surefire plugin configuration section] (http://maven.apache.org/surefire/maven-surefire-plugin/examples/system-properties.html):

[code lang=xml]
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.18.1</version>
                <configuration>
                    <systemPropertyVariables>
                        <org.slf4j.simpleLogger.defaultLogLevel>DEBUG</org.slf4j.simpleLogger.defaultLogLevel>
                        <org.slf4j.simpleLogger.showDateTime>true</org.slf4j.simpleLogger.showDateTime>
                    </systemPropertyVariables>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

In the **systemPropertyVariables** section we can create tags with the Simple logger variable name. In the above example, DEBUG and timestamp logging are enabled.
