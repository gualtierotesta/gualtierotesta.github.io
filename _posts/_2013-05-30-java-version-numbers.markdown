---
author: gualtierotesta
comments: true
date: 2013-05-30 19:00:25+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/05/30/java-version-numbers/
slug: java-version-numbers
title: Java Version numbers
wordpress_id: 95
categories:
- Java
tags:
- cpu
- critical path update
- java
- jdk
- jre
- limited update
- oracle
- release
- version
---

Java Runtime Environment (_JRE_) and Java Development Kit (_JDK_) version numbers follow a strict policy that Oracle has changed few months ago.

Knowing the rules behind the version numbers let us understand which are the benefits and the risks to migrate our environment to newest versions.

First of all, two Oracle definitions:



	
  * _Limited Edition_ : release that includes new functionalities and/or bug fixes not related to security problems. Limited Edition releases have always **even** numbers.

	
  * _Critical Path Update_ (CPU): release that includes only security bug fixes. No new functionality. CPU releases have always **odd** numbers.


So JRE/JDK 7 release 11 (in short 7u11 where u = update) is a CPU release while JRE/JDK 6 release 38 (6u38) has introduced new functionality because it is a Limited Edition.

In general it's worth to migrate to a new CPU update (to secure our application) as soon it is available. Limited Edition releases can (should) be tested with more time before adoption.

Since last December, Oracle release plan was quite stable but in the last months a significant amount of security vulnerabilities have been discovered and fixed. The traditional numbering scheme needed to be updated: more odd numbers were needed.

So for the future, the numbering scheme is the following:

	
  * Limited Edition: use **20** as numbering step (from 20 to 40 to 60..) instead of 2

	
  * CPU: use **5** as numbering step (+1 if final number is even) like : 45 (first CPU after 40), 51 and 55.


The odd numbers not used (41, 43, 47.. in the above example) will be used, if necessary, for urgent security fixes.

More details [here](http://www.oracle.com/technetwork/java/java-update-release-numbers-change-1836624.html).

Last Java 6 releases:



	
  * 6u38  (Limited Edition)

	
  * 6u39

	
  * 6u41

	
  * 6u43

	
  * 6u45


Last Java 7 releases:

	
  * 7u10 (Limited Edition)

	
  * 7u11

	
  * 7u13

	
  * 7u15

	
  * 7u17

	
  * 7u21


Note: 7u19 not released
