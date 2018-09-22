---
author: gualtierotesta
comments: true
date: 2013-08-09 19:00:29+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/08/09/using-assertj/
slug: using-assertj
title: Using AssertJ
wordpress_id: 130
categories:
- Java
- test
tags:
- assertion
- assertj
- coding
- fest-assert
- fluent interface
- hamcrest
- html page development
- java
- junit
---

AssertJ ([https://github.com/joel-costigliola/assertj-core](https://github.com/joel-costigliola/assertj-core)) is an assertion library which can be used to improve our test units code.

I've found two significant benefits by using this library compared to other solutions I used (jUnit basic assertions, Hamcrest and Fest Assert 1.x).

First, its fluent interface (same as Fest Assert) let me express in a more compact and readable way the assertions.

For example, assuming list is a java.util.List, the following jUnit asserts

[code language="java"]asserNotNull(list);
assertEquals(3, list.size());```

can be replaced by

[code language="java"]assertThat(list).isNotNull().hasSize(3);```

which I believe it is a more readable and compact code.

Second benefit is the wide range of checks we can do without need for conversion methods or additional libraries. For example, assuming _birthday_ and _aDate_ are java.util.Date instances:

[code language="java"]
// check if birthday is today
assertThat(birthday).isToday();
// check if aDate is Aug 8th, 2013
assertThat(aDate).isEqualTo(“2013-08-08”);
```

or, assuming aMap is a java.util.Map,

[code language="java"]
// check if aMap has all 3 keys
assertThat(aMap).containsKeys(key1, key2, key3);
```

Again we can have more readable and compact code but also we can be more specific and correct in our assertions.

In the AssertJ web site (hosted on GitHub) you can find the usual API documentation ([http://joel-costigliola.github.io/assertj/core/api/index.html](http://joel-costigliola.github.io/assertj/core/api/index.html)) but also a good source of examples in [https://github.com/joel-costigliola/assertj-examples/](https://github.com/joel-costigliola/assertj-examples/) where you can get syntax and examples for your assertions.

Maven users can add AssertJ as test dependency

    
    <dependency>
        <groupId>org.assertj</groupId>
        <artifactId>assertj-core</artifactId>
        <version>1.3.0</version> 
        <scope>test</scope>
    </dependency>
