---
layout: post
title:  "Spring: Running clean integration tests"
date:   2017-02-20 20:33:00
published: false
---

Any test class that requires a Spring configuration file or class to be loaded in, is defined as an *integration test* in my opinion and not a *unit test*. The requirement/reliance/dependency on an external resource as a criteria for successful execution of a test qualifies it to be an integration test. The external resource in question maybe infrastrucutre (database, message bus, server) or even 3rd party libraries like IoC containers that are initialized and terminated within runtime. These external resources outside of the test code would need to work in harmony to ensure a successful result of your test code.

Integration tests always require the external resources to be in the same initial state during each test execution. This allows for tests to run in isolation under identical conditions without afffecting the outcome of other tests. For example when a test requires interaction with a database where it writes data, it should rollback any changes it performs in the database after its execution before the next test is executed. The next test may also need to utilize the database and any changes in the database from the previous test are a change in state that can result in future tests failing due to a false negative scenario, where the changed state impacts the beahviour of the test. 


```@RunWith(SpringJUnit4ClassRunner.class)```  
```@ContextConfiguration("classpath:META-INF/spring/spring-test-context.xml")```
{% highlight java %}
public class Test 
{
...
}
{% endhighlight %}

```@DirtiesContext(classMode=ClassMode.AFTER_EACH_TEST_METHOD)```
