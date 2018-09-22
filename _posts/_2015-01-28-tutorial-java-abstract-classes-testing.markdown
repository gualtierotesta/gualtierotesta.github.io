---
author: gualtierotesta
comments: true
date: 2015-01-28 20:42:55+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2015/01/28/tutorial-java-abstract-classes-testing/
slug: tutorial-java-abstract-classes-testing
title: 'Tutorial: Java abstract classes testing'
wordpress_id: 335
categories:
- Java
- Mockito
- test
- tutorial
tags:
- abstract class
- java
- junit
- mock
- mockito
- partial mock
- powermock
- spy
- unit test
- unit testing
---

In Java, abstract classes are used to define the interface of the class, with a list of (abstract) methods to be implemented by the extending class; basically what it is also possible to have with the “real” interfaces, the one defined with the interface instruction.

One significant difference between abstract classes and interfaces is that abstract classes can also have fields and non abstract methods which contain logic.

Abstract classes with code (in non abstract methods) are common in some design patterns implementations and in class hierarchy based frameworks. This is a typical solution to share code inside a class family.

Of course this code should be tested but abstract classes cannot be instantiated.

In this tutorial, we will analyze several cases and all possible alternative solutions to test abstract classes with non abstract methods. Let's start with the libraries we will use on our tests (all Java 6 source level):



	
  * [jUnit](http://junit.org/), but it should be easy to migrate the test classes to [TestNG](http://testng.org/)

	
  * [Mockito](http://mockito.org/)

	
  * [PowerMock](https://code.google.com/p/powermock/)


Few important remarks:

	
  1. Some solutions are based on Mockito version 1.10.12 or above but these versions are not compatible with the latest PowerMock version (1.6.1, see [here](https://github.com/jayway/powermock/blob/master/changelog.txt)).
So in the project we will use two different libraries sets:

	
    1. Classic: jUnit 4.12, Mockito 1.10.8, PowerMock 1.6.1

	
    2. Latest: jUnit 4.12, Mockito 1.10.19 (no PowerMock)




	
  2. All examples are based on the “classic” combination except few ones marked with a special comment. In the project pom.xml, both dependencies sets are defined but one is commented out. Please note that, due to the combinations differences, some tests cannot be compiled in all combinations.

	
  3. Complete project can be found at [https://github.com/gualtierotesta/test-solutions
](https://github.com/gualtierotesta/test-solutions)

	
  4. The examples are not real world code. The code has been kept at the minimum in order to better show the adopted testing strategy.

	
  5. A basic knowledge about Java unit testing and jUnit framework is needed for a more complete understanding of the examples. Mockito and PowerMock knowledge is not required.

	
  6. Have also a look at the [Test Glossary](https://gualtierotesta.wordpress.com/testing-glossary/) for the meaning of several test terms used in this post.


**CASE 1: independent method**

This is the simplest case: the abstract class contains, in addition to abstract methods, a public non abstract method (named "methodToBeTested" in our examples) with no internal dependencies: it does not call any other method in its class.

[code language="java"]
public abstract class AbstractCase1 {
    public abstract int myAbstractMethod();
    public String methodToBeTested() {
        return "CASE-1";
    }
}
```

We want to test the logic of the method "methodToBeTested()". We have three possible testing solutions:

**Solution 1.A Using a concrete class**

We create a concrete class which extends the abstract class and test the method from this class.

Our test class body:

[code language="java"]
     private class ConcreteCase1 extends AbstractCase1 {
        @Override
        public int myAbstractMethod() {
            return 0;  //Dummy implementation
        }
    }

    @Test
    public void testUsingConcreteClass() {
        // given
        ConcreteCase1 sut = new ConcreteCase1();
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("CASE-1", result);
    }
```

Explanation:



	
  * Line 1: we define a concrete implementation of the abstract class where all abstract methods have a dummy implementation; they are not used so we don't care.

	
  * Line 11: we create an instance named "sut" (system under test) of the concrete class. Through this instance we have free access to the method we want to test


The only drawback of this solution is the need to create the concrete class and created a dummy implementation for all abstract methods.

The given, when and then comments are used to separated different test phases: preparation, execution and results analysis. See also [http://martinfowler.com/bliki/GivenWhenThen.html](http://martinfowler.com/bliki/GivenWhenThen.html)

**Solution 1.B Using a Mockito spy (Mockito 1.10.12+ required)**

We can use Mockito to create a spy instance of the abstract class. A spy is a class instance with standard behavior but that can be controlled by Mockito; for example we can ask Mockito to change the normal method return value, bypassing the return value calculated by the method body. See also the Testing Glossary.

In our case, we use a Mockito spy only because it let us create an abstract class instance, which is normally not possible. This is not a typical Mockito spy use case.

[code language="java"]
    @Test
    public void testUsingMockitoSpy() {
        // given
        AbstractCase1 sut = Mockito.spy(AbstractCase1.class);
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("CASE-1", result);
    }
```

Explanation:



	
  * line 4: create the spy instance using Mockito.spy(class) method.

	
  * line 6: invoke the method to be tested


The main drawback of this solution is that Mockito.spy() will invoke the abstract class constructor during spy instance creation. In some cases the constructor uses external dependencies which can be an obstacle to our unit test executions. These kind of dependencies are usually called “test impediments”.

**Solution 1.C Using Mockito mock**

If you cannot use latest Mockito version or the abstract class constructor contains some test impediments, we can use Mockito to create a mock.

In general mocking is used to "empty" a class from its logic: a mocked instance does not have code insides the methods. In our case, we create a mock and then ask Mockito to restore the code inside the method we want to test.

This is not a very clean solution but it works.

[code language="java"]
    @Test
    public void testUsingMockitoMock() {
        // given
        AbstractCase1 sut = Mockito.mock(AbstractCase1.class);
        Mockito.doCallRealMethod().when(sut).methodToBeTested();
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals(&quot;CASE-1&quot;, result);
    }
```

Explanation:



	
  * line 4: create the mocked instance using Mockito.mock(class) method.

	
  * line 5: we ask Mockito to use real (=original) code when method is invoked


**CASE 2: independent method with test impediment**

In this second use case, the method we want to test calls a private method which contains a test impediment. We cannot simply execute the method to be tested because first we need to disable the test impediment method.

[code language="java"]  public abstract class AbstractCase2 {

    public abstract int myAbstractMethod();

    public String methodToBeTested() {
        return prefix() + "2";
    }

    // Test impediment
    private String prefix() {
        return "CASE-";
    }
}```

In our example, the method to be tested calls the private method “prefix()”. Note: the method body in the example is NOT a test impediment as it just returns a string. In the real world, the method performs some time and/or resource consuming tasks which should be avoided in our unit tests.
We want to “remove” prefix() standard behavior but to do this we cannot create a concrete class because private methods cannot be overridden. We cannot even use Mockito because it cannot control private (and, by the way, also static ) methods so we will use its stronger companion: PoweMock.

**Solution 2.A Using PowerMock**
The test class:

[code language="java"]
@RunWith(PowerMockRunner.class)
@PrepareForTest(AbstractCase2.class)
public class AbstractCase2Test {

    @Test
    public void testWithPowerMock() throws Exception {
        // given
        AbstractCase2 sut = PowerMockito.mock(AbstractCase2.class);
        PowerMockito.doCallRealMethod().when(sut).methodToBeTested();
        PowerMockito.doReturn("TESTCASE-").when(sut, "prefix");
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("TESTCASE-2", result);
    }
}```

Explanation:



	
  * line 1: the @RunWith annotation defines PowerMock as the runner for the test

	
  * line 2: the @PrepareForTest(class) annotation asks PowerMock to prepare the class for later processing

	
  * line 8: we create a mock using PowerMockito.mock() which, in turns, call the Mockito mock() method

	
  * line 9: as we did for the first use case, we ask PowerMock to re-enable the body of the method to be tested

	
  * line 10: here it is the key element of this test: we ask PowerMock to stub the private method return value so we can test methodToBeTested() without test impediments. Mockito alone cannot do this stub. Please note that the name of the private method, prefix, is passed to PowerMock as string because it is not accessible from outside. PowerMock will use reflection to find it.


**CASE 3: method which access instance fields**
Abstract classes can have an internal state implemented with class fields. The value of the fields can be significant for the test so we need a way to control them in order to define the initial test context (the “test fixture”).
If the field is public (uh, not very nice OO solution) or protected, it can be freely accessed (and modified) from our test class. But if it is private, we have few options to control it.
Let's start with the example class:

[code language="java"]  public abstract class AbstractCase3 {

    protected boolean type = false;
    private int count;

    public abstract int myAbstractMethod();

    public String methodToBeTested() {
        String res;
        count += 1;
        if (count > 10) {
            res = "Too many times";
        } else {
            if (type) {
                res = "Type is true";
            } else {
                res = "Type is false";
            }
        }
        return res;
    }
}```

Two class fields, type and count, control the methodToBeTested() behavior. Note: this is just an example so forgive the meaningless logic.

**Solution 3.A Using Mockito mock**
This solution is identical to solution 1.C: we create a mock and then later we ask Mockito to re-enable the method body.

[code language="java"]
    @Test
    public void testTrueTypeWithCountLessThen10() {
        // given
        AbstractCase3 sut = Mockito.mock(AbstractCase3.class);
        Mockito.doCallRealMethod().when(sut).methodToBeTested();
        sut.type = true;
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("Type is true", result);
    } ```

Explanation:



	
  * line 4: create the mocked instance using Mockito.mock(class) method.

	
  * line 5: we ask Mockito to use real (=original) code when method is invoked

	
  * line 6: we fix the type field value to be true so we can test the method in this specific condition


As shown, we cannot control the count field value with this kind of solution.

**Solution 3.B Using Mockito mock and a setter**
Like the solution 3.A but we add a count field setter to the abstract class (not to the test class !) so we can control its value from the test class.

In the abstract class code we add the following lines:

[code language="java"]
    // Only for test
    void setCount(int pAmount) {
        this.count = pAmount;
    }```

This solution is identical to solution 1.C: we create a mock and then later we ask Mockito to re-enable the method body.

[code language="java"]
    @Test
    public void testCountGreaterThen10WithSetter() {
        // given
        AbstractCase3 sut = Mockito.mock(AbstractCase3.class);
        Mockito.doCallRealMethod().when(sut).methodToBeTested();
        Mockito.doCallRealMethod().when(sut).setCount(Mockito.anyInt());
        sut.setCount(15);
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("Too many times", result);
    } ```

Explanation:



	
  * line 4: create the mocked instance using Mockito.mock(class) method.

	
  * line 5 and 6: we ask Mockito to use real (=original) code when method is invoked and also for the setter. Note the Mockito.anyInt() call which defines for which value (all, in this specific case) the real method body should be used

	
  * line 7: we call the count setter with a value, 15, above the condition defined inside the method (10) so we can test this code path


Of course we could also change type field value like we did in the previous solution.

**Solution 3.C Using PowerMockito**
If we cannot add the setter, for example because abstract class source code cannot be touched, we can use PowerMock to change class fields.

[code language="java"]   
    @Test
    public void testWithPowerMock() {
        // given
        AbstractCase3 sut = PowerMockito.mock(AbstractCase3.class);
        PowerMockito.doCallRealMethod().when(sut).methodToBeTested();
        Whitebox.setInternalState(sut, "count", 15);
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("Too many times", result);
    }```

Explanation:



	
  * line 6: PowerMock Whitebox class uses reflection to control internal class contents. In our case we change to 15 the value of the count field (second argument, which should be noted it is a string).


Using Whitebox is not very common and it should be considered only for very untestable code only.

**CASE 4: method which call abstract methods**
This is the typical case, for example, of the Template Method design pattern (http://en.wikipedia.org/wiki/Template_method_pattern) but also other design patterns and frameworks use this solution. The non abstract method defines the global flow while the abstract method can be implemented in different ways to customize the behavior.

[code language="java"]   public abstract class AbstractCase4 {

    public abstract int myAbstractMethod();

    public String methodToBeTested() {
        int val = myAbstractMethod();
        return "CASE-" + val;
    }
}```

At line 6, the abstract method is invoked.

To test this code, we have three different solutions: extending the abstract class, using Mockito to create a mock or a spy.

**Solution 4.A Using a concrete class **
We can extend the abstract class, giving a convenient (for testing purposes) behavior to the abstract method:

[code language="java"]   
    private class ConcreteCase4 extends AbstractCase4 {
        @Override
        public int myAbstractMethod() {
            return 4;  //Dummy implementation
        }
    }

    @Test
    public void testUsingConcreteClass() {
        // given
        AbstractCase4Test.ConcreteCase4 sut = new AbstractCase4Test.ConcreteCase4();
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("CASE-4", result);
    }```

Explanation:



	
  * line 1: ConcreteClass4 extends the AbstractClass4Test and gives a convenient implementation of the abstract methods.


**Solution 4.B Using a mock with Mockito**
We create a mock and we stub the abstract method:

[code language="java"]
    @Test
    public void testUsingMockitoMock() {
        // given
        AbstractCase4 sut = Mockito.mock(AbstractCase4.class);
        Mockito.doCallRealMethod().when(sut).methodToBeTested();
        Mockito.doReturn(4).when(sut).myAbstractMethod();
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("CASE-4", result);
    }```

Explanation:



	
  * line 6: the abstract method is stubbed with the return value we prefer for the test. Please note that, thanks to Mockito, we are stubbing a method which does not have any implementation (because it is abstract).


**Solution 4.C Using a spy with Mockito (Mockito 1.10.12+ required)**
Very similar to the previous use case:

[code language="java"]   
  @Test
  public void testUsingMockitoSpy() {
        // given
        AbstractCase4 sut = Mockito.spy(AbstractCase4.class);
        Mockito.doReturn(4).when(sut).myAbstractMethod();
        // when
        String result = sut.methodToBeTested();
        // then
        Assert.assertEquals("CASE-4", result);
}```

Explanation:



	
  * line 5: the abstract method is stubbed with the return value we prefer for the test. Please note that, thanks to Mockito, we are stubbing a method which does not have any implementation (because it is abstract).


**CONCLUSIONS
**

Abstract classes code can present a not so short list of use cases. Each of them can be tested using different techniques and different test support libraries.

In this tutorial we went through many examples which hopefully cover many real worlds cases. If not, just let me know so I can extend this post.


