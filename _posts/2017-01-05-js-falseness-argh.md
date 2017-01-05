---
layout: post
title:  "Don't use zero in constants if you value your sanity"
date:   2017-01-05 12:00:00:00
author: shaun
categories: JavaScript
---

This has happened to me enough times now that I should know better.

I have a constant:

{% highlight javascript %}
const statuses = {
  OK: 0,
  ERROR: 1
};
{% endhighlight %}

I use this constant to check the status of something:

{% highlight javascript %}
let currentStatus = null;
Something.getStatus().then((status) => {
  currentStatus = status;
});

// Then later on..

if (currentStatus && currentStatus === statuses.OK) {
  // do something.
}
{% endhighlight %}

The `if` statement will never actually work, because in the `statuses`
constant `OK` is `0`, which is falsy, meaning that the `if (currentStatus`
part won't be true.

It's very easy to solve this, just use strings or positive values:

{% highlight javascript %}
const withNumbers = {
  OK: 1,
  ERROR: 2
};

const withStrings = {
  OK: '0',
  ERROR: '1'
};
{% endhighlight %}
