---
author: gualtierotesta
comments: true
date: 2015-06-07 10:00:00+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2015/06/07/findbugs-plugins/
slug: findbugs-plugins
title: FindBugs plugins
wordpress_id: 369
categories:
- Eclipse
- IntelliJ
- Java
- Netbeans
tags:
- code checks
- code quality
- fbcontrib
- findbugs
- java
- plugin
- security
---

[FindBugs](http://findbugs.sourceforge.net/) is a key code quality tool for Java based projects. 


It includes several dozens of bug patterns which are used by FindBugs to identify potential bugs and, more in general, weaknesses in our code.




FindBugs has a plugin architecture which can be used to extend the set of detectors (bug patterns) used during the analysis.




There are few open source projects which aim to develop FindBugs plugin.


My preferred one is [Fb-Contrib](http://fb-contrib.sourceforge.net/) which contains a significant amount of additional detectors. See [here](http://fb-contrib.sourceforge.net/bugdescriptions.html) for the complete list. Most of them are really useful to detect poor code quality.

Another interesting plugin is [Find Security Bugs](http://h3xstream.github.io/find-sec-bugs/); the focus here is on security vulnerabilities (list [here](http://h3xstream.github.io/find-sec-bugs/bugs.htm)) like using unsecured random generator or not checking data received from the user.

Let's have a look at versions dependencies:
<table cellpadding="4" width="100%" cellspacing="0" > 
<tbody >
<tr valign="top" >

<td width="25%" >**JDK**
</td>

<td width="25%" >**FindBugs**
</td>

<td width="25%" >**FB-Contrib**
</td>

<td width="25%" >**Find Security Bugs**
</td>
</tr>
<tr valign="top" >

<td width="25%" >7 and 8
</td>

<td width="25%" >3.x
</td>

<td width="25%" >6.x
</td>

<td width="25%" >1.3 and above
</td>
</tr>
<tr valign="top" >

<td width="25%" >5 and 6
</td>

<td width="25%" >2.x
</td>

<td width="25%" >5.x
</td>

<td width="25%" >1.2
</td>
</tr>
</tbody>
</table>


All plugins are released in .jar format and they can be easily added to the FindBugs :






	
  * FindBugs stand-alone: place the jar in the plugins dir inside FindBugs installation dir

	
  * Eclipse FindBugs plugin: use the plugin options to specify the plugin path or place the jar file inside FindBugs plugins dir

	
  * NetBeans FindBugs integration: use Custom FindBugs Plugins button inside Editor → Hints → FindBugs page.

	
  * IntelliJ FindBugs plugin: add new plugin in the Plugin Configuration tab.




After adding new plugins, review the list of detectors enabled. New detectors are usually added but not enabled.
