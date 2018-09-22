---
author: gualtierotesta
comments: true
date: 2015-09-06 19:00:34+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2015/09/06/netbeans-project-groups/
slug: netbeans-project-groups
title: NetBeans project groups
wordpress_id: 429
categories:
- Netbeans
tags:
- command line
- netbeans
- project groups
- projects
---

I'm involved on several projects and during my working day it can happen that I need to switch from one project to another.

In **NetBeans** I usually create a _project group_ for each project, to keep them well separated.

From the _Projects_ window select _Project Groups.._ menu option. The _Manage Groups_ window allows you to select the project group you want to switch to or to create a new one.

But what is a project group ?

It can be a single project (any NetBeans recognized project types like ant, maven, ...). If the project is hierarchical, NetBeans will also include all sub modules in the project group.

It can also be a directory with many different projects. Nice if you like to pack similar projects together (by customer, by type...).

Finally a project group can also be _free_: every project you will open after the project group creation will be automatically included in it.

If you do nothing, your are in a special **None** free project group.

Project groups are a nice feature but there is a weak point if we compare NetBeans with the other most popular IDEs: **IDEA** and **Eclipse**.

When NetBeans starts, the last used project group is opened again, with consequent project loading and scanning which is annoying if you wanted to work on a different project because you have to wait until complete project loading before switching to the desired project.

On the contrary Idea has a nice startup GUI which shows the list of the past projects in addition to the possibility to create or import a new one. Eclipse has less flexible but still efficient solution based on workspace selection. Both are before loading last opened projects so very _fast_.

The NetBeans solution is based on the following command line parameters:





  * `--open-group NAME`  

open a project group by name


  * `--close-group`  

close any open project group


  * `--list-groups`  

list available project groups



Invoking NetBeans with `--open-group PROJ1` will force NetBeans to **immediately** close the last opened project group and open the specified one (PROJ1 in this example).

For each project, on Linux and OS-X you can create a dedicated invocation script while on Windows you can create a dedicated icon with a dedicated command line. Tricky but it works.

Another alternative (actually the one I prefer) is to add the `--close-group' parameter to the _netbeans_default_options_ parameter in the netbeans_home/etc/netbeans.conf file.

The `--close-group' parameter force NetBeans to switch to the _None_ project group. Once inside NetBeans you can open the project group you like.

For more inside about this feature, have a look at the [Enhancement 168556](https://netbeans.org/bugzilla/show_bug.cgi?id=168556).

NetBeans startup commands are documented [here](http://wiki.netbeans.org/FaqStartupParameters).
