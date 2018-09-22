---
author: gualtierotesta
comments: true
date: 2015-07-05 10:00:28+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2015/07/05/java-how-to-check-jar-version-at-runtime/
slug: java-how-to-check-jar-version-at-runtime
title: 'Java: how to check jar version at runtime'
wordpress_id: 396
categories:
- Java
tags:
- coding
- jar version
- java
- jdk
- manifest
- version
---

Class loading issues can happen in complex execution environments. Situation gets worst when there are different versions of the same library "loaded" and we are not sure which our application is picking up.

To help debugging this issue, we could check at runtime which version of the library is loaded with the our application.

A code like the following can help:

[code language="java"]// Example using HTMLEmail from Apache Commons Email 
Class theClass = HtmlEmail.class;
 
// Find the path of the compiled class 
String classPath = theClass.getResource(theClass.getSimpleName() + ".class").toString(); 
System.out.println("Class: " + classPath); 

// Find the path of the lib which includes the class 
String libPath = classPath.substring(0, classPath.lastIndexOf("!")); 
System.out.println("Lib:   " + libPath); 

// Find the path of the file inside the lib jar 
String filePath = libPath + "!/META-INF/MANIFEST.MF"; 
System.out.println("File:  " + filePath); 

// We look at the manifest file, getting two attributes out of it 
Manifest manifest = new Manifest(new URL(filePath).openStream()); 
Attributes attr = manifest.getMainAttributes(); 
System.out.println("Manifest-Version: " + attr.getValue("Manifest-Version")); 
System.out.println("Implementation-Version: " + attr.getValue("Implementation-Version"));
```

The above code shows how to find jar file path and how to read the contents of a file (it is usually the Manifest) to read some info from it.
