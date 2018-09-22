---
author: gualtierotesta
comments: true
date: 2013-10-14 19:00:58+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/10/14/pmd-and-maven/
slug: pmd-and-maven
title: PMD and Maven
wordpress_id: 197
categories:
- Java
- Netbeans
- Tools
tags:
- code checks
- coding
- easypmd
- java
- maven
- netbeans
- plugin
- pmd
- quality checks
---

After explaining how to use [PMD ](http://pmd.sourceforge.net/)with NetBeans in [this ](http://gualtierotesta.wordpress.com/2013/10/07/pmd-in-netbeans-the-easypmd-plugin/)post, let me describe how we can directly run PMD using Maven, without the help of an IDE plugin.

We can have two different approaches:



	
  * invoking the PMD plugin for Maven.

	
  * using Maven site feature.


First approach. Add the following lines to the project pom (in the **build/plugins** section):

[code language="xml"]<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-pmd-plugin</artifactId>
  <version>3.0.1</version>
</plugin>```

(Of course, maven-pmd-plugin version can vary. See [here ](http://maven.apache.org/plugins/maven-pmd-plugin/)for last updates).

PMD can be invoked from the command line with the following:

    
    mvn pmd:pmd


which will create a file named _target/pmd.xml. _XML is the default output format but it can be changed with the _format_ configuration option.

The file contains all violations detected by PMD in all _source_ files found in the project. Test files are not checked in the standard configuration.

By default, only rules from the following rulesets are checked by PMD: java-basic, java-imports and java-unusedcode. We will see later how to control which rules and rulesets should be checked.

In the second approach, PMD is invoked during the Maven site command which is a reporting command. In this case the PMD plugin definition should be placed inside the **reporting/plugins** section of the project pom.

[code language="xml"]<reporting>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-jxr-plugin</artifactId>
      <version>2.3</version>
    </plugin>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-pmd-plugin</artifactId>
      <version>3.0.1</version>
      <configuration>
        <targetJdk>1.6</targetJdk>
        <rulesets>
          <ruleset>pmd_rules.xml</ruleset>
        </rulesets>
      </configuration>
    </plugin>
  </plugins>
</reporting>```

Note: the jxr plugin is useful for source code analysis. The reporting section usually has more plugins that the two shown in the examples.

Here the PMD plugin has been configured with a couple of important parameters: the Java version (_targetJdk_) and the set of rules and ruleset to be used (_rulesets/ruleset_).

My usual approach (described in the PMD in NetBeans post) is to define a custom list of rules and rulesets to be checked by PMD. With the Maven plugin we can use the same XML file used to generate the jar file for [EasyPMD ](http://gianlucacosta.info/software/easypmd/)plugin.

We will have the **same** check results from NetBeans + EasyPMD and from Maven. Very good.

I usually save the custom rules file in the root of the project with a name like "pmd_rules.xml". Not so smart name but easy to understand. Of course, it is versioned together with the project files.

Being versioned and saved in the repository let us share exactly the **same** PMD setup among all development team members. In the pom we have specified a relative path so we will not have problem to access it from different project installation paths.

If the project is multi-module with a parent pom as top dir and the modules in subdirs, I change the configuration as following:

[code language="xml"]<rulesets>
  <ruleset><strong>..\</strong>pmd_rules.xml</ruleset>
</rulesets>```

so running mvn site (or mvn pmd:pmd) on a module will work. Only mvn site on parent pom will not work (but this is less important).

The two approaches I described are not exclusive and they can both be used in the same project. Just remember to have the same configuration in the two pom locations (build/plugins and reporting/plugins).


