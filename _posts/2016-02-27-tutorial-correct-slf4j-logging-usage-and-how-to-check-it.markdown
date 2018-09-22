---
author: gualtierotesta
comments: true
date: 2016-02-27 19:00:01+00:00
layout: post
slug: tutorial-correct-slf4j-logging-usage-and-how-to-check-it
title: 'Tutorial: Correct SLF4J logging usage and how to check it'
categories:
- java
tags:
- coding
- exception
- findbugs
- java
- log levels
- loggers
- logging
- plugin
- slf4j
---

*Last update: 22 Set 2018*

[SLF4J](https://www.slf4j.org/) is a very popular logging facade but, like all libraries we use, there is a chance that we use it in a wrong or at least in a not optimal way.

In this tutorial we will list common logging errors and how we can detect them using FindBugs. We will also mention PMD and Sonar Squid checks when relevant.

We will use two external FindBugs plugins which add logging detectors to FindBugs.

The first one is a [SLF4J only plugin](http://kengotoda.github.io/findbugs-slf4j/) by Kengo Toda which contains SLF4J detectors only.

The second plugin is the popular [FB Contrib](http://fb-contrib.sourceforge.net/) which contains, among many others, some logging detectors.

For how to use FindBugs plugins, please refer to the following posts:

* [Using FindBugs with Maven](https://gualtierotesta.wordpress.com/2015/06/14/tutorial-using-findbugs-with-maven/)
* [NetBeans FindBugs plugin](https://gualtierotesta.wordpress.com/2015/06/07/findbugs-plugins/)

Note: in all examples we will assume the following imports:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
```

# 1. Logger definition

Wrong way:

```java
W1a. Logger log = LoggerFactory.getLogger(MyClass.class);
W1b. private Logger logger = LoggerFactory.getLogger(MyClass.class);
W1c. static Logger LOGGER = LoggerFactory.getLogger(AnotherClass.class);
```

Correct way:

```java
C1a. private static final Logger LOGGER = LoggerFactory.getLogger(MyClass.class);
C1b. private final Logger logger = LoggerFactory.getLogger(getClass());
```

_General rule_: the logger should be final and private because there are no reasons to share it with other classes or to re-assign it.

On the contrary there is no general agreement if the logger should be static or not. SLF4J plugin favors non static version (C1b) while PMD (“LoggerIsNotStaticFinal” rule) and Sonar (squid rule S1312) prefer a static logger (C1a) so both options should be considered as valid.

Additional info:

* [SLF4J FAQ](http://slf4j.org/faq.html#declared_static)
* [Apache Commons Static Log](http://wiki.apache.org/commons/Logging/StaticLog).

Please note that

* in the static version (C1a), the logger name is usually in uppercase characters as all constant fields. If not, PMD will report a “VariableNamingConventions” violation.

* in both cases, the suggested name is “logger/LOGGER” and not “log/LOG” because some naming conventions avoid too short names (less than four characters). Moreover log is the verb, more suited for a method name.

* the W1c is wrong because we are referring to a class (AnotherClass) which is not the class where the logger is defined. In the 99% of the cases, this is due to a copy & paste from one class to another.

Related FindBugs (SLF4J plugin) checks:

* SLF4J_LOGGER_SHOULD_BE_PRIVATE
* SLF4J_LOGGER_SHOULD_BE_NON_STATIC
* SLF4J_LOGGER_SHOULD_BE_FINAL
* SLF4J_ILLEGAL_PASSED_CLASS

# 2. Format string

Wrong way:

```java
W2a. LOGGER.info("Obj=" + myObj);
W2b. LOGGER.info(String.format(“Obj=%s”, myObj));
```

Correct way:

```java
C2. LOGGER.info("Obj={}",myObj);
```

_General rule_: the format string (the first argument) should be constant, without any string concatenation. Dynamic contents (the myObj value in the example) should be added using the placeholders (the '{}' ).

Motivation is simple: we should delay logging message creation **after** the logger has established if the message should be logged or not, depending on the current logging level. If we use string concatenation, message is built any way, regardless the logging level which is a waste of CPU and memory resources.

Related FindBugs (SLF4J plugin) checks:

* SLF4J_FORMAT_SHOULD_BE_CONST Format should be constant
* SLF4J_SIGN_ONLY_FORMAT Format string should not contain placeholders only

Related FindBugs (FB Contrib plugin) checks:

* LO_APPENDED_STRING_IN_FORMAT_STRING Method passes a concatenated string to SLF4J's format string

# 3. Placeholder arguments

Wrong way:

```java
W3a. LOGGER.info("Obj={}",myObj.getSomeBigField());
W3b. LOGGER.info("Obj={}",myObj.toString());
W3c. LOGGER.info("Obj={}",myObj, anotherObj);
W3d. LOGGER.info("Obj={} another={}",myObj);
```

Correct way:

```java
C3a. LOGGER.info("Obj={}",myObj);
C3b. LOGGER.info("Obj={}",myObj.log());
```

_General rule_: the placeholder should be an object (C3a), not a method return value (W3a) in order to post-pone its evaluation after logging level analysis (see previous paragraph). In W3a example, the mehod getSomeBigField() will be always called, regardless the logging level. For the same reason, we should avoid W3b which is semantically equivalent to C3a but it always incurs in the toString() method invocation.

Solutions W3c and W3d are wrong because the number of placeholders in the format string does not match the number of placeholders arguments.

Solution C3b could be somehow misleading because it includes a method invocation but it could be useful whenever the myObj contains several fields (for example it is a big JPA entity) but we do not want to log all its contents.

For example, let's consider the following class:

```java
public class Person {
    private String id;
    private String name;
    private String fullName;
    private Date birthDate;
    private Object address;
    private Map<String, String> attributes;
    private List phoneNumbers;
```

its toString() method will most probably include all fields. Using the solution C3a, all their values will be printed in the log file.

If you do not need all this data, it is useful to define a **helper** method like the following:

```java
public String log() {
    return String.format("Person: id=%s name=%s", this.id, this.name);
}
```

which prints relevant information only. This solution is also CPU and memory lighter than toString().

What is relevant ? It depends on the application and on the object type. For a JPA entity, I usually include in the log() method the ID field (in order to let me find the record in the DB if I need all columns data) and, may be, one or two important fields.

For no reason, passwords fields and/or sensitive info (phone numbers,...) should be logged. This is an additional reason to not log using toString().

Related FindBugs (SLF4J plugin) checks:

* SLF4J_PLACE_HOLDER_MISMATCH

# 4. Debug messages

IMPORTANT: rule #4 (see [5 rules article](https://gualtierotesta.wordpress.com/2015/11/29/the-5-java-logging-rules/)) guide us to use a guarded debug logging

```java
if (LOGGER.isDebugEnabled()) {
    LOGGER.debug(“Obj={}”, myObj);
}
```

Using SLF4J, if the placeholder argument is an object reference (see solutions C3a/C3b), we can use avoid the if in order to keep the code cleaner.

So it is safe to use the following:

```java
LOGGER.debug(“Obj={}”, myObj);
```

# 5. Exceptions

Proper exceptions logging is an important support for problems analysis but it is easy to neglect its usefulness.

Wrong way:

```java
W5a. catch (SomeException ex) { LOGGER.error(ex); }..
W5b. catch (SomeException ex) { LOGGER.error("Error:" + ex.getMessage()); }..
```

Correct way:

```java
C5. catch (SomeException ex) { LOGGER.error("Read operation failed: id={}", idRecord, ex); }..`
```

_General rules_:

1. Do not remove the stack trace information by using getMessage() (see W5b) and not the complete exception. The stack trace often includes the real cause of the problem which is easily another exception raised by the underlying code. Logging only the message will prevent us to discover the real cause of the problem.

2. Do show significant (for the human which will analyze the log file) information in the logging message showing a text explaining what we wanted to perform while the exception was raised (not the exception kind or messages like "error": we know already something bad happened). What we need to know is what we were doing and on which data.

The C5 example tells we were trying to read the record with a specific ID whose value has been written in the log with the message.

Please note that C5 use one placeholder in the format string but there are two additional arguments. This is not an error but a special pattern which is recognized by SLF4J as an exception logging case: the last argument (ex in the C5 example) is considered by SLF4J as a Throwable (exception) so it should be not included in the format string.

Related FindBugs (SLF4J plugin) checks:

* SLF4J_MANUALLY_PROVIDED_MESSAGE: the message should not be based on Exception getMessage()
