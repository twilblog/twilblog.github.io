---
layout: post
title:  "Using 'ng-messages' in a Repeater"
date:   2017-01-19 12:00:00:00
author: shaun
categories: AngularJS ng-messages
---

`ng-messages` is great for showing form errors. Here's a contrived example:

{% highlight html %}
<form name="myForm">
  <input type="text" name="username" required />
  <div ng-messages="myForm.username.$error">
    <div ng-message="required">Cannot be blank.</div>
  </div>
</form>
{% endhighlight %}

Some nice validation, without having to write any JS code to make it happen. Nice, right?

But how about when you're using a repeater? You need to use `ng-repeat`'s built in
`$index` variable for the `name` attribute, and then in your `ng-messages` attribute use  array syntax instead
of dot syntax:

{% highlight javascript %}
// Controller code.
const things = ['one', 'two', 'three'];
{% endhighlight %}

{% highlight html %}
<form name="myForm">
  <div ng-repeat="thing in things">
    <input type="text" name="thing{{$index}}" required />
    <div ng-messages="myForm['thing' + $index].$error">
      <div ng-message="required">Cannot be blank.</div>
    </div>
  </div>
</form>
{% endhighlight %}

It's a bit inelegant, but it seems to be the best way to do the job.
