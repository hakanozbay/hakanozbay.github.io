---
layout: post
title:  "CoreOS, etcd, systemd and Fleet in 5 minutes"
date:   2015-10-24 15:20:00
---

## Introduction

This article is a simplified guide which guides you through doing infrastrcture setup for a CoreOS cluster (currently via Amazon EC2) and examples of using etcd, systemd and Fleet. 

The CoreOS setup will have etcd and fleet services running, for a cluster of 3 servers.


## Setup guide

Most of the information is following the [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/ec2/#manual-setup) EC2 guide for performing manual steps. 

Simple service configruation, usage and testing is followed from the [quick start](https://coreos.com/docs/quickstart/) guide leading onto complete guides for services.

# 1) Initial instance creation
Create a vanilla CoreOS instance on Amazon EC2. Create only a single instances. We will then do the necessary configuration and then clone 2 additional instances.

# 2) Security group setup
For your existing security group (via the Security groups page), you will need to open inbound ports 4001 and 7001 via a "custom TCP Rule" option, providing a soure type of "Custom IP" and entering your existing/current security group ID. 

# 3) Cloud-Config setup
Cloud-config is the CoreOS configuration file, which you can declaratively configure and customize various OS level items. This config file gets loaded every time the server is booted. It is here one can configure using services such as Fleet, etcd and cluster discovery. You can read up more about [cloud-config](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/)

The cloud-cofing file to be used is:

{% highlight YAML %}
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new?size=3
    # specify the initial size of your cluster with ?size=X
    discovery: https://discovery.etcd.io/<token>
    # multi-region and multi-cloud deployments need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
{% endhighlight %}

You can only edit the cloud-config when the instance is stopped and not running. In the "Instances" page, If the instance is running then stop the instance first. Then on the instance right-click and go to "Instance Settings" -> "View/Change User Data". You can then copy and past the above into the text area that appears.  
Note: replace the ```<token>``` with a token generated after visiting the URL [https://discovery.etcd.io/new?size=x](https://discovery.etcd.io/new?size=x) where x is the number of servers in the cluster.


# 4) Create multiple instances

In the instances section, right click on the server and select "Launch More Like This". On the following review page, make sure you alter the storage size (as it defaults to 8 GB) and change the Tag value for the "name" property. This will then create a new server with the same cloud-config file and security groups, therefore allowing the cluster to grow and enabling the instances to discover each other.

# 5) Test service discovery through etcd 

From the quick start guide you can view a [complete ectd guide](https://coreos.com/docs/distributed-configuration/getting-started-with-etcd/), but for the purposes of ensuring service discovery exists, a simple example is illustrated here. Note: it is recommended to start using etcd2 rather than etcd.

In one of the machines (login using ```SSH``` as user ```core```), set a key ```message``` with value ```Hello``` like so:

{% highlight sh %}
$ etcdctl set /message Hello
Hello
{% endhighlight %}

Log into another one of the machines in the cluster and read the value of the message:

{% highlight sh %}
$ etcdctl get /message
Hello
{% endhighlight %}

After this validation, you can change the current value:

{% highlight sh %}
$ etcdctl set /message "World" --swap-with-value "Hello"
World
{% endhighlight %}

Log into another machine in the cluster and read the value of the message:

{% highlight sh %}
$ etcdctl get /message
World
{% endhighlight %}

Service discovery and etcd have been setup succesfully now.

# 6) Unit files and systemd

systemd is an init system that provides features for starting, stopping and managing processes. systemd is run through Unit files that reside in ```/etc/systemd/system```, that have the extension ```.service```.

You can view the [systemd guide](https://coreos.com/docs/launching-containers/launching/getting-started-with-systemd/) for more details.

Create a ```hello.service``` file in the ```/etc/systemd/system``` folder with this content:

{% highlight ini %}
[Unit]
Description=MyApp
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

[Install]
WantedBy=multi-user.target
{% endhighlight %}

To start a new unit, systemd needs to create the symlink and then start the file:

{% highlight sh %}
$ sudo systemctl enable /etc/systemd/system/hello.service
Created symlink from /etc/systemd/system/multi-user.target.wants/hello.service to /etc/systemd/system/hello.service.
$ sudo systemctl start hello.service
{% endhighlight %}

To verify the unit started, you can see the list of containers running with command ```docker ps``` : 

{% highlight sh %}
CONTAINER ID        IMAGE               COMMAND                CREATED              STATUS              PORTS               NAMES
552dd2414a87        busybox:latest      "/bin/sh -c 'while t   About a minute ago   Up About a minute                       busybox1 
{% endhighlight %}

and read the unit's output with journalctl:

{% highlight sh %}
$ journalctl -f -u hello.service
-- Logs begin at Wed 2015-06-24 21:33:05 . --
Jun 26 10:07:10 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
Jun 26 10:07:11 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
Jun 26 10:07:12 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
Jun 26 10:07:13 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
Jun 26 10:07:14 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
{% endhighlight %}

You can check the status of the service:

{% highlight sh %}
$ sudo systemctl status hello.service 
● hello.service - MyApp
   Loaded: loaded (/etc/systemd/system/hello.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2015-06-26 10:04:31 ; 4min 51s ago
  Process: 9233 ExecStartPre=/usr/bin/docker pull busybox (code=exited, status=0/SUCCESS)
  Process: 9225 ExecStartPre=/usr/bin/docker rm busybox1 (code=exited, status=0/SUCCESS)
  Process: 9220 ExecStartPre=/usr/bin/docker kill busybox1 (code=exited, status=0/SUCCESS)
 Main PID: 9242 (docker)
   Memory: 1.5M
   CGroup: /system.slice/hello.service
           └─9242 /usr/bin/docker run --name busybox1 busybox /bin/sh -c while true; do echo Hello World; sleep 1; done

Jun 26 10:09:12 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
Jun 26 10:09:13 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
Jun 26 10:09:14 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
Jun 26 10:09:15 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
Jun 26 10:09:16 ip-172-31-5-148.eu-west-1.compute.internal docker[9242]: Hello World
{% endhighlight %}

You can stop the service by running the ```stop``` command:

{% highlight sh %}
$ sudo systemctl stop hello.service
{% endhighlight %}

# 7) Fleet

Fleet is a cluster manager that controls systemd at the cluster level. Fleet works by receiving systemd unit files and scheduling them onto machines in the cluster based on declared conflicts and other preferences encoded in the unit file. Two types of units can be run in your cluster — *standard* and *global* units. Standard units are long-running processes that are scheduled onto a single machine. If that machine goes offline, the unit will be migrated onto a new machine and started. Global units will be run on all machines in the cluster.

You can see which machines are under fleet's control by running the command:

{% highlight sh %}
$ fleetctl list-machines
MACHINE		IP		METADATA
0f964083...	172.31.5.148	-
6f205ff2...	172.31.15.153	-
90df043a...	172.31.5.206	-
{% endhighlight %}

**To illustrate an example of a standard unit:**

create a ```myapp.service``` file in ```/etc/systemd/system``` which conatins:

{% highlight ini %}
[Unit]
Description=MyApp
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1
{% endhighlight %}

Run the start command to start up the container on the cluster:

{% highlight sh %}
$ fleetctl start myapp.service
Unit myapp.service launched on 0f964083.../172.31.5.148
{% endhighlight %}

The unit should have been scheduled to a machine in your cluster:

{% highlight sh %}
$ fleetctl list-units
UNIT		MACHINE				ACTIVE	SUB
myapp.service	0f964083.../172.31.5.148	active	running
{% endhighlight %}

{% highlight sh %}
$ fleetctl list-unit-files
UNIT		HASH	DSTATE		STATE		TARGET
myapp.service	391d247	launched	launched	0f964083.../172.31.5.148
{% endhighlight %}

To stop the container, run:

{% highlight sh %}
$ fleetctl destroy myapp.service
Destroyed myapp.service
{% endhighlight %}

**To illustrate an example of a global unit:**

on the bottom of the ```myapp.service``` file add an additional tag and value
{% highlight ini %}
[X-Fleet]
Global=true
{% endhighlight %}

Run the start command to start up the container on all clusters:

{% highlight sh %}
$ fleetctl start myapp.service 
Triggered global unit myapp.service start
{% endhighlight %}

{% highlight sh %}
$ fleetctl list-units     
UNIT		MACHINE				ACTIVE	SUB
myapp.service	0f964083.../172.31.5.148	active	running
myapp.service	6f205ff2.../172.31.15.153	active	running
myapp.service	90df043a.../172.31.5.206	active	running
{% endhighlight %}

{% highlight sh %}
$ fleetctl list-unit-files     
UNIT		HASH	DSTATE		STATE	TARGET
myapp.service	61017f3	launched	-	global
{% endhighlight %}

{% highlight sh %}
$ fleetctl destroy myapp.service 
Destroyed myapp.service
{% endhighlight %}

To read further on Fleet and its commands, visit the [Fleet client page](https://coreos.com/docs/launching-containers/launching/fleet-using-the-client) and [launching containers with fleet](https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/)
