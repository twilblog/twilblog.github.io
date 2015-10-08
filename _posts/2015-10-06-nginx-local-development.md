---
layout: post
title:  "Local Development With NGINX To Authenticate Against Another Server"
date:   2015-10-06 11:00:00:00
author: shaun
categories: NGINX Vagrant 
---
# Background

I'm developing an application that requires authentication, but doesn't do it directly. 

In production, you log in at *https://my-app.com* (not a real URL), and then a link takes you to *https://my-app.com/myproduct*. When you visit */myproduct*, you're accessing a different server from */*. To handle authentication, *myproduct* checks a session cookie that was assigned by */* against a Redis store. If the session is valid, you're allowed in, otherwise you're redirected to */* to log in.

# Vagrant VM

I've got a Vagrant virtual machine set up to host my tech stack - which in this case is the app that handles logins, a MySQL database and a Redis store (to support said logins). I have an entry in my hosts file to point a hostname at the IP address the machine is located at:

{% highlight bash %}
192.168.0.100 vagrant.localhost.com
{% endhighlight %}

With this, I can go to *http://vagrant.localhost.com* in my browser to log in, like in prod.

# Local Development

When developing my app, it runs on *localhost*. This presents a problem - I can log in and acquire a session at *http://vagrant.localhost.com*, but my app running on *localhost* can't read the session cookie, because the cookie is set against *vagrant.localhost.com*.

# Enter NGINX!

I installed NGINX on my local machine and added a new *server* block, which looks like this:

{% highlight nginx %}
server {
    listen 80;
    server_name vagrant.localhost.com;

    location /dev {
        proxy_pass http://localhost/myproduct;
    }

    location / {
        proxy_pass http://vagrant.localhost.com;
    }
}
{% endhighlight %}

With this block, requests to *http://vagrant.localhost.com/dev* will route to the local development environment, whereas all other requests to *http://vagrant.localhost.com* will route to the Vagrant machine. This means that they can now share cookies and authentication will work as in prod.
