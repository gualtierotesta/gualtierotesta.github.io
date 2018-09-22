---
author: gualtierotesta
comments: true
date: 2013-10-22 19:45:22+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/10/22/improving-netbeans-findbugs-integration/
slug: improving-netbeans-findbugs-integration
title: Improving NetBeans FindBugs integration
wordpress_id: 207
categories:
- Java
- Netbeans
tags:
- code checks
- coding
- easypmd
- external plugin
- fbcontrib
- findbugs
- hints
- java
- netbeans
- plugin
- quality checks
---

In NetBeans 7.4, the FindBugs integration inside the IDE has been improved giving us the possibility to specify additional rules to be checked.




FindBugs has a plugin architecture that let user to add custom rules sets. I usually add FindBugs rules from [FBContrib](http://fb-contrib.sourceforge.net), a FindBugs auxiliary rules set.




There are hundred of rules included in FBContrib. Complete list can be found [here](http://fb-contrib.sourceforge.net/bugdescriptions.html).




What is amazing in NetBeans is that not only you can tell the IDE to use the additional rules sets but that the new rules are seamless integrated in the NetBeans interface. For example, FBContrib rules are grouped together with standard FindBugs checks.




First step is to specify the path to the FBContrib jar file (downloaded from the FBContrib website) in the FindBugs configuration (Options --> Editor --> Hints --> FindBugs )




[![2013-10-22b-fig1](http://gualtierotesta.files.wordpress.com/2013/10/2013-10-22b-fig1.png?w=1024)](http://gualtierotesta.files.wordpress.com/2013/10/2013-10-22b-fig1.png)




Press "Custom FindBugs Plugins" button.




[![2013-10-22b-fig2](http://gualtierotesta.files.wordpress.com/2013/10/2013-10-22b-fig2.png)](http://gualtierotesta.files.wordpress.com/2013/10/2013-10-22b-fig2.png)




The "Add" button let us specify one or more additional jar files.




As I already mentioned, the rules included in the FBContrib jar file are integrated with the other native FindBugs rules.




See for example, the rules related to the "Secure" description before




[![2013-10-22b-fig3](http://gualtierotesta.files.wordpress.com/2013/10/2013-10-22b-fig3.png?w=1024)](http://gualtierotesta.files.wordpress.com/2013/10/2013-10-22b-fig3.png)




and after FBContrib has been specified:




[![2013-10-22b-fig4](http://gualtierotesta.files.wordpress.com/2013/10/2013-10-22b-fig4.png?w=1024)](http://gualtierotesta.files.wordpress.com/2013/10/2013-10-22b-fig4.png)




Both native and FBContrib rules can be enabled and disabled one by one.




With NetBeans Hints, FindBugs and, may be, PMD (see this [post](http://gualtierotesta.wordpress.com/2013/10/07/pmd-in-netbeans-the-easypmd-plugin/) on PMD plugin ) _all_ enabled there is a good chance that one problematic line can have more than one warning. In this case I suggest to keep native NetBeans hints enabled and disable the check in the other tools, mainly because NetBeans native checks are faster.



