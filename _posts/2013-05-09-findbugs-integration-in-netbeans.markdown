---
author: gualtierotesta
comments: true
date: 2013-05-09 19:00:03+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/05/09/findbugs-integration-in-netbeans/
slug: findbugs-integration-in-netbeans
title: Findbugs integration in NetBeans
wordpress_id: 49
categories:
- Java
- Netbeans
tags:
- coding
- findbugs
- java
- netbeans
---

[FindBugs](http://findbugs.sourceforge.net/) is a "must-use" tool for any serious Java programmer to detect real and potential critical bugs in our code.

There are two different ways to use FindBugs:



	
  * while we are coding (inside the IDE) and

	
  * as a step in the project quality checks phase (usually via a maven plugin).


NetBeans support both usages.First of all, we need to enable the plugin "FindBugs Integration" in "Tools --> Plugins"

[caption id="attachment_54" align="alignnone" width="1145"][![FindBugs Integration plugin](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-09_fig1.jpg)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-09_fig1.jpg) FindBugs Integration plugin[/caption]

FindBugs analysis configuration is available in Tools --> Options --> Editor --> Hints.

Select FindBugs in the "Language" drop-down menu.

[caption id="attachment_55" align="alignnone" width="913"][![FindBugs Options Window](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-09_fig2.jpg)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-09_fig2.jpg) FindBugs Options Window[/caption]

In this window, there is a "Run FindBugs in Editor" check-box. If enabled, FindBugs will be executed on the .java file we are working on (just after the save).

Note: FindBugs works on .class files and not on source (.java files).

FindBugs can be executed on the complete project using the Source --> Inspect function:

[caption id="attachment_57" align="alignnone" width="769"][![Inspection Dialog](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-09_fig3.jpg)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-09_fig3.jpg) Inspection Dialog[/caption]

The results of the FindBugs analysis on the complete project is reported in the "Inspector" window:

[caption id="attachment_58" align="alignnone" width="1176"][![Inspector Window](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-09_fig4.jpg)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-09_fig4.jpg) Inspector Window[/caption]

**Important:**



	
  * Editor level analysis is done on a single file and with minimal FindBugs effort (to avoid to slow down the editor)

	
  * Inspection analysis is done on all project class files with deeper FindBugs effort. In this case, it is acceptable to have a slower but more accurate analysis.


The two different analysis can bring to different violations detected.
