---
layout: post
title:  "JavaScript: How to access a character at a specific position in a number"
date:   2015-07-10 16:57:24
author: Sam Adams
categories: javascript
---

Using JavaScript, You can access any character in a *string*: 

{% highlight javascript %}
'foo'[1]
//=> "o"
{% endhighlight %}

*but*, this doesn't work with a number:

{% highlight javascript %}
123[0]
//=> undefined
{% endhighlight %}

so you have to cast it to a string:

{% highlight javascript %}
('' + 123)[0]
//=> "1"
{% endhighlight %}

and to cast back to a number again: 

{% highlight javascript %}
+('' + 123)[1]
//=> 2
{% endhighlight %}
