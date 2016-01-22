---
layout: post
title:  "Useful Java APIs for unit tests"
date:   2015-07-13 15:00:00
author: andy
categories: java junit
---

I came across 2 interesting APIs to make testing easier this week...

## Awaitility

The first is [Awaitility](https://code.google.com/p/awaitility/) which makes it easy to test asynchronous 
systems, it allows you to express a condition to wait for using a fluent API that encapsulates all the threading
and retry logic that would otherwise be required. It works by polling for the condition to be met for a defined
interval, failing if the condition is not met within that time.

The example below demonstrates one way the code could wait for single async insert to occur:

{% highlight java %}

@Test
public void endToEndTest() {
    saveActivityAsync();
    await().until(activityCount(), equalTo(1));
}

private Callable<Integer> activityCount() {
    return () -> repository.getActivityCount();
}

{% endhighlight %}

Getting started is easy, just add the maven dependency:
{% highlight xml %}
<dependency>
      <groupId>com.jayway.awaitility</groupId>
      <artifactId>awaitility</artifactId>
      <version>1.6.3</version>
      <scope>test</scope>
</dependency>
{% endhighlight %}

There are many configuration options (e.g. poll delay/frequency) and different way to express the condition to be
met so it is well worth reading the simple and [concise documentation here](https://code.google.com/p/awaitility/wiki/Usage).

**Beware**, I had trouble with the latest version and wasn't getting my conditions retried until I downgraded to
version 1.6.1.

## JsonUnit

The second is [JsonUnit](https://github.com/lukas-krecan/JsonUnit) which allows you to compare JSON in a far more
flexible manner. For example you can ignore certain fields (for example if they will contain a generated value)
or ignore array ordering when you only care each element is present. A simple example is below:

{% highlight java %}
String expectedResponse = "{\"test\":1}";
String actualResponse = "{\n\"test\": 1\n}"
assertThatJson(actualResponse).when(IGNORING_ARRAY_ORDER).isEqualTo(expectedResponse);
{% endhighlight %}

You can also easily compare the JSON against some regukar expression. For example you might want to see if a UUID was present using the string below:

{% highlight java %}
String jsonUnitUuidRegex = "${json-unit.regex}[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}";
{% endhighlight %}

Getting started is easy, just add the maven dependency and check out the [documentation](https://github.com/lukas-krecan/JsonUnit):
{% highlight xml %}
<dependency>
    <groupId>net.javacrumbs.json-unit</groupId>
    <artifactId>json-unit-fluent</artifactId>
    <version>1.5.5</version>
    <scope>test</scope>
</dependency>
{% endhighlight %}
