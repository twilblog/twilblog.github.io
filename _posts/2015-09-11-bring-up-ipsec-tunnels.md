---
layout: post
title:  "Bringing up Cross Region Openswan Tunnels in AWS"
date:   2015-09-11 11:00:00:00
author: Ben Doyle
categories: VPN ipsec AWS
---
During a recent project, Openswan (or Freeswan) tunnels weren't coming up for me.

I really couldn't figure out why, and the documentation was a little scarce surrounding this.


# Config
According to a few different [sources](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Security_Guide/Host-To-Host_VPN_Using_Openswan.html#Verify_Host-To-Host_VPN_Using_Openswan):

*"You can use the identical configuration file on both left and right hosts."*

I found this not to be the case, and eventually managed to get the following working, where the *left* was the instance's own details, and the *right* the details of the target instance.

Below is an example of a tunnel I had in `/etc/ipsec.conf`:

{% highlight yaml %}
conn region1_to_region2
        type=tunnel
        authby=secret
        left=%defaultroute
        leftid=my-elastic-ip
        leftnexthop=%defaultroute
        leftsubnets=my-subnet-cidr
        right=other-instance-elastic-ip
        rightsubnets=other-instance's-subnet-cidr
        pfs=yes
        auto=ignore
{% endhighlight %}

Auto ignore in this case means that when the instance/server restarts, you'll have to manually bring up the ipsec tunnel to let traffic flow.

You can set it to:

{% highlight yaml %}
auto=start
{% endhighlight %}
so that it will start when the *ipsec* service starts. 

This isn't always ideal... especially if your project is to make a highly-available cross-region VPN, with tunnel failover - More on that in future posts...


Then, in `/etc/ipsec.secrets` I had something like:

{% highlight yaml %}
# Connection from region1 to region2
my.elas.tic.ip other.elas.tic.ip: PSK "some long pre-shared key"
{% endhighlight %}


# Add before up
So I had my config all added in, and the *ipsec* service has been started.
I noticed that sometimes, my tunnels wouldn't come up when I told them to...
Even when I set `auto=start`. 

I eventually found out that the tunnels must be *"added"* on either VPN endpoint, before an ipsec tunnel can be brought *"up"*:

{% highlight yaml %}
ipsec auto --add region1_to_region2
{% endhighlight %}
Again, this needs to be run on **both** endpoints before you can run:

{% highlight yaml %}
ipsec auto --up region1_to_region2
{% endhighlight %}
If it's successful you'll get output like this from the `--up` commands:

{% highlight yaml %}
004 "region1_to_region2/1x1" #2: STATE_QUICK_I2: sent QI2, IPsec SA established tunnel mode {ESP=>0xdeadbeef...
{% endhighlight %}


# And don't forget...
AWS instances need to have Source/Destination Checks disabled, if they're going to send traffic with source/destination traffic that isn't their own IP address.

This can be done by right clicking the instance in the console, hovering over *Networking* and pressing *Change Source/Dest. Check*, and pressing, *Yes, Disable*.

This isn't all you'll need to do for a proper site-to-site VPN to let traffic flow through the VPN endpoints though, one last step.

In `/etc/sysctl.conf`, make sure the following are set to the following:

{% highlight yaml %}
net.ipv4.ip_forward = 1
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
{% endhighlight %}
and then apply these updates with the command:

{% highlight yaml %}
sysctl -p
{% endhighlight %}