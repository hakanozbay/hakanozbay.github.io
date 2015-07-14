---
layout: post
title:  "Enable your FlashBuilder to have code-assist for both Flex and Java in your project"
date:   2012-02-28 17:24:00
---

In FlashBuilder a user has the ability to create either a Java project or a Flex project, but combining them into 1 project in the workspace for code-assist and building is not straight forward. This blog outlines how you can achieve this. You must have a project that uses both Flex and Java.
 
 
When you create a Flex project, you can see that options such as setting the Flex buildpath are enable for you to do so by default. This is because FlashBuilder creates a `.project` file in your workspace project folder. This file contains build specifications and project natures. Here is an example:
 
{% highlight xml %} 
<?xml version="1.0" encoding="UTF-8"?>
<projectDescription>
<name>PROJECT_NAME</name>
<comment></comment>
<projects>
</projects>
<buildSpec>
  <buildCommand>
   <name>com.adobe.flexbuilder.project.flexbuilder</name>
   <arguments>
   </arguments>
  </buildCommand>
</buildSpec>
<natures>
  <nature>com.adobe.flexbuilder.project.flexnature</nature>
  <nature>com.adobe.flexbuilder.project.actionscriptnature</nature>
</natures>
</projectDescription>
{% endhighlight %}
 
This file shows the structure of the created project in FlashBuilder and its natures are specified. All specs are defined for Flex atrributes of the project. To allow you to configure Java buildpath and properties, just simply amend this file to include a Java build command and nature like so:

{% highlight xml %} 
<?xml version="1.0" encoding="UTF-8"?>
<projectDescription>
<name>PROJECT_NAME</name>
<comment></comment>
<projects>
</projects>
<buildSpec>
  <buildCommand>
   <name>com.adobe.flexbuilder.project.flexbuilder</name>
   <arguments>
   </arguments>
  </buildCommand>
  <buildCommand>
   <name>org.eclipse.jdt.core.javabuilder</name>
  </buildCommand>
</buildSpec>
<natures>
  <nature>com.adobe.flexbuilder.project.flexnature</nature>
  <nature>com.adobe.flexbuilder.project.actionscriptnature</nature>
  <nature>org.eclipse.jdt.core.javanature</nature>
</natures>
</projectDescription>
{% endhighlight %}
 
You can now start setting the Java build path, specify libraries and other variables to the Java side of your project. Code-assist and Intellisense are now enabled aswell for syntax checking, error checking and compilation.