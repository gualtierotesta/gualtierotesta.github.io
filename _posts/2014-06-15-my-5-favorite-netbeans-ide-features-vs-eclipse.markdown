---
author: gualtierotesta
comments: true
date: 2014-06-15 19:00:53+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2014/06/15/my-5-favorite-netbeans-ide-features-vs-eclipse/
slug: my-5-favorite-netbeans-ide-features-vs-eclipse
title: My 5 favorite NetBeans IDE features (vs. Eclipse)
wordpress_id: 307
categories:
- Netbeans
- Tools
tags:
- code checks
- coding
- findbugs
- hints
- java
- junit
- netbeans
- test
- unit test
- unit testing
---

In my current project, Eclipse Kepler has been selected by the customer as the preferred IDE for all main tasks. Of course, I’m also using NetBeans 8.0 for some activities like, for example, legacy code test coding.

I have already used Eclipse some years ago and, at that time (2009), Eclipse Galileo were quite superior to NetBeans 6.x: faster, rich of code hints and warnings, full of useful plugins. So, I was not really happy when the project moved to NetBeans (because of its Swing support).

Now let me compare them again, Eclipse Kepler 4.3 vs NetBeans 8.0, on same project (a big ant based web project) and on the same PC (Win7 64bit 4G RAM).


### Speed


My first big surprise. Five years ago, when I moved from Galileo to NetBeans I really missed Eclipse speed: coding, windowing, compilation, everything were significantly faster in Eclipse.

Now this speed advantage is gone. I don’t have benchmarks to demonstrate my feeling but now my coding is visibly faster with NetBeans 8.0 than with Eclipse Kepler. If you install Findbugs and PMD plugins, Eclipse becomes really slow.


### Native testing support


Second surprise. Why, in 2014, an IDE for Java EE developers does not include full testing support ?

I cannot imagine any real EE developers which does not need any kind of test support from the IDE.

In NetBeans there is complete native jUnit and TestNG support (templates, test runs, jump to test option in the editor, both *Test and *IT files support) which I found very useful and efficient.

Eclipse native support is poor but of course it can be extended via plugin. I’m using MoreUnit plugin which is quite nice and add many missing features to Eclipse. See [here ](http://gualtierotesta.wordpress.com/2014/05/25/tutorial-unit-testing-in-eclipse-with-moreunit-plugin/)for a small tutorial.

But even if you install a test support plugin like MoreUnit, still an important Eclipse limitation remains : the possibility to have a dir for test files separated from the main src dir, which is an essential feature to let us handling deployments. If you don’t believe me, have a look at this 2008 bug still open: [https://bugs.eclipse.org/bugs/show_bug.cgi?id=224708](https://bugs.eclipse.org/bugs/show_bug.cgi?id=224708) and its related bugs.
Simply unbelievable.


### Java Hints and FindBugs integration


While coding, it is important to have feedbacks from the IDE about the quality of the code and possible errors. [NetBeans Java hints](http://wiki.netbeans.org/Java_Hints) is a very reach set of the code checks which helps me to avoid stupid errors and keep high the quality of my work. In addition, NetBeans includes first class support for FindBugs which, again, helps me during coding sessions.

I’ve found NetBeans code quality checks support better than the one available in Eclipse Kepler + FindBugs plugin. More checks and better control on how, for example, disable a FindBugs warning on a line (because it is ok) using an annotation: just a click with NetBeans and by hand with Eclipse.


### Coding support: templating, completion,interfaces…


I like NetBeans coding support: code templates (some letters + tab to get most used keywords and structures (see [here ](http://gualtierotesta.wordpress.com/2014/03/09/tutorial-how-to-create-a-junit-test-method-template-in-netbeans/)for some examples), auto-completion, now also using sub words, I can easily jump from an interface to its implementation, the new indent guide lines.

Similar features are available in Eclipse using, for example, the Code Recommenders plugin but I’ve found it quite slow on my machine so I had to disinstall it.


### User Interface


NetBeans user interfaces has improved a lot while keeping its strong point: it is easy to use. Windows and menus are easy to find and the full screen mode is very useful on small screens. I really like the color coding setup (Norway Today) which makes code reading a pleasure and the dark skins for working late in the evening.

On the contrary, Eclipse interfaces remains somehow confusing and, my personal opinio, perspectives and workspaces are simply a waste of time.
