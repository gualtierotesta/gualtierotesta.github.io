---
author: gualtierotesta
comments: true
date: 2014-03-03 20:00:46+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2014/03/03/tutorial-using-lombok-to-reduce-boilerplate-code-in-java/
slug: tutorial-using-lombok-to-reduce-boilerplate-code-in-java
title: 'Tutorial: using Lombok to reduce boilerplate code in Java'
wordpress_id: 257
categories:
- Java
- Tools
- tutorial
tags:
- annotations
- boilerplate
- coding
- equals
- findbugs
- Getter
- hashCode
- java
- loggers
- logging
- lombok
- pmd
- project lombok
- Setter
- toString
---

Lombok (http://projectlombok.org) is a very useful Java annotation library which help us to:



	
  * reduce boilerplate code

	
  * code faster

	
  * avoid stupid errors like forgetting the getter and setter for a new added field.


Lombok can create getter, setters, toString, constructor, equals and hashCode methods automatically. You just need to write the class and add the fields.

Lombok provides several annotations (see http://projectlombok.org/features/index.html for the complete list) but, in my projects, I usually use few of them and, in this tutorial I will show which and why.

To better show Lombok benefits, let's start with a very simple (Maven based) project. You can find complete project source code [here. ](https://dl.dropboxusercontent.com/u/27338103/blog/2014-03-03.zip)

We should first define Lombok Maven dependency in the pom.xml file:

[code language="xml"] <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.12.4</version>
    <scope>provided</scope>
</dependency>```

**Important**: scope should be _provided_ because we do not want Lombok jar file to be included in our build file.

Let's now add to a project a very basic class without using any Lombok annotations.

[code language="java"]

public class WithoutLombokAnnotations {
    private boolean flag;
    private final int number;
    private final String text;
    private List<String> strList;

    public WithoutLombokAnnotations(int number, String text) {
        this.number = number;
        this.text = text;
    }

    public boolean isFlag() {
        return flag;
    }

    public void setFlag(boolean flag) {
        this.flag = flag;
    }

    public List<String> getStrList() {
        if (strList == null) {
            strList = new ArrayList<>(128);
        }
        return Collections.unmodifiableList(strList);
    }

    public void setStrList(List<String> strList) {
        this.strList = strList;
    }

    public int getNumber() {
        return number;
    }

    public String getText() {
        return text;
    }

    @Override
    public int hashCode() {
        int hash = 7;
        hash = 11 * hash + this.number;
        hash = 11 * hash + Objects.hashCode(this.text);
        return hash;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == null) {
            return false;
        }
        if (getClass() != obj.getClass()) {
            return false;
        }
        final WithoutLombokAnnotations other = (WithoutLombokAnnotations) obj;
        if (this.number != other.number) {
            return false;
        }
        if (!Objects.equals(this.text, other.text)) {
            return false;
        }
        return true;
    }

    @Override
    public String toString() {
        return "WithoutLombokAnnotations{" + "flag=" + flag + ", number=" + number + ",
            text=" + text + '}';
    }
}```

Comments on the above code:



	
  * The class has four fields, two of them are final so we need a constructor.

	
  * The flag field is a boolean so the getter is named isFlag() and not getFlag()

	
  * The strList getter method initialize the list if necessary

	
  * The strList is not included in the toString method

	
  * Equals and hashCode are calculated on number and text fields only.


A lot of boilerplate code for a very simple class.

Let's reduce it with the help of Lombok annotations.


###### GETTER and SETTER


First step: the getters and setters (http://projectlombok.org/features/GetterSetter.html). In our example, all of them are very simple and standard except the strList getter. We can use Lombok's @Getter and @Setter annotations to remove all standard getters and all setters from our code and have Lombok to generate them during compilation.

Our class code becames:

[code language="java"]@lombok.Getter
@lombok.Setter
public class GettersAndSetters {
    private boolean flag;
    private final int number;
    private final String text;
    private List<String> strList;

    public GettersAndSetters(int number, String text) {
        this.number = number;
        this.text = text;
    }

    public List<String> getStrList() {
        if (strList == null) {
            strList = new ArrayList<>(128);
        }
        return Collections.unmodifiableList(strList);
    }

    @Override
    public int hashCode() {
        int hash = 7;
        hash = 11 * hash + this.number;
        hash = 11 * hash + Objects.hashCode(this.text);
        return hash;
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == null) {
            return false;
        }
        if (getClass() != obj.getClass()) {
            return false;
        }
        final GettersAndSetters other = (GettersAndSetters) obj;
        if (this.number != other.number) {
            return false;
        }
        if (!Objects.equals(this.text, other.text)) {
            return false;
        }
        return true;
    }

    @Override
    public String toString() {
        return "WithoutLombokAnnotations{" + "flag=" + flag + ", number=" + number + ",
            text=" + text + '}';
    }
}```

Great. Same functionality and 18 lines of code removed. We left in the code the strList getter because it has non standard functionality.

By using Lombok's @Getter and @Setter annotation at class level we ask Lombok to generate getter and setter for all _non-static_ fields. Of course final fields will not have setters.

@Getter and @Setter annotations can be placed at class level (like in the example) and at field level.

I usually prefer to use Lombok annotations at class level for two reasons: to not forget to add proper annotations when I add new fields in the class and to avoid to clutter class code with too many annotations (at fields level; see for example when you are using Lombok in JPA entity classes).




###### CONSTRUCTORS


Second step: the constructor (http://projectlombok.org/features/Constructor.html). By adding the @lombok.RequiredArgsConstructor annotation at class level, we can remove the 4 lines of the constructor.

Lombok has three different annotations for the constructors:



	
  * @RequiredArgsConstructor, the one we used in the example, generates a constructor for all final fields, with parameter order same as field order

	
  * @NoArgsConstructor creates an empty constructor.

	
  * @AllArgsConstructor creates a constructor for all fields





###### HASHCODE and EQUALS


Third step: the hashCode and equals methods (http://projectlombok.org/features/EqualsAndHashCode.html). In the example we have created these methods using the number and text fields.

By adding the @lombok.EqualsAndHashCode(of = {"number", "text"}) annotation we can remove Equals and HashCode methods, going down by other 26 lines of code. And Lombok also generates the canEqual method. See here (http://www.artima.com/lejava/articles/equality.html) for the reasons.

**Important**: never use @lombok.EqualsAndHashCode without specifying the fields. Always use the _of_ parameter (or the opposite _exclude_ parameter). Without parameters, @lombok.EqualsAndHashCode will use all fields, possibly creating heavy equals and hashCode methods.




###### TOSTRING


Last step is to use @lombok.ToString (http://projectlombok.org/features/ToString.html) to implement the toString method. While having a toString method is highly suggested (for ex. for debugging and analysis purposes), my usual approach is to avoid collections and arrays fields in the toString method to avoid cluttering logs and messages windows with too long text lines.

After all our changes, the original class is now:

[code language="java"]@lombok.Getter
@lombok.Setter
@lombok.RequiredArgsConstructor
@lombok.EqualsAndHashCode(of = {"number", "text"})
@lombok.ToString(exclude = "strList")
public class EveryThing {
    private boolean flag;
    private final int number;
    private final String text;
    private List<String> strList;

    public List<String> getStrList() {
        if (strList == null) {
            strList = new ArrayList<>(128);
        }
        return Collections.unmodifiableList(strList);
    }
}```

Very short and with just the relevant code shown: the fields and the custom getter. No boilerplate code. Exactly what we were looking for.


###### LOGGING


Another quite useful Lombok annotation is related to logging (http://projectlombok.org/features/Log.html): Lombok can create a Logger field, named _log_, in our class. There are six variations of the annotation depending on the logging framework (java, apache, log4j, slf4j,,,).

The logging annotation does not reduce the amount of code in our class (one line for the annotation vs. one line for the manual logger definition) but it can help us to guarantee that we are using the same logging framework among all project classes. In some projects with large teams, I've found different logging frameworks used simply because different programmers were using IDE auto-complete functions in a different way.

Having something like

[code language="java"]@Slf4J
public class ….```

help us to use the same logger on all the class files.


###### @DATA


The @lombok.Data annotation (http://projectlombok.org/features/Data.html) is a short version for @Getter @Setter @EqualsAndHashCode, @ToString and @RequiredArgsConstructor.

At first glance it looks very convenient (one annotation instead of five) but, unfortunately, we cannot specify the fields for @EqualsAndHashCode and @ToString so it is not a good idea to use the @Data annotation unless the class is really a very simple POJO, without complex fields.

Moreover @Data does not generated automatically a no args constructor, required by JavaBeans and XML conversions so a @NoArgsConstructor annotation is also needed.


###### SUGGESTIONS


Lombok is very useful to reduce boilerplate code but it is important to keep in mind what Lombok is doing on our code to avoid useless and heavy generated code.

My personal suggestions:



	
  1. Always specify the fields you want in @EqualsAndHashCode or, in another words, never use the default @EqualsAndHashCode behaviour (all fields). Very important if the class is a JPA entity, where generated ID field should not be used for equality.

	
  2. Always exclude heavy fields (collections, arrays) in @EqualsAndHashCode and @ToString unless it is really necessary.

	
  3. Use @NoArgsConstructor when an empty constructor is needed (like for XML conversions and in JavaBeans)

	
  4. Never use Lombok @Data. As mentioned before, with the @Data annotation we cannot specify the fields for @EqualsAndHashCode and @ToString (see previous suggestions).

	
  5. On JPA entities:
@EqualsAndHashCode: exclude generated @Id fields (persistence equality constraint: a generated ID field is not always defined during entity life-cycle.)
@ToString: exclude other JPA entities and relationship to reduce method weight and avoid unexpected persistence problems like lazy initialization exceptions.





###### FINDBUGS and PMD


FindBugs works well with Lombok because it analyses the compiled code, so after Lombok.

On the contrary PMD, which analyses the source code, does not work well. I suggest to add a @SuppressWarnings("PMD") annotation at class level to tell PMD to completely skip the class. See here (http://pmd.sourceforge.net/pmd-5.1.0/suppressing.html) for more details.




###### DELOMBOK


Lombok provides a “delombok” (http://projectlombok.org/features/delombok.html) function so we can look at the code is really compiled, after Lombok processing.

We should open a terminal window, go to the project root dir and run the following command:

[code language="bash"]java -jar <path_to_lombok.jar> delombok src -d src-del```

where



	
  * _<path_to_lombok.jar>_ is the path to the lombok jar file. Maven users can find it at <maven_repository>/org/projectlombok/lombok/<version>/lombok-<version>.jar

	
  * _src_ is the name of source dir (Maven default)

	
  * _src-de_l is a name of the dir where we want delombok to put all files.


Our delombok-ed file is

[code language="java"]public class ToString {
    private boolean flag;
    private final int number;
    private final String text;
    private List<String> strList;

    public List<String> getStrList() {
        if (strList == null) {
            strList = new ArrayList<>(128);
        }
        return Collections.unmodifiableList(strList);
    }

    @java.lang.SuppressWarnings("all")
    public boolean isFlag() {
        return this.flag;
    }

    @java.lang.SuppressWarnings("all")
    public int getNumber() {
        return this.number;
    }

    @java.lang.SuppressWarnings("all")
    public String getText() {
        return this.text;
    }

    @java.lang.SuppressWarnings("all")
    public void setFlag(final boolean flag) {
        this.flag = flag;
    }

    @java.lang.SuppressWarnings("all")
    public void setStrList(final List<String> strList) {
        this.strList = strList;
    }

    @java.beans.ConstructorProperties({"number", "text"})
    @java.lang.SuppressWarnings("all")
    public ToString(final int number, final String text) {
        this.number = number;
        this.text = text;
    }

    @java.lang.Override
    @java.lang.SuppressWarnings("all")
    public boolean equals(final java.lang.Object o) {
        if (o == this) return true;
        if (!(o instanceof ToString)) return false;
        final ToString other = (ToString)o;
        if (!other.canEqual((java.lang.Object)this)) return false;
        if (this.getNumber() != other.getNumber()) return false;
        final java.lang.Object this$text = this.getText();
        final java.lang.Object other$text = other.getText();
        if (this$text == null ? other$text != null : !this$text.equals(other$text)) return false;
        return true;
    }

    @java.lang.SuppressWarnings("all")
    public boolean canEqual(final java.lang.Object other) {
        return other instanceof ToString;
    }

    @java.lang.Override
    @java.lang.SuppressWarnings("all")
    public int hashCode() {
        final int PRIME = 277;
        int result = 1;
        result = result * PRIME + this.getNumber();
        final java.lang.Object $text = this.getText();
        result = result * PRIME + ($text == null ? 0 : $text.hashCode());
        return result;
    }

    @java.lang.Override
    @java.lang.SuppressWarnings("all")
    public java.lang.String toString() {
        return "ToString(flag=" + this.isFlag() + ", number=" + this.getNumber() + ", text=" +
            this.getText() + ")";
    }
}```
