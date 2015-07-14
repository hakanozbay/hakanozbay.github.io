---
layout: post
title:  "Timezone conversion by BlazeDS while coding in Flex and Java leads to date changes"
date:   2012-02-28 19:29:24
categories: jekyll update
---

When working with Date and Timestamp classes in Java, transfering this data to the Flex UI client through BlazeDS causes an auto correction depending on the timezone you are located in. This "clever" feature in BlazeDS and Flex is actually troublesome, as timezone corrections lead to potentially changing the date of the data that is visible to viewers in different regions of the world.

In the past, business users of the application that I develop have pointed out numerous times that they are seeing incorrect dates for data. However, when I check at the same time as them, the dates that are visible to me are the correct dates. The users are located in the US (Florida and California) and are telling me that the dates they are seeing are T-1 (i.e. 1 day before what they expect).

 So what's going on?  Here's an example:

 I am a developer in London, UK. When I load my Flex application, it queries the database on the Java sever side, and returns us a date, say, 28/02/2012. When this date is transferred over to Flex via BlazeDS, the incoming date data looks like this:
 
`
<Tue Feb 28 00:00:00 GMT+0000 2012>
`
 
On my UI, I can now see 28/02/2012. Note however, the declared default timestamp of 00:00:00 and the timezone GMT +0000. Flex has detected my timezone, and appended the default time of midnight, as we have not specifically provided a time element to our date object on the Java side.

 If a business user in California also performs the same action, the incoming date data for them, looks like this:

` 
<Mon Feb 27 16:00:00 GMT-0800 2012>
`
 
It turns out that Flex's detection of timezone and automatic time correction has lead to discrepancies of the data. This feature is good for real-time scenarios, but in the case of displaying static data, it can give headaches.

 
How can this be corrected? The best way to avoid this problem is to amend the timestamp of the date object on the Java side to incorporate timezone differences. This way, when the date object is transferred to Flex, the timezone correction does not result in date changes. Here's how this can be achieved:

 
On the Java, get the time difference of your current region:

 
{% highlight java %}
int timezoneOffset = Calendar.getInstance().getTimeZone().getRawOffset() ;  
{% endhighlight %}
 

Apply this offset to timestamp of your date object:

{% highlight java %} 
date.setTime(date.getTime() - timezoneOffset);
{% endhighlight %} 

Before the data gets sent to Flex, it now looks like this:


` 
<28/02/2012 08:00>
`

When it is transferred to Flex, the data now looks like this:

`
<Tue Feb 28 00:00:00 GMT-0800 2012>
`
 
The timezone correction has subtracted the 8 hour time difference to make the timestamp midnight again.

 
There is a more in depth article and discussion of this [in the Adobe cookbook][cookbook].

[cookbook]:  http://cookbooks.adobe.com/post_Date_time_transfer_in_BlazeDS-13114.html
