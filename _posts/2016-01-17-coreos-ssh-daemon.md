---
layout: post
title:  "CoreOS: Issues with customizing the SSH Daemon port number"
date:   2016-01-17 21:09:30
---

While working on a project, I have been using CoreOS to be the main component of the infrstrtuctre. I wanted to set up SSH access on a different port number than the standard 22 (for example 2222), but this turned out to be more difficult than anticipated.

After reading through the CoreOS documentation, I found an article on [Customizing the SSH Daemon][], which outlines how to customize the SSH daemon (```sshd```) configuration and unit to listen out for specific port numbers.

I essentially followed this cloud-config setup as outlined in the article:

{% highlight YAML %}
#cloud-config

coreos:
  units:
  - name: sshd.socket
    command: restart
    content: |
      [Socket]
      ListenStream=2222
      Accept=yes
{% endhighlight %}

The above currently intends for the sshd socket unit to now listen only on port 2222 on all interfaces at runtime.

This indeed does work, however only for a limited time. After a few days, when trying to login to the server I get a connection error:

```ssh: connect to host 123.45.6.789 port 2222: Connection refused``` 

After doing several failed troubleshooting steps, I decided to read the [OpenSSH manual][] to understand if I can customize the ***sshd configuration*** rather than the ***sshd socket unit***. I found a parameter called ```Port``` defined as:

> Port  
Specifies the port number that sshd(8) listens on. The default is 22. Multiple options of this type are permitted. See also ListenAddress.

It then became clear that the sshd configuration should specify the port number rather than the socket unit, as the socket unit reads the sshd configuration.   
The following cloud-config setup creates the required configuration:

{% highlight YAML %}
#cloud-config

write_files:
  - path: /etc/ssh/sshd_config
    permissions: 0600
    owner: root:root
    content: |
      Port 2222
{% endhighlight %}

This now works permanently without any issues.

The reason why the sshd socket unit fails after a few days is unclear. It is likely to be an underlying software bug within the CoreOS operating system for this unit when customizing its runtime behaviour. My recommendation is not to customize the runtime parameters, but to customize the configuration which is fed into the runtime service.

[Customizing the SSH Daemon]: https://coreos.com/os/docs/latest/customizing-sshd.html 
[OpenSSH manual]: http://www.openssh.com/cgi-bin/man.cgi?query=sshd_config
