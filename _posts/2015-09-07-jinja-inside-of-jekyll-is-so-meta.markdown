---
layout: post
title:  "Markup Matryoshka: Jinja2 inside of Jekyll"
date:   2015-09-07 23:27:20
author_avatar: https://avatars0.githubusercontent.com/u/982046?v=3&s=460
author: Scott Cunningham
categories: recursion markdown jinja2 jekyll meta
---

Have you ever tried to write a Jekyll blog post with Jinja2 code inside of it? You probably wrote something like this:

{% highlight awk %}

{% raw %}
{% highlight awk %}
This Jinja2 code is going to be soooooooooo {{ adjective }}
{% endhighlight %}
{% endraw %}

{% endhighlight %}

Which probably actually rendered to something like this:

{% highlight awk %}

{% raw %}
{% highlight awk %}
This Jinja2 code is going to be soooooooooo
{% endhighlight %}
{% endraw %}

{% endhighlight %}

It's going to be soooooo? Sooooo what?!

![So what?](https://media.giphy.com/media/QbWlCK9lbKoUM/giphy.gif)

Solution: just use the {{ "{% raw " }}%} and {{ "{% endraw " }}%} tags:

{% highlight yaml %}

This Jinja2 code is going to be soooooooooo {{ "{% raw" }} %} {% raw %} {{ adjective }} {% endraw %} {{ "{% endraw" }} %}

{% endhighlight %}

Now, how did we insert inline {{ "{% raw " }}%} and {{ "{% endraw " }}%} tags without Jekyll choking on them? You'll
just have to check out the source for this post on our [GitHub](https://github.com/twilblog/twilblog.github.io/).
