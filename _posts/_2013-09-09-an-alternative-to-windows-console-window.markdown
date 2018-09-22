---
author: gualtierotesta
comments: true
date: 2013-09-09 19:00:17+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/09/09/an-alternative-to-windows-console-window/
slug: an-alternative-to-windows-console-window
title: An alternative to Windows console window
wordpress_id: 138
categories:
- Tools
tags:
- coding
- command prompt
- console
- shell
- window shell
- windows
---

The native Windows console window is for me not usable for real programming work because it is too limited compared to shell terminals available in Linux (my second programming platform).

A good alternative solution I have found is Console, available from http://sourceforge.net/projects/console/

[![2013-09-09_fig1](http://gualtierotesta.files.wordpress.com/2013/09/2013-09-09_fig1.png?w=1024)](http://gualtierotesta.files.wordpress.com/2013/09/2013-09-09_fig1.png)

Console has many configurable options, including the possibility to change font, colors and background. (Hint: do not forget to specify Consolas as font).

[![2013-09-09_fig2](http://gualtierotesta.files.wordpress.com/2013/09/2013-09-09_fig2.png)](http://gualtierotesta.files.wordpress.com/2013/09/2013-09-09_fig2.png)

Console window dimensions can be changed on the fly; selecting text in the window is simply a Ctrl + mouse action. Copy and Paste functions are available from menu and toolbar.

Console is a good and working solution for all command level actions. I use it every day for non standard maven and subversion operations.

Warning: Console is not a shell interpreter. The shell interpreter is the Windows _cmd.exe_. A Console configuration options let you specify a different shell. For example it is possibile to use the Bash shell included in the Git package. You can read [here ](http://lostechies.com/jimmybogard/2010/04/05/integrating-the-git-bash-into-console/)for more details.
