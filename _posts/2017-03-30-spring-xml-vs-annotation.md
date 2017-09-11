---
layout: post
title:  "Spring: XML vs Annotation configuration - why XML is still relevant"
date:   2017-03-30 20:38:00
---

Spring context configuration has shifted to be more annotation and code based than XML in recent years. The expectation is for annotations to be the main form of Spring configuration and replace XML entirely. I have seen blog posts that advocate this, however there are differences between XML and annotations that are not addressed as of yet which still merit the use of XML. I base this on a particular experience, which is an edge case example but also exposes a gap in using annotations that undermines its identity as an [IoC][] configuration mechanism. 

[Inversion of Control][IoC] at its core is a paradigm shift in defining application behaviour, component relationships and object lifecycle management from compile time of source code to having it defined in runtime i.e. the object graph is built up in runtime instead of compile time. **XML configuration upholds this paradigm but annotation configuration does not. Annotation configuration is still compile time dependent, whereas XML is only runtime dependent.**

The example I have to illustrate this is through the configuration of a [Solace JMS message bus][solaceMessageBus] connection. Reading through the [Solace documentation][solaceDocument] on setting up Solace connectivity using Spring, on page 10 it defines all the beans required to create in the XML based configuration file:

{% highlight xml %}
<bean id="solaceJndiTemplate" class="org.springframework.jndi.JndiTemplate" lazy-init="default" autowire="default">
	<property name="environment">
		<map>
			<entry key="java.naming.provider.url" value="smf://___IP:PORT___" />
			<entry key="java.naming.factory.initial" value="com.solacesystems.jndi.SolJNDIInitialContextFactory" />
			<entry key="java.naming.security.principal" value="spring_user@Solace_Spring_VPN" />
			<entry key="java.naming.security.credentials" value="spring_password" />
		</map>
	</property>
</bean>

<bean id="solaceConnectionFactory" class="org.springframework.jndi.JndiObjectFactoryBean" lazy-init="default" autowire="default">
	<property name="jndiTemplate" ref="solaceJndiTemplate" />
	<property name="jndiName" value="JNDI/CF/spring" />
</bean>

<bean id="solaceCachedConnectionFactory" class="org.springframework.jms.connection.CachingConnectionFactory">
	<property name="targetConnectionFactory" ref="solaceConnectionFactory" />
	<property name="sessionCacheSize" value="10" />
</bean>
{% endhighlight %}

Converting this xml to be purely annotation based only in a Java class:

{% highlight java %}
@Configuration
public class config {

	@Bean
	public JndiTemplate solaceJndiTemplate()
	{
		Properties environment = new Properties();
		environment.setProperty("java.naming.provider.url", "smf://___IP:PORT___");
		environment.setProperty("java.naming.factory.initial", "com.solacesystems.jndi.SolJNDIInitialContextFactory");
		environment.setProperty("java.naming.security.principal", "spring_user@Solace_Spring_VPN");
		environment.setProperty("java.naming.security.credentials", "spring_password");
		
		return new JndiTemplate(environment);	
	}
	
	@Bean
	public JndiObjectFactoryBean solaceConnectionFactory()
	{
		JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
		jndiObjectFactoryBean.setJndiName("JNDI/CF/spring");
		jndiObjectFactoryBean.setJndiTemplate(solaceJndiTemplate());
		
		return jndiObjectFactoryBean;
	}
	
	@Bean
	public CachingConnectionFactory solaceCachedConnectionFactory()
	{
		CachingConnectionFactory cachingConnectionFactory = new CachingConnectionFactory();
		cachingConnectionFactory.setTargetConnectionFactory(solaceConnectionFactory());
		cachingConnectionFactory.setSessionCacheSize(10);

		return cachingConnectionFactory;
		
	}
}
{% endhighlight %}

The difference between these 2 configurations is that there is a compilation error in the annotation based configuration, specifically due to this line:

{% highlight java %}
cachingConnectionFactory.setTargetConnectionFactory(solaceConnectionFactory());
{% endhighlight %}

The error mentions:

>
The method setTargetConnectionFactory(ConnectionFactory) in the type SingleConnectionFactory is not applicable for the arguments (JndiObjectFactoryBean)

The `setTargetConnectionFactory` method expects a `ConnectionFactory` to be the parameter whereas what is provided is a `JndiObjectFactoryBean`. As it stands the annotation based configuration can never be compiled and executed. 

The XML configuration however executes fine without problems, even though the configuration is exactly the same, where a `JndiObjectFactoryBean` is provided to the `setTargetConnectionFactory` method. Why is this?

With the XML configuration, at runtime while the object graph is being created, a JNDI lookup is invoked. The `solaceConnectionFactory` bean is performing a JNDI lookup to find a connection factory called `JNDI/CF/spring` on the Solace message bus, which will then be provided as the `targetConnectionFactory` to the `solaceCachedConnectionFactory` bean. This will then lead to the correct initialization of the `solaceCachedConnectionFactory` bean. Essentially this object derivation action can only occur during runtime and not at compile time, therefore given the XML configuration is invoked only at runtime this approach works. As this is not possible to achieve at compile time, the annotation configuration will never work.

The XML configuration approach is still a runtime only invocation, allowing for the object graph to be dynamic and deriving objects that can be injected. The annotation configuration is restricted by requiring it to be compliant at compile time first, therefore creating a condition for statically binding objects to one another based on type. The difference is highlighted in this example. This difference makes XML configuration still the complete and truest IoC configuration mechanism over annotation configuration, which due to its compile time dependency for functionality, weakens its IoC capabiltities. Until annotation based configuration can replicate the behaviour of XML configuration fully, XML configuration will still be relevant, required and in cases like these, superior to annotations.  

_The snippets of code in this article are from my repository [Spring: XML vs annotation context configuration][springXmlVsAnnotation]_

[IoC]: https://en.wikipedia.org/wiki/Inversion_of_control
[solaceMessageBus]: https://solace.com/products/message-routers
[solaceDocument]: https://solace.com/wp-content/uploads/resources/Solace-JMS-Integration-with-Spring-v4.0.pdf
[springXmlVsAnnotation]:https://github.com/hakanozbay/spring-xml-vs-annotation