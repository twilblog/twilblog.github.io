---
layout: post
title:  "Getting to grips with Java 8's DateTimeFormatter"
date:   2016-01-22 16:27:21
author: christian
categories: Java 8
---

# Background

Gone are the days of using the SimpleDateFormat to format your dates and times to a nice string. Enter DateTimeFormatter which produces the same result but has a couple nuances which tripped us up at first. 

# Problem

One of the most frustrating problems proved to be with how the DateTimeFormatter actually works. When trying to format an Instant using the formatter shown in the below example:

{% highlight java}
{
	private final DateTimeFormatter formatterItim = DateTimeFormatter.ISO_OFFSET_DATE_TIME;
	Instant now = Instant.now(clock).truncatedTo(ChronoUnit.SECONDS);
	String formatted = formatterItim.format.now();
}
{% endhighlight}

Running this would cause an error and produce an extremely ambiguous stack trace:

{% highlight java}
{
	java.time.temporal.UnsupportedTemporalTypeException: Unsupported field: Year
}
{% endhighlight}

# Solution

Turns out this problem occured because the formatter wasn't given a Zone when it was initialized so it threw a really unhelpful and seemingly irrelevant exception. This is resolved by adding the following to DateTimeFormatter:

{% highlight java}
{
	private final DateTimeFormatter formatterItim = DateTimeFormatter.ISO_OFFSET_DATE_TIME.withZone(ZoneId.systemDefault());
}

# Problem

Another problem we had was trying to figure out how you specify what format you want an offset in. The Java Docs say you should use x, X or Z to specify a zone-offset but then give multiple different examples of what it will produce....: +0000; -0800; -08:00; 

We couldn't figure out how you explicitly tell it to give you an offset with a colon in (+02:00) as opposed to one without (+2000).

# Solution

If you want on offset with a colon using x or X then you'll need to use three to get +02:00 (that's xxx or XXX) and for Z for you'll need 5! (ZZZZZ)


