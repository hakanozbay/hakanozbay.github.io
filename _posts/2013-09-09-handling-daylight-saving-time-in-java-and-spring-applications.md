---
layout: post
title:  "Handling Daylight Saving Time in Java and Spring applications"
date:   2013-09-09 09:44:32
---

There have been many cases where Daylight Saving Time (DST) changes have lead to issues in software that have not been thought through properly whilst designing, potentially causing cascading problems. Implementing immediate solutions to this are only as complex as the dependent architecture of the software in question. I work on a financial market data platform, which requires time sensitive data processing at a consistent level. Designing solutions requires consideration for differences in timezones, as well as time changes such as DST in regions. I have devised a robust solution for handling DST changes in Java and Spring applications by making use of existing features of both technologies, outlined below.


Using Java's API, it can be identified if a particular timezone operates in DST, by using some of the methods offered in the [java.util.TimeZone][java.util.timezone] package. For example:

 
{% highlight java %}
boolean IsEuropeDST = TimeZone.getTimeZone("Europe/Berlin").inDaylightTime(new Date());
{% endhighlight %}
 
A simple boolean check like this can be added around parts of Java code as decision points. However, for more complex uses such as specific time based actions, an enhanced approach must be engineered, utilizing this feature.

If you are using an application framework such as [Spring][spring] or [Spring Integration][springIntegration], you will be familiar with using time based schedulers and triggers such as [Cron][cron]. A [Cron expression][cronExpression] is a String representing periodic scheduling behaviour for execution of processes/jobs. As these Strings are usually declared explicitly, it seems unavoidable that DST changes can be compensated for. However, using some of Spring's features, you can easily generate Cron expressions which will cater for DST changes.


Using the example above, lets abstract this into a useful method:

{% highlight java %}
public static String getEuropeCronHourTimeExpressionInUTC() 
{
 return TimeZone.getTimeZone("Europe/Berlin").inDaylightTime(new Date()) ? "12-22" : "13-23";
}
{% endhighlight %}

Using Java's TimeZone class methods and the tertiary operator, this method returns the hour part of a Cron expression. In the case above, the hour element translates as "between the hours of 12 and 22" if Europe is in DST, otherwise "between the hours of 13 and 23." This method won't be used in the Java code at all, it will be used in the Spring application context files where the Cron triggers are (should be) defined.

 
To accomplish this, a reference to the method must exist in the Spring context, via a bean. Usually, Spring beans have been placeholders for classes for dependency injection purposes, but it is also perfectly possible to have a bean for just a *method* using Spring's configuration library. In the Spring application context file, this can be done like so:

{% highlight xml %} 
<bean id="europeCronHourExpressionUTC" class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
      <property name="staticMethod" value="com.db.gm.cps.Utilities.getEuropeCronHourTimeExpressionInUTC" />
</bean>
{% endhighlight %}

A bean of the [MethodInvokingFactoryBean][MethodInvokingFactoryBean] class is created to execute the declared method in the "staticMethod" property wherever the bean is called upon in the Spring application context. Therefore by using [SPEL][spel] (**SP**ring **E**xpression **L**anguage) notation now, this bean can be used and invoked when the Cron expression is constructed, in scheduling beans making use of Cron triggers:

{% highlight xml %} 
<bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerBean">
  <property name="jobDetail" ref ="exampleJob"/>
  <property name="cronExpression" value = "0 0/5 #{europeCronHourExpressionUTC} * * MON-FRI" />
</bean>
 
<bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
  <property name="triggers">
    <list>
      <ref local="cronTrigger"/>
    </list>
  </property>
</bean>
{% endhighlight %} 
 

Similarly In Spring Integration, this can be accomplished in task schedulers:

{% highlight xml %}
<task:scheduled-tasks scheduler="workflowScheduler">
    <task:scheduled ref="myProcessingBean" method="startProcess" cron="0 0/5 #{europeCronHourExpressionUTC} * * MON-FRI"/>
</task:scheduled-tasks>
 
<task:scheduler id="workflowScheduler" />
{% endhighlight %} 
 
For clarity, this Cron expression evaluates as: 

> Execute every 5 minutes between the determined hours (either 12-22 or 13-23 depending on DST) Monday to Friday.

 
Only for the purpose of easy illustration of the solution, I have put the Cron expression directly into the Spring application context file. A good engineering practice is to abstract this expression into a property in a properties file and refer to the property in the Spring application context file using the `"${property}"` convention.

 
We've now accomplished an automated generation technique for the hour element of a Cron expression. This provides a modular, flexible and automatic solution to cater for DST changes, eliminating manual intervention and this point of failure in applications.

[java.util.timezone]: http://docs.oracle.com/javase/7/docs/api/java/util/TimeZone.html
[spring]: http://www.springsource.org/
[springIntegration]: http://www.springsource.org/spring-integration
[cron]: http://en.wikipedia.org/wiki/Cron
[cronExpression]: http://quartz-scheduler.org/documentation/quartz-1.x/tutorials/crontrigger
[MethodInvokingFactoryBean]: http://static.springsource.org/spring/docs/2.0.x/api/org/springframework/beans/factory/config/MethodInvokingFactoryBean.html
[spel]: http://static.springsource.org/spring/docs/3.0.x/reference/expressions.html