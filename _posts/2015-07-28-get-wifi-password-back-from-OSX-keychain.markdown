---
layout: post
title:  "How to get a Wi-Fi password back out of OSX"
date:   2015-07-28 13:27:24
author: Sam Adams
author_avatar: http://pbs.twimg.com/profile_images/624258346107949057/IW0mJXun_200x200.png
categories: mac osx wifi
---

Every wanted to give someone a wifi password for a network you have previously connected to?

Well, if you are on OSX, and using the keychain to store you wifi passwords, here is how:

First, open the keychain manager:

    open /Applications/Utilities/Keychain\ Access.app
 
Then:

1. Select category 'Passwords'
1. Type the name of the network in the top right
1. Select the entry you want
1. Click 'copy' on the bar the the bottom and you will see an authentication dialogue:
  - If you selected a 'system' password, you will also have to enter the full name 
  (not your unix username - i.e. 'Sam Adams' not 'sam') and password of an administrator
  - Alternatively, if you select an 'iCloud' password, you have to enter the current, iCloud user's password
1. Paste the password into a plain text editor to see it (or send via whatever medium)

![password from keychain](/images/password-from-keychain.png)
