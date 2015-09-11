---
layout: post
title:  "Running JARs, and ordering your arguments"
date:   2015-07-13 15:00:00
author: shaun
categories: java jar argument
---

When running a Java JAR, you'll often want to pass some command line arguments. In the below example, an argument
is being used to set the port the application runs on.

{% highlight bash %}
java -jar myapp.jar -Dserver.port=7428
{% endhighlight %}

However, this won't work, because the arguments were passed in after the `-jar myapp.jar` part.

Switching the arguments around fixes this:

{% highlight bash %}
java -Dserver.port=7428 -jar myapp.jar
{% endhighlight %}

So, if it ever seems like one of your properties isn't being picked up, check the arguments just in case.