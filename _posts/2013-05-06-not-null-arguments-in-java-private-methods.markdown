---
author: gualtierotesta
comments: true
date: 2013-05-06 19:00:55+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/05/06/not-null-arguments-in-java-private-methods/
slug: not-null-arguments-in-java-private-methods
title: Not null arguments in Java private methods
wordpress_id: 33
categories:
- IntelliJ
- Java
- Netbeans
tags:
- '@Nonnull'
- coding
- java
- netbeans
- null analysis
---

After the article on[ not null arguments on public methods](http://gualtierotesta.wordpress.com/2013/04/30/not-null-arguments-in-java-public-methods/), let's talk about class **private** methods.

Checking the public method arguments is necessary to guarantee that methods callers are respecting the class API contract.

On the contrary, private methods are not visible to outside code so there is no need the detect contracts violation.

So, can we skip to check nullity of our private methods arguments ?

The answer is **no**.

Private methods are called by other methods from the same class so **we** (not class users) are responsible to guarantee that the methods are invoked correctly.

If we pass a null argument to a private method which need a non null argument, this is a bug in our code and we should fix it.

Joshua Bloch, in his famous Effective Java book, suggest to check private methods arguments using assertion.

This is correct approach because assertion are usually enabled during development and test, while we are debugging our code, and disabled in production code.

Let me make small example:
[code language="java"]
private void metSimple(final String arg) {
    System.out.println(arg.length());
}
```
The String argument _arg_ of this method cannot be null. Best approach is to check the argument at the begin of the method body:
[code language="java"]private void metChecked(final String arg) {
    assert arg != null;
    System.out.println(arg.length());
}```
During the devolpment of the class (with assertions enabled) which uses the method, the assert will check nullity of the argument. Assertion error will help us to debug and improve our code.

To have better support from the IDE, we can also use Nonnull annotation (see [this ](http://gualtierotesta.wordpress.com/2013/04/30/not-null-arguments-in-java-public-methods/)article for more details) like as follows:
[code language="java"]
private void metAnnotatedChecked(@javax.annotation.Nonnull final String arg) {
    assert arg != null;
    System.out.println(arg.length());
}```
This is the **preferred** solution: assert check run at execution time while Nonnull annotation guides the IDE to detect improper method calls.

Using NetBeans 7.3 we have following situation:
[![NetBeans 7.3](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-06-fig1.jpg?w=300)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-06-fig1.jpg)

NetBeans detects wrong method calls at lines 14 and 15 thanks to annotations while it does not find problems on lines 12 and 13.

NetBeans is also reporting an "Unnecessary test for null - the expression is never null") on the assert line.This is a false message because the annotation cannot guarantee that _arg_ method is never null; I have opened a[ bug report](https://netbeans.org/bugzilla/show_bug.cgi?id=225970).

**Update**: this has been fixed on NetBeans 7.4

Using IntelliJ IDEA 12.1 Community Edition we have the following:

[![IntelliJ](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-06-fig2.jpg?w=300)](http://gualtierotesta.files.wordpress.com/2013/05/2013-05-06-fig2.jpg)

Curiously the two IDEs have the same behavior:



	
  * same correct warnings (lines 14 and 15)

	
  * same missed warnings (lines 12 and 13)

	
  * same "bug" on line 32


Most probably there is some code sharing between the tools.

Eclipse users: please look at the comments on the my article on[ public](http://gualtierotesta.wordpress.com/2013/04/30/not-null-arguments-in-java-public-methods/) methods.
