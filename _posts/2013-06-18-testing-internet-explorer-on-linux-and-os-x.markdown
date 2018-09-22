---
author: gualtierotesta
comments: true
date: 2013-06-18 19:00:43+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/06/18/testing-internet-explorer-on-linux-and-os-x/
slug: testing-internet-explorer-on-linux-and-os-x
title: Testing Internet Explorer on Linux and OS X
wordpress_id: 100
categories:
- Web
tags:
- internet explorer
- microsoft
- parallel
- virtual machine
- virtualbox
- vmware
---

As web developer, I often think that world would be better if we could forget Internet Explorer, just because of its peculiar way to interpret the web standards. Moreover different Internet Explorer versions have usually quite different behavior.

Unfortunately, this is not always possible and I have to check my web applications on IE 7, IE8, IE9 and now also on IE 10.

Microsoft has created a support site dedicated to whom need to test web sites and applications with IE: [www.modern.ie](http://www.modern.ie). The site offers several tools to help our compatibility checks.

Among them, I have found quite useful the [pre-built virtual machines](http://www.modern.ie/virtualization-tools) which allow us to run a specific Windows (from XP to 8) and a specific Internet Explorer (from 6 to 10) version inside a virtual machine.

On these machines, Windows is fully working (but with a 90 days time limit) and additional software can be installed (a JRE in my case).

If you a Linux or a OS X, this is very interesting solution to run IE on these boxes.



Depending on guest operating system, several virtual machines solutions are supported: VirtualBox, Parallel and VMWare.
