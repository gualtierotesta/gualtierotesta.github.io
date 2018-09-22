---
author: gualtierotesta
comments: true
date: 2014-02-25 20:00:13+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2014/02/25/tutorial-license-headers-in-netbeans-reloaded/
slug: tutorial-license-headers-in-netbeans-reloaded
title: 'Tutorial: license headers in NetBeans (reloaded)'
wordpress_id: 249
categories:
- Netbeans
- Tools
- tutorial
tags:
- external plugin
- java
- license
- license header
- netbeans
- plugin
- project configuration
---

An **alternative** solution to the native NetBeans license header management (see my previous[ post](http://gualtierotesta.wordpress.com/2014/02/18/tutorial-license-headers-in-netbeans/)) is the “License Changer” plugin by nhoffmann available on NetBeans plugin portal ([http://plugins.netbeans.org/plugin/17960/license-changer](http://plugins.netbeans.org/plugin/17960/license-changer)).

The plugin is based on license template concept. NetBeans installation includes several license templates (Tools → Templates) for the most popular Open Source licenses: [![2014-02-25_pic01](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic01.jpg)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic01.jpg)The plugin uses one of the available templates. If you have your own license file, you have to include it in the template list (but the plugin help on this).

**NOTE**: all custom license files will be saved in the NetBeans User Directory. You can have a look at the beginning of View → IDE Log for your real path.

With the plugin we can do more that native NetBeans solution except for one use-case: when you have a proprietary and project specific license. In this case NetBeans let us save the license file within the project (so all team members can use it) while, with the plugin, all licenses are stored in the user NetBeans installation (which is not shared across the team).

Let's describe now the plugin.

After plugin installation, you can select a file or a package and apply “Change License Header” (see picture below).

[![2014-02-25_pic02](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic02.jpg)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic02.jpg)A wizard starts. On the first page we can select the files types on which the plugin should operate on (see 1). Nice added feature is the possibility to change the line endings character (see 2).

[![2014-02-25_pic03](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic03.jpg?w=1024)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic03.jpg)



Second page of the wizard is the following:

[![2014-02-25_pic04](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic04.jpg?w=1024)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic04.jpg)

We can select the license from the template list (see 1) or browse the disk for a custom license file (see 2) which will be included in the template list by the plugin.

**IMPORTANT**: The checkbox “Set as default project license” (see 3) is very important. If checked, the plugin will save the selected license in the project configuration (the same used by native NetBeans solution). All new created files (including file types not supported by the plugin) will inherit the setting and they will have the correct license header.

After license selection:

[![2014-02-25_pic05](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic05.jpg?w=1024)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-25_pic05.jpg)The rest of the wizard is very simple.

**NOTE**: One little annoying problem with the plugin is that the selected license is not kept for the following plugin invocations.

Typical procedure :



	
  1. install the plugin

	
  2. run the plugin on a project file or package or folder,in order to include **all existing** project files

	
  3. use standard NetBeans File → New File procedure to create new project files


