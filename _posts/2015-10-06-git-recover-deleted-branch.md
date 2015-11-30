---
layout: post
title:  "Recovering a deleted merged Git branch"
date:   2015-11-30 12:00:00:00
author: shaun
categories: git 
---

# The Problem

After merging a pull request, you deleted a branch from Git, both locally and remotely, and you want it back.

# The Solution

Run `git log` to see all commits made in reverse order, and find the one where the pull request was merged originally.

(% highlight bash %}
commit <very long SHA here>
Merge:  <more SHA stuff>
Author: Hermione Granger <hgranger@ministryofmagic.uk>
Date:   Thu Nov 26 10:09:00 2015 +0000
 
   Merged in branch-to-recover (pull request #100)
   
   
(% endhighlight %}

Copy the long SHA from the top of the entry (it looks something like `commit j45yf2yu45fuy5f3u4yt2f5acbdfdcdcdbfacdbfbfc425u`).

Then run `git checkout -b recover-branch <SHA you copied earlier>`. You'll check out a new branch named `recover-branch`, based
off the SHA where the pull request was merged.

Push this to the remote with `git push -u origin recover-branch`, and your branch is back, both locally and remotely.
