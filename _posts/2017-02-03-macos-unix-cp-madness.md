---
layout: post
title:  "macOS and Linux Treat 'cp' Differently"
date:   2017-02-03 12:00:00:00
author: shaun
categories: macOS Linux cp copy
---

I'm using an [NPM script](https://css-tricks.com/why-npm-scripts/)
for building a project. I develop on macOS, but my production builds are run by
Jenkins on a Linux server.

The build script copies the contents of one directory to another. When I run it locally,
the `cp` command copies the contents but not the directory itself,
which is what I want to happen.

However, when Jenkins creates a build the directory itself is copied. Here's an example:

{% highlight bash %}
# macOS. The contents of the folder are copied.

$ ls source
one   two   three

$ cp -a source/ destination/

$ ls destination
one   two   three

# Linux. The folder itself is copied.

$ ls source
one   two   three

$ cp -a source/ destination/

$ ls destination
source/
{% bash %}

Weird! Fortunately the solution is very simple, just add a `.`:

{% highlight bash %}
# macOS. The contents of the folder are copied.

$ ls source
one   two   three

$ cp -a source/. destination/

$ ls destination
one   two   three

# Linux. The contents of the folder are copied.

$ ls source
one   two   three

$ cp -a source/. destination/

$ ls destination
one   two   thee
{% bash %}
