---
layout: post
title:  "Starting a Jekyll Blog"
date:   2015-07-10 11:47:24
categories: jekyll update
---

This blog runs on [Jekyll](http://jekyllrb.com/). Here's the steps we took to get it up and running.

## Step One - Make your repo

Go to Github and [make a new organisation](https://help.github.com/articles/creating-a-new-organization-from-scratch/).
Make a new repo, named like *orgname.github.io*. For us, that's [twilblog.github.io](https://github.com/twilblog/twilblog.github.io).

## Step Two - Clone it

On your computer, clone your new repo:

{% highlight bash %}
git clone git@github.com:orgname/orgname.github.io.git
{% endhighlight %}

`cd` into the new folder that was created.

## Step Three - Jekyll

[Get Jekyll installed](http://jekyllrb.com/docs/installation/), then start a new Jekyll blog:

{% highlight bash %}
# The 'dot' makes it use the current folder.
jekyll new .
{% endhighlight %}

You've now got a Jekyll site, woo!

## Step Four - Commit it

Commit your blog and push it up to Github:

{% highlight bash %}
git add -A
git commit -m "I heart Jekyll"
git push -u origin master
{% endhighlight %}

Now, your blog's been pushed to Github.

*"But wait!"* you say, *"I didn't run `jekyll build` or anything so Github's just going to have the uncompiled source,
what's the deal with that?"* you continue.

Don't worry. Github is pretty cool with this stuff - it detects you've got a Jekyll blog, runs the build process for you, then
serves up the compiled `_site` directory on root path of *https://orgname.github.io*. Go to your newly created github.io page
and try it.

Although, note that this only works on the `master` branch. The whole setup is pretty cool because Github is essentially
acting like a CI server that builds and pushes your code live automatically.

## Step Five - Make it less boilerplatey

At this point your site is still just the default Jekyll site. The [Jekyll docs](http://jekyllrb.com/docs/home/)
have plenty of info about how to change it to suit you.