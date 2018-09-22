---
author: gualtierotesta
comments: true
date: 2013-06-20 19:00:19+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/06/20/new-java-release-7u25/
slug: new-java-release-7u25
title: 'New Java release: 7u25'
wordpress_id: 110
categories:
- Java
tags:
- cpu
- critical path update
- java
- jdk
- jre
- manifest
- oracle
- release
- security
- version
---

[![Java_Logo](http://gualtierotesta.files.wordpress.com/2013/06/java_logo.jpg?w=80)](http://gualtierotesta.files.wordpress.com/2013/06/java_logo.jpg)Oracle has released new Java 7 CPU (see meaning [here](http://gualtierotesta.wordpress.com/2013/05/30/java-version-numbers/)) release: 7u25.

The release contains, in addition to usual security bug fixes, several changes that are also targeted to improve security.

The complete list of changes is [here](http://www.oracle.com/technetwork/java/javase/7u25-relnotes-1955741.html) but let me remark the most important changes:



	
  * several changes on signed jar management including the check, before execution, that the certificate is valid (not revoked). The check can delay applet/application startup.

	
  * new attributes on JAR manifest file (permissions, to control jar execution authorizations, and codebase,to control who is using the JAR) has been introduced to let JAR author to better control JAR usage.


Other 40 bug fixes are documented [here](http://www.oracle.com/technetwork/topics/security/javacpujun2013-1899847.html).
