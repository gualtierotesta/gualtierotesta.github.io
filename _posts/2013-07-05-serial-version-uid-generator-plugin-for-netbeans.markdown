---
author: gualtierotesta
comments: true
date: 2013-07-05 19:00:23+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/07/05/serial-version-uid-generator-plugin-for-netbeans/
slug: serial-version-uid-generator-plugin-for-netbeans
title: Serial version UID generator plugin for NetBeans
wordpress_id: 119
categories:
- Netbeans
tags:
- coding
- netbeans
- plugin
- serialize
- serialVersionUID
---

**UPDATE (June 2014)**: the Plugin 49508 presented in this post is no more available at NetBeans plugin portal for unknown (to me) reasons. Moreover the author of another plugin (6887, available from [here ](http://plugins.netbeans.org/plugin/6887/serialversionuid-generator)is reporting unfear cloning of his work; see comments below).

When I moved from Eclipse to NetBeans on my main project, I realized there are some small features which are not in NetBeans. One of them is the possibility to generate the serial version UID.

Standard NetBeans option is to show, for all classes which implements Serializable and do not have a serialVersionUID field, an "Add default serialVersionUID" hint. Generated serialVersionUID (i.e. calculated on class contents) hint is not available.

[![2013-07-05_img1](http://gualtierotesta.files.wordpress.com/2013/07/2013-07-05_img1.png)](http://gualtierotesta.files.wordpress.com/2013/07/2013-07-05_img1.png)

The default hint always creates the following field

[code language="java"]private static final long serialVersionUID = 1L;```

The [NB Serial version UID generator plugin](http://plugins.netbeans.org/plugin/49508/nb-serial-version-uid-generator) adds the feature to have both default and generated serialVersionUID.

[![2013-07-05_img4](http://gualtierotesta.files.wordpress.com/2013/07/2013-07-05_img4.png)](http://gualtierotesta.files.wordpress.com/2013/07/2013-07-05_img4.png)

The plugin implements a new "serialVersionUID not defined" hint in the General section. Do not forget to disable the standard NetBeans hint.

[![2013-07-05_img2](http://gualtierotesta.files.wordpress.com/2013/07/2013-07-05_img2.png)](http://gualtierotesta.files.wordpress.com/2013/07/2013-07-05_img2.png)
