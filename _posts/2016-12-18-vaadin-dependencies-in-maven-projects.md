---
author: gualtierotesta
comments: true
date: 2016-12-18 19:00:39+00:00
layout: post
slug: vaadin-dependencies-in-maven-projects
title: Vaadin 7 dependencies in Maven projects
wordpress_id: 658
categories:
- java
tags:
- atmosphere
- coding
- dependency
- gwt
- java
- maven
- plugin
- vaadin
- vaadin compiler
---

*Last update: 17 Sep 2018*

The [Vaadin framework](https://vaadin.com) has several dependencies but not all of them should be included in our war/ear artifacts.

The following table shows all Vaadin **version 7.6/7.7** main modules and their meaning and usage

| Module | Description and usage |
| ---    | --- |
| server | This is the core of the framework. It has the following (transitive) dependencies: _vaadin-shared_ and _vaadin-sass-compiler_ |
| themes | Compiled version of the standard Vaadin themes |
| client-compiled | Compiled version of the standard Vaadin widgets set |
| client | Vaadin and GWT classes for widgets |
| client-compiler | Widgets compiler based on [GWT Google Web Toolkit](http://www.gwtproject.org/) |
| push | Optional module. It includes the support for push protocols (server to client) thanks to the [Atmosphere framework](https://github.com/Atmosphere/atmosphere)|
| shared | Common modules code. It is included as dependency in the _server_ module |
| sass-compiler | SASS to CSS compiler, used at build time and at run-time ("on-the-fly" compilation). It is included as dependency in the _server_ module |

Depending on the project requirements, the above modules should be included or not as project dependencies. We can identify two possible scenarios:

1. Project without a custom widget set. It can have a custom theme
1. Project with a custom widget set

In the first case (_without_ a custom widget set) we need the following modules:

* server
* themes
* push (optional)
* client-compiled

while, if we have a custom widget set, we need to compile the widgets so the dependencies become:

* server
* themes
* push (optional)
* client (for build only)
* client-compiler (for build only)

**Note**: the compiled custom widgets are included in our artifact

The following table summarizes the Maven dependencies:

| Module | ArtifactId | Scope | Required?|
| --- | --- | --- | --- |
|server|vaadin-server|compile|yes|
|themes|vaadin-themes|compile|yes|
|client-compiled|vaadin-client-compiled|runtime|only if the project does _not_ use custom widget set|
|client|vaadin-client|provided|only with custom widget set|
|client-compiler|vaadin-client-compiler|provided|only with custom widget sett. See also note below.|
|push|vaadin-push|compile|optional|
|shared|vaadin-shared|-|vaadin-server dependency. No need to be specified in the pom.xml|
|sass-compiler|vaadin-sass-compiler|-|vaadin-server dependency. No need to be specified in the pom.xml|

**Note**: the `vaadin-client-compiler` dependency is automatically included in the classpath by the Vaadin Maven plugin (`vaadin-maven-plugin`) when the custom widgets set should be compiled.
