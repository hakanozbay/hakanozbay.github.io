---
layout: post
title:  "Capture URL redirects in Java"
date:   2011-03-05 15:17:52
---

URLs are generally aliases that are then redirected to another URL. There are times when you actually want to caputre the URL itself rather than be redirected automatically. A simple example of this is when redirects lead to a file download, where you potentially need to handle the file download code differently or do different actions based on the file type etc.

In order to capture the redirected URL, the code snippet below demonstrates how to do this:

{% highlight java %}
URL url = new URL(url);
HttpURLConnection firstURLConnection = (HttpURLConnection) url.openConnection();
firstURLConnection.setInstanceFollowRedirects(false);
URL secondURLConnection = new URL(firstURLConnection.getHeaderField("Location"));
URLConnection conn = secondURLConnection.openConnection();
{% endhighlight %}

The "magic" here happens in these 2 steps:

{% highlight java %}
firstURLConnection.setInstanceFollowRedirects(false);
URL secondURLConnection = new URL(firstURLConnection.getHeaderField("Location"));
{% endhighlight %}

By default ```InstanceFollowRedirects``` is set to true, but you want to set it to false in order to capture the second URL. Then to retrieve that second URL from the first URL, you need to get the value of the header field called ```Location``` from the first URL connection.