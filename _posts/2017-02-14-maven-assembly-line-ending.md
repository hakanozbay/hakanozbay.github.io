---
layout: post
title:  "Maven: Dangers of line ending configuration in assembly plugin"
date:   2017-02-14 19:42:00
published: false
---

I use the [Maven Assembly Plugin][] often to package my project, its library dependencies and configuration files into a single distributable archive, to simplify deployment of particular executable artifacts of my system. 

I very much like the flexibility and features it offers during its assembly execution that eliminates the need for any need to do additional manual tasks or configurations once extracted from the archive file.

One example feature I use is the [File Set][]. This feature allows to specify which folders to place the files in, its filemode to establish account permissions, filtering to allow for property substitution into variables and line endings to apply different line ending styles depending on the operating system it will reside in.

The default value of the `lineEnding` element is `keep` if not specified which is the safest option. If the artifact you have created will run in a unix environment then it is useful to apply the unix line ending format to avoid markers like `^M` appearing at the end of each line of configuration files that may cause execution errors. 

The temptation is for creating a fileSet configuration for simplcity:

{% highlight xml %}
<fileSet>
	<directory>target/</directory>
	<outputDirectory>/</outputDirectory>
	<lineEnding>unix</lineEnding>
</fileSet>
{% endhighlight %}

This configuration applies unix line endings to everything when making the archive file. Although this may look fine, what happens is that your artifact will not be executable, throwing runtime exceptions. This is because the assembly plugin applies the unix line ending procedure to all files such as executable jar files and library dependency jar files which corrupts them. You can notice this as the resulting archive file size is smaller than anticipated. 

{% highlight xml %}
<fileSet>
	<directory>target/</directory>
	<outputDirectory>/</outputDirectory>
	<lineEnding>unix</lineEnding>
	<includes>
		<include>**/*.properties</include>
		<include>**/*.xml</include>
	</includes>
</fileSet>
<fileSet>
	<directory>target/</directory>
	<outputDirectory>/</outputDirectory>
	<lineEnding>keep</lineEnding>
	<excludes>
		<exclude>**/*.properties</exclude>
		<exclude>**/*.xml</exclude>
	</excludes>
</fileSet>
{% endhighlight %}

{% highlight xml %}
<fileSet>
	<directory>target/etc</directory>
	<outputDirectory>/etc</outputDirectory>
	<lineEnding>unix</lineEnding>
</fileSet>
<fileSet>
	<directory>target/lib</directory>
	<outputDirectory>/lib</outputDirectory>
</fileSet>
{% endhighlight %}




[Maven Assembly Plugin]: http://maven.apache.org/plugins/maven-assembly-plugin/
[File Set]: http://maven.apache.org/plugins/maven-assembly-plugin/assembly.html#class_fileSet