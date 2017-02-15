---
layout: post
title:  "Maven: Dangers of line ending configuration in assembly plugin"
date:   2017-02-15 21:41:00
---

I use the [Maven Assembly Plugin][] often to package my project, its library dependencies and configuration files into a single distributable archive, to simplify deployment of particular executable artifacts of my system. 

I very much like the flexibility and features it offers during its assembly execution that eliminates the need for any additional manual tasks or configuration once extracted from the archive file.

One example feature I use is the [File Set][]. This feature allows to specify which folders to place the files in, its filemode to establish account permissions, filtering to allow for property substitution into variables and line endings to apply different line ending styles depending on the operating system it will reside in.

The default value of the `lineEnding` element is `keep` if not specified which is the safest option. If the artifact you have created will run in a unix environment then it is useful to apply the unix line ending format to avoid markers like `^M` appearing at the end of each line of configuration files that may cause execution errors. 

The temptation is to create a fileSet configuration for simplicity like this:

{% highlight xml %}
<fileSet>
	<directory>target/</directory>
	<outputDirectory>/</outputDirectory>
	<lineEnding>unix</lineEnding>
</fileSet>
{% endhighlight %}

This configuration applies unix line endings to everything when making the archive file. Although this may look fine, what happens is that your artifact will not be executable, throwing runtime exceptions. This is because the assembly plugin applies the unix line ending procedure to all files such as executable jar files, library dependency jar files, certificates and truststores, which shreds and corrupts them.

**The lineEnding element must only be used for plaintext files**. Example file extensions are `.properties`, `.text`, `.xml` or `.sh`, where the contents of the file are plaintext. You must exclude files such as libraries (`.jar`), truststores/keystores (`.jks`), certificates(`.cer`,`.p12`,`.pem`) or other files that are not plaintext. 

In order to specify only the particular plaintext files or file extensions to apply the unix line endings to, we need to utilize a combination of the `<includes>` and `<excludes>` elements :

{% highlight xml %}
<fileSet>
	<directory>target/</directory>
	<outputDirectory>/</outputDirectory>
	<lineEnding>unix</lineEnding>
	<includes>
		<include>**/*.properties</include>
		<include>**/*.xml</include>
		<include>**/*.text</include>
		<include>**/*.sh</include>
	</includes>
</fileSet>
<fileSet>
	<directory>target/</directory>
	<outputDirectory>/</outputDirectory>
	<lineEnding>keep</lineEnding>
	<excludes>
		<exclude>**/*.properties</exclude>
		<exclude>**/*.xml</exclude>
		<exclude>**/*.text</exclude>
		<exclude>**/*.sh</exclude>
	</excludes>
</fileSet>
{% endhighlight %}

This configuration applies unix line endings only to files with the extensions of .properties, .xml, .text and .sh and will keep the existing line endings for everything else. It is necessary to have the second fileSet with the excludes elements otherwise there will be a conflict over which line ending to use for the file extensions in question. This can result in the first fileSet configuration being overwritten by the second one.

The conclusion is to use lineEnding settings carefully and specify it only for plaintext files.


[Maven Assembly Plugin]: http://maven.apache.org/plugins/maven-assembly-plugin/
[File Set]: http://maven.apache.org/plugins/maven-assembly-plugin/assembly.html#class_fileSet