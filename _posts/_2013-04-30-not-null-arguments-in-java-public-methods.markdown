---
author: gualtierotesta
comments: true
date: 2013-04-30 19:00:02+00:00
layout: post
slug: not-null-arguments-in-java-public-methods
title: Not null arguments in Java public methods
categories:
- IntelliJ
- Java
- Netbeans
tags:
- '@Nonnull'
- coding
- intellij
- java
- netbeans
- null analysis
---

As Java programmer, one of my references is Joshua Bloch's Effective Java. In this book, the theme 38 says we should always check the validity of the arguments on each method.

No excuse.

For example, let's have a very dummy method which prints on the standard output the length of the `String` passed as argument:

``` java
public void metSimple(String arg) {
    System.out.println(arg.length());
}
```

if we call this method with a null String, we will get a Null Pointer Exception (NPE) at run-time.

Accordingly to Bloch's book, we should add a proper check to validate the argument like as follow:

``` java
public void metChecked(final String arg) {
    if (arg == null) {
        throw new NullPointerException("Argument arg is null");
    }
    System.out.println(arg.length());
}
```

The check will assure us that the body of the method (here just a simple `System.out.println` but in real code will be more complex) can work on the argument `arg` without problems, knowing that the string `arg` is not null.

Note: we should not use assert instructions to check public methods arguments. Reason is that assert can be disabled. There is also an old but still valid Java article on [assertions](http://docs.oracle.com/javase/1.4.2/docs/guide/lang/assert.html) on this.

The nullity check is done at run-time so we need to work on testing side to have reasonable confidence that null condition (i.e the NPE) will not really happen in front of our customers.

Can we also have some support during coding? I mean, from our fabulous IDE?

Yes, but we need to help them, adding a "not null" annotation on the argument. There are several of these annotations but the standard is `@javax.annotation.Nonnull`.

Our method becomes:

``` java
public void metAnnotatedChecked(@Nonnull final String arg) {
    if (arg == null) {
        throw new NullPointerException("Argument arg is null");
    }
    System.out.println(arg.length());
}
```

The `@Nonnull` annotation is from [jsr305](http://www.mvnrepository.com/artifact/com.google.code.findbugs/jsr305) library. Maven users can add it in the `pom.xml` file the following dependency:

``` xml
<dependency>
    <groupId>com.google.code.findbugs</groupId>
    <artifactId>jsr305</artifactId>;
    <version>2.0.1</version>;
</dependency>
```

The `@Nonnull` annotation simply says: dear method users, do not try to invoke it with a null as argument.

**Important**: the `@Nonnull` annotation is simply an annotation, a kind of hint for the tools. It is not a run-time check and it does not enforce or guarantee that nobody can call the method with a null argument. So our "if arg == null" check should stay there.

The IDEs use these annotations (there are also others like CheckForNull) to perform additional checks (named "null analysis") during coding. Not at run-time. These checks complements the run-time checks.

Let's how most popular IDEs use these annotations. To check their behavior I created a calling class as follows:

``` java
public static void main(final String[] args) {
    check1();
    check2();
}
private static void check1() {
    final Args1 args1 = new Args1();
    args1.metSimple(null);
    args1.metChecked(null);
    args1.metAnnotated(null);
    args1.metAnnotatedChecked(null);
}
private static void check2() {
    final Args1 args1 = new Args1();
    final String str1 = null;
    args1.metSimple(str1);
    args1.metChecked(str1);
    args1.metAnnotated(str1);
    args1.metAnnotatedChecked(str1);
}
```

The two checks method (check1() and check2() ) call the methods with the String argument passing a null value (check1) or a null String (check2).

At run-time, this code will produce 8 null pointer exceptions (if executed one by one).

Let's start with NetBeans (7.3).

<a href="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig1.png"><img src="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig1.png?w=300" alt="2013-04-30_fig1" height="277" class="alignnone size-medium wp-image-26" width="300" /></a>

As you can see from the picture, NetBeans checks and report method calls with null arguments but only if the arguments have been annotated with @Nonnull. It does not report not annotated methods, even if the nullity is very obvious.

So NetBeans marks 4 (out of 8) null pointer exceptions “without executing the code”. Here it is the benefit: during coding I will immediately see sure and potential null pointer exceptions.

The NetBeans check is implemented by the following Java Hint

<a href="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig2.png"><img src="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig2.png?w=300" alt="2013-04-30_fig2" height="154" class="alignnone size-medium wp-image-27" width="300"/></a>

If we move to IntelliJ IDEA (12.1.1 Community Edition), the situation is very similar:

<a href="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig3.png"><img src="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig3.png?w=300" alt="2013-04-30_fig3" height="285" class="alignnone size-medium wp-image-28" width="300"/></a>

IntelliJ reports same methods as NetBeans except the last one metAnnotatedCheck in check2. It is not clear to me if this is a bug or not.

IntelliJ check is performed by the following Inspection

<a href="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig4.png"><img src="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig4.png?w=300" alt="2013-04-30_fig4" height="222" class="alignnone size-medium wp-image-29" width="300"/></a>

And Eclipse?

Unfortunately there is no such much to say, at least for the moment. In the latest release (4.2 Juno), a (preliminary) support for null analysis has been introduced:

<a href="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig5.png"><img src="http://gualtierotesta.files.wordpress.com/2013/04/2013-04-30_fig5.png?w=300" alt="2013-04-30_fig5" height="251" class="alignnone size-medium wp-image-30" width="300"/></a>

Even if I enable all checks, no errors/warnings (0 out of 8) are reported by the tool on our main class. On [Eclipse Null Analysis wiki](http://wiki.eclipse.org/JDT_Core/Null_Analysis/Beta) it seems it is still a work in progress. Let's check again when it will be officially available.

Final remark on something not covered by this article: there are tools like Findbugs which can also perform (sometimes in a better way) null-analysis. These tools have usually a plugin that let user to use them from inside the IDE. In NetBeans there is also a deeper integration with FindBugs but there are still some issues (I have reported a couple of bugs).
