---
author: gualtierotesta
comments: true
date: 2015-06-14 10:00:52+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2015/06/14/tutorial-using-findbugs-with-maven/
slug: tutorial-using-findbugs-with-maven
title: 'Tutorial: using FindBugs with Maven'
wordpress_id: 371
categories:
- FindBugs
- Java
- Maven
- Tools
- tutorial
tags:
- code checks
- coding
- external plugin
- fbcontrib
- findbugs
- java
- maven
- null analysis
- quality checks
---

[FindBugs](http://findbugs.sourceforge.net/) can be executed using [Maven](https://maven.apache.org/) in two different modes: as stand-alone command or as part of the Maven site command.

These two modes require different settings in the project pom.xml but they are not incompatible to each other so we can use both of them.

[![info48](https://gualtierotesta.files.wordpress.com/2015/06/info48.png)](https://gualtierotesta.files.wordpress.com/2015/06/info48.png)
You can find complete Maven project [here](https://github.com/gualtierotesta/code-quality-gallery).


### Stand-alone mode


Let's start with the stand-alone mode which requires the following configuration in the pom.xml:

[code language="xml"]
<build>
    <plugins>       
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>findbugs-maven-plugin</artifactId>
            <version>3.0.1</version>
        </plugin>
         ... other build plugins ....
    </plugins>
</build>
```

Please note that the FindBugs plugin definition is inside build - plugins section of the pom file.

Giving the following command:

[code language="bash"]
mvn findbugs:findbugs
```

FindBugs is run against our project with the following log in the console output:

    
    [INFO] --- findbugs-maven-plugin:3.0.1:findbugs (default-cli) @ CodeQualityGallery ---
    [INFO] Fork Value is true
         [java] Warnings generated: 1
    [INFO] Done FindBugs Analysis....


[![warning48](https://gualtierotesta.files.wordpress.com/2015/06/warning48.png)](https://gualtierotesta.files.wordpress.com/2015/06/warning48.png)
FindBugs analyze the compiled files (*.class). If the project is not compiled or just cleaned, FindBugs will report nothing, without error messages. So take care to build your project before running FindBugs.

A warnings number is reported on the console. Warnings are possible bugs found by the FindBugs detectors.

The warnings details are included in a report created in the file target/findbugsXml.xml which is in XML format and so not really readable.

A better way to examine the warnings is to use the FindBugs native GUI with the command:

[code language="bash"]
mvn findbugs:gui
```

The FindBugs window shows the bugs on the left, the (read-only) code on the right and the bug explanation below.

[![FindBugs GUI](https://gualtierotesta.files.wordpress.com/2015/06/2015-06-08a.png?w=1024)](https://gualtierotesta.files.wordpress.com/2015/06/2015-06-08a.png)

You can filter bugs for bug rank from the most critical ("scariest") to trivial and even, saving project status, comparing project evolution (which means how may bugs has been eliminated/introduced after previous FindBugs check).

[![warning48](https://gualtierotesta.files.wordpress.com/2015/06/warning48.png)](https://gualtierotesta.files.wordpress.com/2015/06/warning48.png)All main IDEs (Eclipse, NetBeans, IntelliJ) FindBugs plugins show similar views and they can also let you modify the code so I don't suggest to use FindBugs native GUI unless you have to create exclusion files (see below) which is easier with the native GUI: select a bug, filter it out and export bug filters.




### Site mode


Maven site command is used to generate a project "site", which is a collection of information on the project nicely reported in HTML pages. You can configure which information is included in the site report and, of course, you can add FindBugs analysis results.

Maven site requires a "reporting section" in the pom file.

[code language="xml"]
<reporting>
    <plugins>       
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>findbugs-maven-plugin</artifactId>
            <version>3.0.1</version>
        </plugin>
         ... other site plugins ....
    </plugins>
</reporting>
```

Giving the following command (note: clean and install options are in to assure that project is compiled and all tests run):

[code language="bash"]
mvn clean install site
```

on the console output we will see the following:

    
    .....
    [INFO] --- maven-site-plugin:3.3:site (default-site) @ CodeQualityGallery ---
    [INFO] configuring report plugin org.apache.maven.plugins:maven-jxr-plugin:2.5
    [INFO] configuring report plugin org.apache.maven.plugins:maven-project-info-reports-plugin:2.8
    [INFO] configuring report plugin org.codehaus.mojo:findbugs-maven-plugin:3.0.1
    [INFO] Fork Value is true
         [java] Warnings generated: 2
    [INFO] Done FindBugs Analysis....
    [INFO] configuring report plugin org.apache.maven.plugins:maven-pmd-plugin:3.4
    [WARNING] No project URL defined - decoration links will not be relativized!
    [INFO] Rendering site with org.apache.maven.skins:maven-default-skin:jar:1.0 skin.
    [INFO] Skipped "Source Xref" report, file "xref/index.html" already exists for the English version.
    [INFO] Generating "Source Xref" report    --- maven-jxr-plugin:2.5
    [INFO] Generating "Dependency Convergence" report    --- maven-project-info-reports-plugin:2.8
    [INFO] Generating "Dependency Information" report    --- maven-project-info-reports-plugin:2.8
    [INFO] Generating "About" report    --- maven-project-info-reports-plugin:2.8
    [INFO] Generating "Plugin Management" report    --- maven-project-info-reports-plugin:2.8
    [INFO] Generating "Project Plugins" report    --- maven-project-info-reports-plugin:2.8
    [INFO] Generating "Project Team" report    --- maven-project-info-reports-plugin:2.8
    [INFO] Generating "Project Summary" report    --- maven-project-info-reports-plugin:2.8
    [INFO] Generating "FindBugs" report    --- findbugs-maven-plugin:3.0.1
    [INFO] Generating "PMD" report    --- maven-pmd-plugin:3.4
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    .....


The "site" is created in the target/site dir. Opening the file target/site/index.html with the browser, we will see something like the following:

[![2015-06-08b](https://gualtierotesta.files.wordpress.com/2015/06/2015-06-08b1.png)](https://gualtierotesta.files.wordpress.com/2015/06/2015-06-08b1.png)In the Project Reports section you will find the FindBugs report like the following:

[![2015-06-08c](https://gualtierotesta.files.wordpress.com/2015/06/2015-06-08c1.png?w=1024)](https://gualtierotesta.files.wordpress.com/2015/06/2015-06-08c1.png)   The report is interesting because it give us the complete picture of our project in the summary section but also the list of the files which have FindBugs detected issues with all details including the priority (i.e. how dangerous could be the problem).


### Configuration options


Regardless the used mode, the FindBugs plugin can be configured to modify and tune its behaviour. All below configurations should be placed in the configuration section of the plugin:

[code language="xml"]
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>findbugs-maven-plugin</artifactId>
    <version>3.0.1</version>
    <configuration>
        .....put here
    </configuration>
</plugin>
```

[![warning48](https://gualtierotesta.files.wordpress.com/2015/06/warning48.png)](https://gualtierotesta.files.wordpress.com/2015/06/warning48.png)
Configurations in the build and reporting sections are independent so we can have two different settings in the two modes, stand-alone and site.

Best practice is to use a light and fast configuration on the standalone mode, in order to run quick checks during development and an high effort, production quality configuration for the site mode.

Let's examine the most important configuration options:

**DEBUG**: With <debug>true</debug> option, FindBugs will show what is doing during analysis. Useful to fine tune or checks the settings.

**EFFORT**: with  <effort>Max</effort> we can increase the time FindBugs is allowed to use to analyze the code. More effort means more accurate analysis but, of course, FindBugs run slower.

**EXTERNAL PLUGINS**: FindBugs can be extended with additional detectors. See my [post](https://gualtierotesta.wordpress.com/2015/06/07/findbugs-plugins/) on the topic. The  <pluginList>plugin1[, plugin2...]</pluginList> configuration option list the plugins to be used. See the sample project for a real example.

** EXCLUSIONS**: If you need to disable a detector or to disable the analysis on same classes, an exclude list can be specified with the <excludeFilterFile> path_to_exclude_file </excludeFilterFile> option. File syntax is documented [here](http://findbugs.sourceforge.net/manual/filter.html). An easy way to create this file is by using the FindBugs GUI which has an export filter function. See the sample project for a real example.


