---
author: gualtierotesta
comments: true
date: 2013-04-22 19:00:53+00:00
layout: post
slug: code-templates-in-netbeans
title: Code templates in NetBeans
wordpress_id: 19
categories:
- Java
- Netbeans
tags:
- apache-commons
- coding
- java
- netbeans
---

During software coding it is very common to use many times same instructions or group of instructions, even in the same file.

For example, in Java we frequently have lines like the following:

``` java
private static final String COD = "123";
```

The sequence `private static final String` is 27 chars long, not so short and prone to typing errors.

To help us to reduce typing effort and errors, most of the IDE supports the template concept: sequence of chars which the tool expand in a longer sequence char.

For examples in NetBeans I can type `psfs` and press TAB to have `private static final String`. Nice.

Another example is the `fori` template that creates a complete for loop.

The list of templates can be found on Options -> Editor -> Code Templates. It is worth to have a look at this list to identify most interesting templates.

[![Netbeans Code template window](/images/2013-04-22-code-templates-in-netbeans/fig1.png)

The available code templates can also be modified and expanded to cover code instructions and structures we mostly use in our projects.

In my project I added the followings:

``` text
abbreviation la
final ${stype type="java.util.List" default="List" editable="false"}&lt;${paramType default="String"}&gt; ${var newVarName default="list"} = new ${listType type="java.util.ArrayList" editable="false"}&lt;${paramType}&gt;(0);
${cursor}
```

it creates a line like

``` java
final List<String> list = new ArrayList<String>(0)
```

The template

``` text
abbreviation ve
${coltype type="org.apache.commons.lang.Validate" default="Validate" editable="false"}.notEmpty(${cursor});```
it creates following line (adding the correct import if necessary):
[code language="java"]
Validate.notEmpty(...);
```

Similarly
[code language="text"]
abbreviation vn
${coltype type="org.apache.commons.lang.Validate" default="Validate" editable="false"}.notNull(${cursor});```
it creates line with Validate.notNull(...) method call.

Note: I use the [Apache Commons Lang](http://commons.apache.org/proper/commons-lang/) Validate functions to validate public methods arguments.

More details on the code templates syntax can be found at [How_to_use_Code_Templates on NetBeans](http://wiki.netbeans.org/Java_EditorUsersGuide#How_to_use_Code_Templates)
