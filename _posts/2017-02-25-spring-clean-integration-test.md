---
layout: post
title:  "Spring: Running clean integration tests"
date:   2017-02-25 19:28:37
---

Any test class that requires a Spring context configuration file or class to be loaded in, is defined as an *integration test* in my opinion, not a *unit test*. The requirement/reliance/dependency on an external resource as a criteria for successful execution of a test qualifies it to be an integration test. The external resource in question maybe infrastructure (database, message bus, server) or even 3rd party libraries like [IoC][] containers that are initialized and terminated within runtime. These external resources outside of the test code would need to work in harmony to ensure a successful result of your test code.

Integration tests should always require the external resources to be in the same initial state for each test execution. This allows for tests to run in isolation under identical conditions without afffecting the outcome of other tests. For example when a test requires interaction with a database where it writes data, it should rollback any changes it performs in the database after its execution before the next test is executed. The next test may also need to utilize the database and any changes in the database from the previous test are a change in state that can result in future tests failing due to a false negative scenario, where the changed state impacts the outcome of the test.

If this is the case with an external resource like a database, then it should also be true of all external resources, which includes Spring context configuration. Some Spring contexts configure elements which can modify state. Examples of this are modifying bean properties; temporal aspects such as repetition frequency or interval expiration for invocation of a method due to thresholds being met; Changes in the metadata of a Spring Integration message channel (sequence number header value for instance).  

Test classes denote Spring usage with the following annotations above the class declaration:

`@RunWith(SpringJUnit4ClassRunner.class)`  
`@ContextConfiguration("classpath:META-INF/spring/spring-test-context.xml")`
{% highlight java %}
public class IntegrationTest 
{
...
}
{% endhighlight %}

This leads to the context file being loaded and used for all tests in the `IntegrationTest` class. However **the context is loaded only once and used for all tests in the lifetime of the jvm process**. Therefore any state changes created by one test is carried forward onto the next. This is equivalent to modifications of a database in each test carried forward to the next without rollback. This will lead to inaccurate, inconsistent, unpredictable and unrepeatable outcomes for tests.

These context modifications that cause a change in state is known as making the context ***dirty***. To ensure the same initial state exists for each test, the solution is to reload the context for *each test*. For this purpose, Spring has an annotation type called [DirtiesContext][]. There are several ways to use this annotation. I usually declare the annotation at class level above the class declaration along with the other annotations. The specific annotation setting I use is: 

```@DirtiesContext(classMode=ClassMode.AFTER_EACH_TEST_METHOD)```

The [AFTER_EACH_TEST_METHOD][afterEachTestMethod] mode is defined as:

>
The associated ApplicationContext will be marked as dirty after each test method in the class.

This means that when the context is marked as dirty after the test, **the current context will close and a new context instance will be loaded before the execution of the next test**. This is essentially a context refresh, achieving the same initial state for each test execution, allowing isolation. State changes in one test will no longer be carried forward to the next and test outcomes will be accurate, consistent and repeatable. 

As good practice and on principle, this annotation should be added to all Spring test classes. The consequences are that the overall test time will be longer, but I feel this is acceptable in order to ensure the overall correctness of your software.

[IoC]: https://en.wikipedia.org/wiki/Inversion_of_control
[DirtiesContext]: http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/annotation/DirtiesContext.html 
[afterEachTestMethod]: http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/annotation/DirtiesContext.ClassMode.html#AFTER_EACH_TEST_METHOD