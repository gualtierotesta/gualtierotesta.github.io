---
author: gualtierotesta
comments: true
date: 2014-01-29 21:58:03+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2014/01/29/tutorial-using-mockito-in-an-integration-test/
slug: tutorial-using-mockito-in-an-integration-test
title: 'Tutorial: Using Mockito in an integration test'
wordpress_id: 229
categories:
- Java
- Mockito
- test
- Tools
- tutorial
tags:
- assertion
- assertj
- controller
- integration test
- java
- mock
- mocking
- mockito
- plugin
- spring framework
- unit test
---

Even if Mockito (see my previous [post](http://gualtierotesta.wordpress.com/2013/10/03/tutorial-using-mockito/)) usually helps us to cut all dependencies in our unit tests, it can be useful also in the integration tests where we often want to have all real applications layers.




Let's assume to have the following Spring controller:



[code language="java"]
@Controller
public class LandingPageController {
  protected static final String ADMIN_MAIN = "adminMain";
  protected static final String PROVIDER_MAIN = "providerMain";
  protected static final String USER_MAIN = "userMain";
  private UserManager userManager;

  @Autowired
  public void setUserManager(final UserManager pUserManager) {
    this.userManager = pUserManager;
  }

  @RequestMapping(value = "/users")
  public ModelAndView landingPage(final HttpServletRequest request) {
    ModelAndView mav;
    final User user = userManager.getCurrentUser();
    if (user.isAdministrator()) {
      mav = new ModelAndView(new RedirectView(ADMIN_MAIN));
    } else if (utente.isProvider()) {
      mav = new ModelAndView(new RedirectView(PROVIDER_MAIN));
    } else {
      mav = new ModelAndView(new RedirectView(USER_MAIN));
    }
    return mav;
  }
}```


The controller redirects the currently logged user to a proper landing page, depending on its role.




The controller could be tested with a unit test, mocking the UserManager and asserting the return values but I decided for a different test approach.




In this case, I want to check if Spring configuration is correct and if the controller method is properly invoked when we access the “/users” url.




In other words, I need an integration test which usually requires that all dependencies are real. In this case, the UserManager is already tested in other tests so it would be better to use an UserManager mock to reduce test effort and execution time.




To create an integration test for a Spring based application, I usually use the following abstract class which defines the Spring configuration to be used for test:



[code language="java"]
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {
  "classpath:/spring/dispatcher-servlet.xml",
  "classpath:/spring/applicationContext.xml",
  "classpath:/spring/security.xml"})
@TransactionConfiguration(transactionManager = "txManager", defaultRollback = true)
@Transactional
public abstract class IntegrationTestCase {
  @Autowired
  protected ApplicationContext applicationContext;
}```


 Few comments:






	
  * we use the Spring specific jUnit runner (SpringJUnit4ClassRunner) to have Spring environment during the test

	
  * with the @ContextConfiguration annotation we define all Spring configuration files which are relevant for the tests




The integration test for our controller is the following:



[code language="java"]
public class LandingPageControllerIT extends IntegrationTestCase {
```


we extend the class from the IntegrationTestCase class so we can inherit all Spring configuration. Note: class (and file) name ends with IT which is the maven default for integration test files. See failsafe plugin documentation for more details.




Important: we cannot use the usual Mockito runner because we are using the Spring one, so we will not use Mockito annotations.



[code language="java"]
private static final String PAGE = "/users";
private MockHttpServletRequest request;
private MockHttpServletResponse response;
private HandlerAdapter handlerAdapter;
private LandingPageController sut;
private UserManager userManager;```


MockHttpServletRequest and MockHttpServletResponse are mocking classes available in the Spring library while the HandlerAdapter is the real one because we need in the setUp method which is the following:



[code language="java"]
@Before
public void setUp() {
  sut = new LandingPageController();
  userManager = Mockito.mock(UserManager.class);
  sut.setUserManager(userManager);

  request = new MockHttpServletRequest();
  request.setRequestURI(PAGE);
  response = new MockHttpServletResponse();

  handlerAdapter = applicationContext.getBean("methodHandler", HandlerAdapter.class);
  assertThat(handlerAdapter.supports(sut)).isTrue();
}```


We create the UserManager mock and inject it in the sut (System Under Test) instance using the setter.




We then create the request and response instances and set the requested page.




Finally we find the handler adapter bean as defined in the Spring application configuration. The assertThat (which is from AssertJ library) checks if the handler bean is configured to handle the controller under test. If not, there is no meaning to continue the tests (first piece of our integration test).




The first test is to check what happens when the logged user is an administrator:



[code language="java"]
@Test
public void userIsAdministrator() {
  // given
  doReturn(createAdministrator()).when(userManager).getCurrentUser();
  // when
  final ModelAndView mav = handlerAdapter.handle(request, response, sut);
  // then
  assertModelAndView(mav, LandingPageController.ADMIN_MAIN);
}```

In the given section, we defined the return value for the mocked UserManager.getCurrentUser method, with the help of a private method (“createAdministrator()), see below.

In the when section, we invoke the handler which is the same method invoked by Spring when a real HTTP request reaches our application. This is the real integration test.

Finally, in the then section, we check the results, using a utility method (see below).

Similar tests (not shown) for the other types of user.

At the end we have the utility methods, to check the ModelAndView instance and to create a dummy user.

[code language="java"]
private static void assertModelAndView(final ModelAndView pMav, final String pLandingPage) {
  assertThat(pMav.getView()).isNotNull().isInstanceOf(RedirectView.class);
  final RedirectView view = (RedirectView) pMav.getView();
  assertThat(view.getUrl()).isEqualTo(pLandingPage);
}

private static User createAdministrator() {
  final User ris = new User();
  ris.setRole(“ADM”);
  return ris;
}
}```


Conclusion: Mockito is very useful even if without runner and annotations.
