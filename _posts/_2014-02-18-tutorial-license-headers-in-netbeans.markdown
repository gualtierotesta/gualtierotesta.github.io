---
author: gualtierotesta
comments: true
date: 2014-02-18 20:13:23+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2014/02/18/tutorial-license-headers-in-netbeans/
slug: tutorial-license-headers-in-netbeans
title: 'Tutorial: license headers in NetBeans'
wordpress_id: 239
categories:
- Netbeans
- tutorial
tags:
- copyright
- java
- license
- license header
- netbeans
---

NetBeans has special support for the license headers we usually add at the begin of the source files for copyright messages and references to license agreement.

In the project properties we can find a dedicated section to license headers:

[![2014-02-18_pic01](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic01.jpg)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic01.jpg)The "Use Global license" options let us choose among popular license header: Apache, GPL, MIT, NetBeans but in this tutorial we will create a custom license header.

First step is to create a file for our project license header. Let's create a text file on the root of the project which will contain the text of the license header.

[![2014-02-18_pic02](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic02.jpg)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic02.jpg)

[![2014-02-18_pic03](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic03.jpg)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic03.jpg)

In this example, the file is named _mylicense.txt_ and it is placed in the root of the project.

Now we open the mylicense.txt file to add some contents.

[![2014-02-18_pic04](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic04.jpg?w=1024)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic04.jpg)

The text is free but it should formatted as a comment (in my case I used the Java block comment: /* */ ).

Important: actually the syntax of the license file can be more complex than in our example because it is processed by a tool during the inclusion process. I will post a new tutorial on this topic.

Save the file and close the editor window.

Now we return to the project properties window to specify the path of our project specific license header:

[![2014-02-18_pic05](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic05.jpg)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic05.jpg)

I used the variable _${project.basedir}_ which is the symbolic path to the project root dir. The window shows the preview of the license header.

Now, when we create a new file (in the example a new Java Class file), we have our template header in place:

[![2014-02-18_pic06](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic06.jpg?w=1024)](http://gualtierotesta.files.wordpress.com/2014/02/2014-02-18_pic06.jpg)

For the moment, there is no option in NetBeans to add the template header to an already existing file but I filled an [enhancement request](https://netbeans.org/bugzilla/show_bug.cgi?id=242068) on this.
