---
author: gualtierotesta
comments: true
layout: post
slug: using-maven-enforcer-plugin
title: Enforcing the Maven version using the Maven Enforcer Plugin
categories:
- java
tags:
- coding
- java
- maven
- plugins
- versions
- pom.xml
- super pom
- maven-enforcer-plugin
---

*Last update: 16 Dec 2018*

The [Maven Enforcer Plugin](https://maven.apache.org/enforcer/maven-enforcer-plugin ) let us define and, if required, enforce some environment conditions like the:

* the Java version
* the Maven version
* the operating system type (windows, linux…)

If an environment condition is *enforced*, the project build will **fail** if that condition is not respected.

In this post, we will focus on enforcing the Maven tool version but let’s begin with why we should enforce the Maven version.

Every Maven release has linked, in its [super pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Super_POM), a specific set of plugins versions; changing the Maven version will also change the plugins versions unless the plugin is specified in the build→ plugins or in the build → pluginManagement sections in the pom.xml files.

In the Maven build process, several key plugins are involved: clean, compile, assembly, jar, war, ear, surefire… A different plugin version can potentially bring to a different results.

If we are working in a team, with several developers with their own PC and local development environment, and/or if we are using a CI/CD pipeline, we can have:

1. Developer A use Maven version x to build the project
2. Developer B or the CI tool like Jenkins use Maven version y to build the project

The two build outputs could be different because the Maven versions x and y could use a different version of the plugins involved in the build (if not explicitly set in the pom.xml file).

If we want to avoid this “hidden” dependency between the Maven version and the build results we can:

1. Specify the version of the most common/important plugins in the pom.xml file
2. Enforce the Maven tool version to be used to build in the project; in our example we could say: everybody should use version y (or x).

How to enforce the Maven release ? By using the  Maven Enforcer Plugin adding the following lines in the build → plugins section of the pom.xml:

``` xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-enforcer-plugin</artifactId>
                <version>3.0.0-M2</version>
                <executions>
                    <execution>
                        <id>enforce-maven</id>
                        <goals>
                            <goal>enforce</goal>
                        </goals>
                        <configuration>
                            <rules>
                                <requireMavenVersion>
                                    <version>[3.5.4,)</version>
                                </requireMavenVersion>
                            </rules>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
</build>
```

In this example, the `requireMavenVersion` **rule** has been specified. Withinh this rule, the Maven release can be specified as:

`<version>3.6.0</version>` : only version 3.6.0 can be used

`<version>[3.5.4,)</version>` : minimum accepted version is 3.5.4; 3.5.4 and 3.6.0 are ok, 3.5.1 not ok

`<version>3.5</version>` : accepted versions are any 3.5.x

Another interesting rule is the `requirePluginVersions` which can help us to check a common Maven best practice: Maven plugins version should be explicitly defined in the pom.xml, to prevent build results variation when a different Maven tool release is used.

An example of the `requirePluginVersions` rule is the following:

``` xml
<requirePluginVersions>
    <message>Best Practice is to always define plugin versions!</message>
    <banLatest>true</banLatest>
    <banSnapshots>true</banSnapshots>
    <phases>[ERROR] clean,deploy,site</phases>
</requirePluginVersions>
```

which forces the best practice to always define the version of the Maven plugins used in the project. The build will fail if plugins version is not explicity defined or it is a snapshot (`banSnapshots` tag) or "latest" (`banLatest` tag) version.

See [here](https://maven.apache.org/enforcer/enforcer-rules/requirePluginVersions.html) for all details about this rule configuration.

More usages can be found on the [plugin site](https://maven.apache.org/enforcer/maven-enforcer-plugin/usage.html).
