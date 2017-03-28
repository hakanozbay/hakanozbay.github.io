---
layout: post
title:  "Python 3: Resolve \"SSLV3_ALERT_HANDSHAKE_FAILURE\" error"
date:   2015-12-29 14:42:00
---

I recently came across this error message when attempting to communicate with a server at the point of authentication/authorisation. I thought a simple Google search would give me the right answers to resolve this, however it turned out to be more complicated than anticipated, as the answers I read in various Stackoverflow posts and other online forums provided many different approaches.

After several hours of exploring, troubleshooting and trying new approaches I finally resolved this issue. I am blogging about it to provide more clraity on the approach that helped me and to provide geneal troubleshooting steps to identify the root issue.

## Attempting to explictiy specify the protocol

The first common tip I read is that the SSL3 protocol is insecure and shouldn't be used, in favour of the TLS protocol. Most suggestions recommended that explicity declaring usage of TLS and not using SSL would resolve the issue. 

The [Python SSL][] library offers the following protocols for connectivity: SSL2, SSL3, TLS 1.0, TLS 1.1, TLS 1.2 

Configuring the protocol usages involves using the following snippets of code as examples, later wrapping them into your connection code:

This allows for Python to configure the default and optimumum settings to use
{% highlight python %}
context = ssl.create_default_context()
{% endhighlight %}

This explicity states that SSL2 and SSL3 should not be used, therefore allowing for only TLS to be used
{% highlight python %}
context = ssl.SSLContext(ssl.PROTOCOL_SSLv23)
context.options |= ssl.OP_NO_SSLv2
context.options |= ssl.OP_NO_SSLv3
{% endhighlight %}

This explicity states that TLS 1.0 should be used 
{% highlight python %}
context = ssl.SSLContext(ssl.PROTOCOL_TLSv1)
{% endhighlight %}

If these work out for you, then great! However my issue was not resolved and I had to continue troubleshooting.

## OpenSSL Version

I wanted to try using TLS 1.1 and 1.2 explicitly, as the server I am communicating with may only accept these protocol versions.  
Adapting to the above and trying to use ``` ssl.SSLContext(ssl.PROTOCOL_TLSv1_1) ``` and ``` ssl.SSLContext(ssl.PROTOCOL_TLSv1_2) ``` in my code gave me a separate error:

``` AttributeError: module 'ssl' has no attribute 'PROTOCOL_TLSv1_1' ```  
``` AttributeError: module 'ssl' has no attribute 'PROTOCOL_TLSv1_2' ```

Looking at the Python documentation for these protocol versions, it states

> ssl.PROTOCOL_TLSv1_1  
Selects TLS version 1.1 as the channel encryption protocol. Available only with openssl version 1.0.1+.  
New in version 3.4.

> ssl.PROTOCOL_TLSv1_2  
Selects TLS version 1.2 as the channel encryption protocol. This is the most modern version, and probably the best choice for maximum protection, if both sides can speak it. Available only with openssl version 1.0.1+.  
New in version 3.4.

Note the part which states ***available only with openssl version 1.0.1+. New in version 3.4.***

It is clear that your Python 3.4+ installation must be using OpenSSL 1.0.1+. To establish what version you are using, open a Python command prompt and check the ``` OPENSSL_VERSION ```

{% highlight python %}
$ python3
Python 3.5.0 (v3.5.0:374f501f4567, Sep 12 2015, 11:00:19) 
Type "help", "copyright", "credits" or "license" for more information.
>>> import ssl
>>> ssl.OPENSSL_VERSION
'OpenSSL 0.9.8zg 14 July 2015'
{% endhighlight %}

As evident, I am unable to use TLS 1.1 and 1.2 as the OpenSSL version my Python installation is using is ``` OpenSSL 0.9.8zg 14 July 2015 ```.

To verify that the protocols do no exist, on the command prompt type the variable names for the protocol to see their attribute values if they exist

{% highlight python %}
>>> ssl.PROTOCOL_SSLv2
<_SSLMethod.PROTOCOL_SSLv2: 0>
>>> ssl.PROTOCOL_SSLv3
<_SSLMethod.PROTOCOL_SSLv3: 1>
>>> ssl.PROTOCOL_SSLv23
<_SSLMethod.PROTOCOL_SSLv23: 2>
>>> ssl.PROTOCOL_TLSv1
<_SSLMethod.PROTOCOL_TLSv1: 3>
>>> ssl.PROTOCOL_TLSv1_1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'ssl' has no attribute 'PROTOCOL_TLSv1_1'
>>> ssl.PROTOCOL_TLSv1_2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'ssl' has no attribute 'PROTOCOL_TLSv1_2'
{% endhighlight %}

With this proof, I then proceed to install the latest OpenSSL version, verifying it on the command prompt

{% highlight sh %}
$ openssl
OpenSSL> version
OpenSSL 1.0.2d 9 Jul 2015
{% endhighlight %}

I then check that the new version is being used in Python

{% highlight python %}
$ python3
Python 3.5.0 (v3.5.0:374f501f4567, Sep 12 2015, 11:00:19) 
Type "help", "copyright", "credits" or "license" for more information.
>>> import ssl
>>> ssl.OPENSSL_VERSION
'OpenSSL 0.9.8zg 14 July 2015'
{% endhighlight %}

Unexpectedly, Python still uses the old version of OpenSSL. It turns out that **Python uses either its own pacakged OpenSSL version or the version that existed on your system at the time of installation.**

For Macs - In order to use the updated version of OpenSSL on your system, you must reinstall Python and OpenSSL via ```brew``` specifying usage of the OpenSSL installed with brew.

## Reinstall Python and force usage of newer OpenSSL version

In order to do this on a Mac, run the following commands:

{% highlight sh %}
$ brew install openssl
$ brew link openssl --force
$ brew install python3 --with-brewed-openssl
{% endhighlight %}

Once completed, there will be a new Python and pip installation at:

```
/usr/local/Cellar/python3/3.5.0/bin/python3  
/usr/local/Cellar/python3/3.5.0/bin/pip3
```

# Remap python3 and pip3 command line aliases

As the exisitng Python installation exists, it is worthwhile remapping the ```python3``` and ```pip3``` command line aliases to point to the new installation for convenience. 

You can do this by creating aliases in your ```.bash_profile``` file as follows:

{% highlight sh %}
vi ~/.bash_profile
{% endhighlight %}

{% highlight sh %}
alias python3="/usr/local/Cellar/python3/3.5.0/bin/python3"
alias pip3="/usr/local/Cellar/python3/3.5.0/bin/pip3"
{% endhighlight %}

## Conclusion

After following the above, with the new Python installation and OpenSSL version, I can verify if this has worked by opening a Python command prompt, checking the OpenSSL version and the variable names for the protocols to see their attribute values if they exist

{% highlight python %}
$ python3
Python 3.5.0 (default, Sep 23 2015, 04:42:00) 
Type "help", "copyright", "credits" or "license" for more information.
>>> import ssl
>>> ssl.OPENSSL_VERSION
'OpenSSL 1.0.2d 9 Jul 2015'
>>> ssl.PROTOCOL_SSLv2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'ssl' has no attribute 'PROTOCOL_SSLv2'
>>> ssl.PROTOCOL_SSLv3
<_SSLMethod.PROTOCOL_SSLv3: 1>
>>> ssl.PROTOCOL_SSLv23
<_SSLMethod.PROTOCOL_SSLv23: 2>
>>> ssl.PROTOCOL_TLSv1
<_SSLMethod.PROTOCOL_TLSv1: 3>
>>> ssl.PROTOCOL_TLSv1_1
<_SSLMethod.PROTOCOL_TLSv1_1: 4>
>>> ssl.PROTOCOL_TLSv1_2
<_SSLMethod.PROTOCOL_TLSv1_2: 5>
{% endhighlight %}

We can see that TLS 1.1 and TLS 1.2 exist, and SSL2 no longer exists. 

With this in place, retrying my code has worked ***without a need to even explicitly specify a protocol to use.***

The critical conclusion in this article is to **primarily make sure that your Python installation uses OpenSSL 1.0.1+, before needing to explicitly specify which protocol to use, as the Python default settings of the SSL module may be sufficient.**

[Python SSL]: https://docs.python.org/3/library/ssl.html 
