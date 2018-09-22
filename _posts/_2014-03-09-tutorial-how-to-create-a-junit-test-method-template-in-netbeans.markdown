---
author: gualtierotesta
comments: true
date: 2014-03-09 20:00:43+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2014/03/09/tutorial-how-to-create-a-junit-test-method-template-in-netbeans/
slug: tutorial-how-to-create-a-junit-test-method-template-in-netbeans
title: 'Tutorial: how to create a JUnit test method template in NetBeans'
wordpress_id: 267
categories:
- Java
- Netbeans
- test
- tutorial
tags:
- code template
- coding
- junit
- netbeans
- template
- unit test
---

NetBeans has a very nice feature: **code templates**.

Let me show how I use it to add a new JUnit test method in the test classes.

Go to Tools → Options → Editor → Code Templates and add the following template:

Abbreviation: **te** (this is only my proposal)

Expanded text:

[code language="java"]@${baseType type="org.junit.Test" default="Test" editable="false"}
public void ${cursor}() {
    // given

    // when

    // then

}```

[![2014-03-05_pic01](http://gualtierotesta.files.wordpress.com/2014/03/2014-03-05_pic01.jpg)](http://gualtierotesta.files.wordpress.com/2014/03/2014-03-05_pic01.jpg)Then go to a test class, type “te” (our template abbreviation) and press the TAB key.

See what's happen:

[![2014-03-05_pic02](http://gualtierotesta.files.wordpress.com/2014/03/2014-03-05_pic021.jpg?w=1024)](http://gualtierotesta.files.wordpress.com/2014/03/2014-03-05_pic021.jpg)[![2014-03-05_pic03](http://gualtierotesta.files.wordpress.com/2014/03/2014-03-05_pic031.jpg?w=1024)](http://gualtierotesta.files.wordpress.com/2014/03/2014-03-05_pic031.jpg)

First, the org.junit.Test include has been added (if not already in). This is thanks to the ${baseType} instruction we added in the template.

Second, the prompt is waiting for you for the method name. See ${cursor} instruction in the template.

Just write the name and we have complete test method template in our class.

[![2014-03-05_pic04](http://gualtierotesta.files.wordpress.com/2014/03/2014-03-05_pic041.jpg)](http://gualtierotesta.files.wordpress.com/2014/03/2014-03-05_pic041.jpg)

Of course, the template can be adjusted to your needs and habits.

Benefits:



	
  * we code faster

	
  * we have more uniform code style


NetBeans code template syntax is (partially) documented [here](http://wiki.netbeans.org/Java_EditorUsersGuide#How_to_use_Code_Templates).
