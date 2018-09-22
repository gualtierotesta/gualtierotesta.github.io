---
author: gualtierotesta
comments: true
date: 2013-10-03 19:00:54+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2013/10/03/tutorial-using-mockito/
slug: tutorial-using-mockito
title: 'Tutorial: using Mockito'
wordpress_id: 157
categories:
- Java
- Mockito
- test
- tutorial
tags:
- assertion
- assertj
- fluent interface
- integration test
- java
- mockito
- pet clinic
- spring
- spring framework
- unit test
---

Mockito ([http://code.google.com/p/mockito/](http://code.google.com/p/mockito/)) is a Java mocking library which is very useful in unit testing but also to simplify and enhance the integration tests.


There are different situations where Mockito can be used and each situation requires a different approach and different Mockito usage.




In this tutorial I will show how I implement unit tests for middle (service, business logic) layer classes.




In the examples below I will use the Spring Framework demo project named PetClinic and available at [https://github.com/spring-projects/spring-petclinic](https://github.com/spring-projects/spring-petclinic) but the article contents does **not** depend on any Spring features.




To use Mockito I added the following dependency to the project pom.xml :



[code language="xml"]<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>1.9.5</version>
    <scope>test</scope>
</dependency>```


We will also use AssertJ (link):



[code language="xml"]<dependency>
    <groupId>org.assertj</groupId>
    <artifactId>assertj-core</artifactId>
    <version>1.3.0</version>
    <scope>test</scope>
</dependency>```


Inside the PetClinic demo there is the _ClinicService_ which is a Springs Service bean. The interface is _org.springframework.samples.petclinic.service.ClinicService_ while the implementing class is _org.springframework.samples.petclinic.service.ClinicServiceImpl_.




The _ClinicService_ has several dependencies to the application lower level, the DAO / Repository level:



[code language="java"]private PetRepository petRepository;
private VetRepository vetRepository;
private OwnerRepository ownerRepository;
private VisitRepository visitRepository;```


The _ClinicService_ is tested by _AbstractClinicServiceTests_ and its 3 different implementations (using 3 different DB access Spring solutions: direct JDBC, JPA and Spring Data).




Actually these tests are integration tests because the class under test (ClinicService) is using, during the test, its real dependencies (the repositories) and so it is not isolated.




By the way, PetClinic demo does not use the standard naming convention for the tests (at least for maven projects) which is the following:






	
  * _*Test.java_ or _Test*.java_ for unit tests (Surefire plugin)

	
  * _*IT.java_ or _IT*.java_ for integration tests (FailSafe plugin)




In PetClinic, all tests (unit and integration) are named _*Tests.java_ ("Tests" instead of "Test") so Surefire plugin has been customized as following:



[code language="xml"]<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.13</version>
    <configuration>
        <includes>
            <strong><include>**/*Tests.java</include></strong>
        </includes>
    </configuration>
</plugin>```


In this tutorial, I will follow their convention even if it is not what I usually do in my projects.




Let's unit test _ClinicService_ with the help of jUnit and Mockito.




First step is to create the test class and instantiate the _ClinicService_ implementation class:



[code language="java"]public class ClinicServiceImplTests {
    private ClinicServiceImpl sut;
}```


A couple of comments:






	
  * I use the implementing class and not the interface. Using the implementing class, let us access to the class protected methods (if any), not accessible using the interface.

	
  * The instance name of the class to be tested is usually _sut_ (which stays for "system under test") to help myself and other test code readers to easily identify the class we are testing. Cut (for "class under test") is also used (by others) but I prefer sut because it is more generic.




We need now to instantiate the _ClinicServiceImpl_ and, to avoid dependencies among tests, I use the jUnit before method



[code language="java"]@Before
public void setUp() throws Exception {
    sut = new ClinicServiceImpl(petRepository, vetRepository,
        ownerRepository, visitRepository);
}```


_ClinicServiceImpl_ costructor requires the 4 repositories: we will create mocked versions of the real ones (to break the dependency between _ClinicServiceImpl_ and the repositories).




To create the mocks:



[code language="java"]@Mock private OwnerRepository ownerRepository;
@Mock private PetRepository petRepository;
@Mock private VetRepository vetRepository;
@Mock private VisitRepository visitRepository;```


_@Mock_ is a Mockito annotation which need a jUnit TestRunner to be activated. We should add_ @RunWith(MockitoJUnitRunner.class)_ as class annotation.




After all these changes we have



[code language="java"]@RunWith(MockitoJUnitRunner.class)
public class ClinicServiceImplTests {
    private ClinicServiceImpl sut;
    @Mock private OwnerRepository ownerRepository;
    @Mock private PetRepository petRepository;
    @Mock private VetRepository vetRepository;
    @Mock private VisitRepository visitRepository;

    @Before
    public void setUp() throws Exception {
        sut = new ClinicServiceImpl(petRepository, vetRepository,
            ownerRepository, visitRepository);
    }
}```


One important comment to do is that we are NOT using any Spring features (like _@Autowire_ or _@ContextConfiguration_) in the test. We do not depend on Spring and we will also enjoy a faster test execution because Spring framework will not be loaded.




Spring allows us to use setter to inject repositories as alternative to constructor injection used in the demo. For example we could have in the implementation class something like the following:



[code language="java"]private OwnerRepository ownerRepository;
.... other fields
@Autowired
public void setOwnerRepository(OwnerRepository pOwnerRepository) {
    this.ownerRepository = pOwnerRepository;
}
..... other setters```


In this case, in the test setup method we will have:



[code language="java"]sut = new ClinicaServiceImpl();
sut.setOwnerRepository(ownerRepository);
.....other calls to sut setters```


We are ready now to test _ClinicServiceImpl_ (our sut) behaviour.




Let's start with a _ClinicServiceImpl_ method which is a good (even if very simple) example of a method which receives data from one of its dependencies (_ownerRepository_ in this case).




Method (from _ClinicServiceImpl_) is



[code language="java"]
public Owner findOwnerById(int id) throws DataAccessException {
    return ownerRepository.findById(id);
}```


Following my TDD habits, the test is:



[code language="java"]/**
* Pet owner can be found using its ID
*/
@Test
public void findPetOwnerById() {
    // given
    final int id = 123;
    Mockito.doReturn(createOwner()).when(ownerRepository).findById(id);
    // when
    final Owner res = sut.findOwnerById(id);
    // then
    assertThat(res).isNotNull();
}
private static Owner createOwner() {
    return new Owner();
}```


Comments:






	
  * the method comment helps to clarify the purpose of the test which should be also reported in the method name. Thankfully, there is no more need to use old jUnit 3 "test***" naming convention.

	
  * the test method body is divided in three sections:

	
    * _given_: all data (test fixture) preparation

	
    * _when_ : method to be checked invocation

	
    * _then_ : checks and assertions




	
  * createOwner() is a support private method to create a very basic Owner.

	
  * in the when section I usually name the result of the method (if not void) as "res" (result). Again this is to make test code clearer.

	
  * in the then section I usually uses AssertJ fluent assertion library. See [here ](http://gualtierotesta.wordpress.com/2013/08/09/using-assertj/)for more details about AssertJ library. We will see later how Assertj let us to be more efficient in our verification step.

	
  * findOwnerById() does not change the Owner instance returned by ownerRepository so there is no meaning to check Owner fields value. In other cases, I use more detailed assertions (like _assertThat(res.getId()).isEqualTo(123)_) than the _isNotNull()_.




The key element is here is the line:



[code language="java"]
Mockito.doReturn(createOwner()).when(ownerRepository).findById(id);```


which says:



dear Mockito, during the test execution, when someone (the findOwnerById method in our case) will invoke the ownerRepository.findById() method with int 123 as argument, please return the Owner instance created by createOwner()


Without this line,_ ownerRepository.findById_() will return null. This is the standard behavior for Mockito.




Specifying the int value (123) will let us to filter _ownerRepository.findById_() invocations:






	
  * ownerRepository.findById(123) with return a not null Owner

	
  * ownerRepository.findById(44) will return a null




If we cannot be so accurate about the argument (for example, when method argument is calculated by the method under test), we can use



[code language="java"]
Mockito.doReturn(createOwner()).when(ownerRepository).findById(Mockito.anyInt());```


to say Mockito to return a not null Owner regardless the findById parameter value.




Let's now test another method from _ClinicServiceImpl_ which send data to a dependency:



[code language="java"]public void savePet(Pet pet) throws DataAccessException {
    petRepository.save(pet);
}```


The test is :



[code language="java"]@Test
public void savePet() {
    // given
    final Pet pet = createPet();
    // when
    sut.savePet(pet);
    // then
    Mockito.verify(petRepository, Mockito.times(1)).save(pet);
}
private static Pet createPet() {
    Pet pet = new Pet();
    pet.setName("My little Pet");
    return pet;
}```


The structure is the same but here we use Mockito for a different purpose: to check if a method is invoked or not and how many times.




The line



[code language="java"]Mockito.verify(petRepository, Mockito.times(1)).save(pet);```


says:



Dear Mockito, please check during the test execution, that petRepository.save() will be invoked one time (no zero, no more than one) and with the argument pet (as it is created by the createPet() support method).


If the method petRepository.save() is not invoked or invoked more than one times or invoked with a different pet, verify will rise a test failure exception.




Comment: Mockito.times(1) is the default so we can write the same line as



[code language="java"]Mockito.verify(petRepository).save(pet);```


Do not be confused by the fact that _Mockito.verify_ is specified in the _then_ section, after the petRepository.save() invocation (in the _when_ section). Mockito always collect all methods invocation data. The verify istruction just compare what happened with what we expect.




Now we can move to a more complicated example but the ClinicService is too simply. Let me add to it the following method:



[code language="java"]@Override
@Transactional
public void adoptAPet(Owner owner, Pet pet) {
    if (pet != null) {
        owner.addPet(pet);
        ownerRepository.save(owner);
    }
}```


The method let an owner to adopt a pet.




Its test can be:



[code language="java"]/**
* Pet adoption with a real pet
*/
@Test
public void petAdoptionWithRealPet() {
    // given
    final Owner owner = createOwner();
    final Pet pet = createPet();
    final ArgumentCaptor<Owner> ownerCaptor = ArgumentCaptor.forClass(Owner.class);
    // when
    sut.adoptAPet(owner, pet);
    // then
    Mockito.verify(ownerRepository).save(ownerCaptor.capture());
    assertThat(ownerCaptor.getValue().getPets()).hasSize(1).contains(pet);
}```


We want now to find which is the argument of the ownerRepository.save() invocation inside the adoptAPet() body. In other words we want to intercept the call to ownerRepository.save() and get the value of its argument.




This is a three steps approach.




First step is to prepare a dedicated parameters captor (something that will capture the method argument) with:



[code language="java"]ArgumentCaptor<Owner> ownerCaptor = ArgumentCaptor.forClass(Owner.class);```


where Owner is the ownerRepository.save() argument type.




Second step is to ask verify to save the parameter value in our captor with the following line:



[code language="java"]Mockito.verify(ownerRepository).save(ownerCaptor.capture());```


Verify will not only check if the _ownerRepository.save()_ is invoked only once but also it will save its argument value (with the captor capture() method).




Final step is to get the captured argument value using the_ ownerCaptor.getValue()_ method (which in our case returns the Owner instance used to invoke _ownerRepository.save()_) and to assert its contents.




In the test I used AssertJ power to check if owner.getPets() collection has size 1 and contains the same pet I passed to the adoptAPet() (when section):



[code language="java"]assertThat(ownerCaptor.getValue().getPets()).hasSize(1).contains(pet);```


The _ArgumentCapture_ is a very powerful tool which let us to get internal data if used as method argument to mocked dependencies.




The _adoptAPet_() method handles the null case for the pet parameter so let's test this behavior:



[code language="java"]/**
* Pet adoption do nothing if pet is null
*/
@Test
public void petAdoptionWithNullPet() {
    // given
    final Pet pet = null;
    final Owner owner = createOwner();
    // when
    sut.adoptAPet(owner, pet);
    // then
    //Mockito.verify(ownerRepository, Mockito.never()).save(Mockito.any(Owner.class));
    // or
    Mockito.verifyZeroInteractions(ownerRepository);
}```


Here we want to be sure that _ownerRepository.save()_ is not invoked when the pet parameter is null.




Two different solutions are possible: we can verify that the _ownerRepository_ has not been used (which means none of its method has been used) with the following:



[code language="java"]Mockito.verifyZeroInteractions(ownerRepository);```


or verify that the _ownerRepository.save()_ is never invoked with the following:



[code language="java"]Mockito.verify(ownerRepository, Mockito.never()).save(Mockito.any(Owner.class));```


leaving the possibility that other _ownerRepository_ methods can be invoked. In this case, I can use _Mockito.any_(Owner.class) as save() parameter because here the save() argument value is not important.




This tutorial has shown different ways to use Mockito (and AssertJ) to create a unit test for a middle layer class. The _ClinicServiceImpl_ class was intrinsically testable and we could easily break its dependencies without code refactoring.




This is not always the case. In future articles I will show how I handle difficult to test classes and also how Mockito can help in integration tests.




Final comment is how I have used Mockito in the above examples: I did not use static imports to help you to identify Mockito instructions but please be aware that all instructions can be shortened. For example



[code language="java"]Mockito.verify(ownerRepository, Mockito.never()).save(Mockito.any(Owner.class));```


becomes



[code language="java"]verify(ownerRepository, never()).save(any(Owner.class));```


with the proper static imports. Ask your preferred IDE to help you.




All files I have modified/added to the PetClinic application can be found [here](https://dl.dropboxusercontent.com/u/27338103/blog/2013-10-03%20files.zip).
