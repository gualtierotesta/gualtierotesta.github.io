---
author: gualtierotesta
comments: true
date: 2014-02-02 20:00:58+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2014/02/02/netbeans-plugin-for-file-line-endings/
slug: netbeans-plugin-for-file-line-endings
title: NetBeans plugin for file line endings
wordpress_id: 233
categories:
- Netbeans
- Tools
tags:
- CR
- LF
- line endings
- netbeans
- oracle
- plugin
- tools
---

Nowadays it is common to work in a multi-platform (Windows, Linux, Mac) development environment and that means different file line endings characters.

To terminate text files lines, Windows uses two characters, CR (Carriage Return) and LF (Line Feed), while Linux and Mac OS X use LF character only and "old" Mac OS uses CR only.

Three platform, three combinations. We are not lucky.

NetBeans can handle all combinations, while opening and saving text files (.java, .html, .css, .php,....) but this is not necessarily the case for all developments tools we are using.

Best approach is to use just one and, we are now lucky, one plugin can help us.

The _"Change Line Endings on Save"_ plugin can be found [here ](http://plugins.netbeans.org/plugin/46829/change-line-endings-on-savehttp://)and on NetBeans plugin portal.

Once installed, we can decide to have NetBeans or project based setup. Development teams should use project based setup so every developer uses the same configuration.

NetBeans based setup can be found on Tools → Options → Editor → Line Endings as in the following image:

[![2014-02-02 fig1](http://gualtierotesta.files.wordpress.com/2014/01/2014-02-02-fig1.jpg)](http://gualtierotesta.files.wordpress.com/2014/01/2014-02-02-fig1.jpg)Project based setup is available on project Properties page:

[![2014-02-02 fig2](http://gualtierotesta.files.wordpress.com/2014/01/2014-02-02-fig2.jpg)](http://gualtierotesta.files.wordpress.com/2014/01/2014-02-02-fig2.jpg)Note: project setup is saved in the nb-configuration.xml file (in the project root dir).
