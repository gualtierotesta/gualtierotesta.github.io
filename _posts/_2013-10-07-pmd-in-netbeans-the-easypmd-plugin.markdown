---
author: gualtierotesta
comments: true
date: 2013-10-07 19:00:49+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/10/07/pmd-in-netbeans-the-easypmd-plugin/
slug: pmd-in-netbeans-the-easypmd-plugin
title: 'PMD in NetBeans: the EasyPMD plugin'
wordpress_id: 51
categories:
- Java
- Netbeans
- Tools
tags:
- code checks
- coding
- easypmd
- external plugin
- gianluca costa
- hints
- java
- netbeans
- pdm
- plugin
- quality checks
- source code analyzer
---

[PMD ](http://pmd.sourceforge.net/) is a powerful source code quality checker which analyze our code and report bad coding habits, failures to respect naming conventions, inefficiencies and many others.

PMD has an internal list of rules to be checked. Rules are divided in sets. Each set contains several rules.

For example the "Comments" set includes all rules related to code comments.

PMD rules sets are documented [here](http://pmd.sourceforge.net/pmd-5.0.5/rules/index.html).

On my opinion a PMD rule violation should be intended as a suggestion to better check the code to be sure that the offending code line is really what we want to have. Often PMD is right to say the line should be fixed or at least improved.

In NetBeans PMD is enabled by the **EasyPMD3** plugin available from NetBeans plugin area and from the plugin home page ([ http://gianlucacosta.info/software/easypmd/](http://gianlucacosta.info/software/easypmd/) ).

Note: There is an [enhancement request](https://netbeans.org/bugzilla/show_bug.cgi?id=230237) to introduce native PMD support in NetBeans, like what has been done with FindBugs.

One very important point to remember is that EasyPMD (and so PMD) is controlled by the "Action Items" window settings. Just click on the "Action Items" window filter icon and check that EasyPmd notifications are enabled. If not enabled,PMD will not check the code.

[![2013-10-05a](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05a.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05a.png)

When enabled, PMD checks Java code and



	
  * reports in the "Action Items" window all rules violations

	
  * marks as a blue circle the lines in the open editor window that contain a rule violation


[![2013-10-05b](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05b.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05b.png)

In the above picture, PMD reports that the field _callCount_ is initialized to zero but this is a meaningless operation because zero is the default value for numeric fields. Actually this initialization will cost us few cpu cycles.

This is the PMD "RedundandFieldInitializer" rule belonging to "Optimization" rule set but there are many others.

Some PMD rules conflict with NetBeans hints because they check the same situation.

[![2013-10-05c](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05c.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05c.png)

The _processFindForm_() method in the picture reassigns one of its parameter (owner); PMD reports it at line 86 while NetBeans at line 106.

I usually disable the PMD rule if there is an equivalent NetBeans hint, also because some NetBeans hints give us the possibility to fix the problem with a dedicated refactoring (but not the one in the picture). PMD is an pure analyzer so it does not perform any change in the code.

There are several rules and rules sets available in PMD but not all of them are relevant for us. For example the "Android" rule set is of course not interesting for Java EE applications.

Thankfully we can select rules set and single rules we want PMD to check.

EasyPMD give us two approaches: select the rules sets to use but not the single rules or full freedom in rules and rule sets selection.

The first approach can be done with the EasyPMD UI interface (Tools --> Options --> Miscellaneus --> EasyPmd3):

[![2013-10-05d](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05d.png?w=1024)](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05d.png)

The list in the picture contains all rules set we want PMD to check. Single rules can be included or removed with the "Add Standard" and "Remove" buttons.

The alternative solution (full freedom) requires a preliminary activity: to prepare a jar file with the PMD rules set and rules we want to be checked. Detailed instructions to prepare the rules jar can be found [here.](http://gianlucacosta.info/blog/easypmd/custom-rulesets)

The basic idea is to create a xml file with contents like the following:

[code language="xml"]<?xml version="1.0"?>
<ruleset name="Custom ruleset" xmlns="http://pmd.sourceforge.net/ruleset/2.0.0">
<rule ref="rulesets/java/basic.xml"/>
<rule ref="rulesets/java/comments.xml">
    <exclude name="CommentSize"/>
</rule>```

In the fragment above, I selected the complete "basic" rule set and the "comments" rule set except the "CommentSize" rule.

Source and jar file I use can be found [here](https://dl.dropboxusercontent.com/u/27338103/blog/2013-10-05.zip)

Once the jar file with rules has been created, we instructs EasyPMD plugin to use it:

[![2013-10-05e](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05e.png)](http://gualtierotesta.files.wordpress.com/2013/05/2013-10-05e.png)

The rules jar is also useful to share PMD rules across the development team. The jar file can be placed in a shared area and all team members can refer to it in their (local) EasyPMD configuration.
