---
layout: post
title:  "OSX hosts file craps out on long lines"
date:   2015-07-28 10:27:24
author: Sam Adams
author_avatar: http://pbs.twimg.com/profile_images/624258346107949057/IW0mJXun_200x200.png
categories: mac osx dns hosts
---

## Back Story

*sigh*

So, we use the following technologies:

1. [vagrant] to run our dev environment
1. [ansible] to provision our dev and prod environments
1. Mac OSX as our dev machines (Yosemite - 10.10.4 at time of writing)

Once upon a time, we tried to make our dev environment (vagrant) as 'live like' as possible. We used 
[multiple VMs][multi-machine-vagrant] to represent each server in our production tier. 

So we could be event more 'live like' (and so we didn't need to remember IP addresses) we used [vagrant hostmanager] to 
automatically update our host machine's `/etc/hosts` file with useful hostnames to our vagrant machine IPs, like so:

    ## vagrant-hostmanager-start id: 5586eff5-227f-47em-b6b9-cc7d775d8160
    192.168.24.41	foo1.localhost.com
    192.168.24.42	foo2.localhost.com
    192.168.24.43	foo3.localhost.com
    192.168.24.44	foo4.localhost.com
    192.168.24.45	foo5.localhost.com
    192.168.24.46	foo6.localhost.com
    192.168.24.47	foo7.localhost.com
    192.168.24.48	foo8.localhost.com
    ## vagrant-hostmanager-end
    
This worked fine for a while, and ansible runs worked great; the best thing was being able to blow the machines away and
start fresh with a new environment. 

After adding more and more services, the overhead was too much, and our laptops ran out of resource. As a 
solution, we switched to using a single, bigger Vagrant instance to handle all of our products on one machine.
 
Vagrant hostmanager started writing the following, single-line hostfile:

    ## vagrant-hostmanager-start id: 5586eff5-227f-47em-b6b9-cc7d775d8160
    192.168.24.41	foo1.localhost.com foo2.localhost.com foo3.localhost.com foo4.localhost.com foo5.localhost.com foo6.localhost.com foo7.localhost.com foo8.localhost.com
    ## vagrant-hostmanager-end

## The Problem

That's when we started to have trouble. Ansible was taking ages to complete even simple tasks (e.g. copy config 
files), but only against certain machines.
For far too long we assumed we had done something wrong in the Ansible roles being run against the slow services. 
However, our Ansible code wasn't the problem. It seems there is a bug in OSX with [parsing the hosts file] `/etc/hosts`,  
the result of the bug is that some of the hosts, when many are defined on a single line, can randomly take ~30 seconds to 
resolve the host over SSH.

*sigh again*

## The Solution

Thankfully, the maintainer of Vagrant Hostmanager was kind enough to fix [the issue][hostmanager issue] in version 
1.6.0 (`vagrant plugin update hostmanager`), and now, if you add this to your `Vagrantfile` config:

    config.hostmanager.aliases_on_separate_lines = true

... your hosts file will look like this:

    ## vagrant-hostmanager-start id: 5586eff5-227f-47em-b6b9-cc7d775d8160
    192.168.24.41	foo1.localhost.com
    192.168.24.41	foo2.localhost.com
    192.168.24.41	foo3.localhost.com
    192.168.24.41	foo4.localhost.com
    192.168.24.41	foo5.localhost.com
    192.168.24.41	foo6.localhost.com
    192.168.24.41	foo7.localhost.com
    192.168.24.41	foo8.localhost.com
    ## vagrant-hostmanager-end
    
... and the problem is solved.


[ansible]: http://docs.ansible.com/ansible/index.html
[vagrant hostmanager]: https://github.com/smdahlen/vagrant-hostmanager
[vagrant]: http://docs.vagrantup.com
[multi-machine-vagrant]: http://docs.vagrantup.com/v2/multi-machine/
[parsing the hosts file]: https://github.com/smdahlen/vagrant-hostmanager/issues/60#issuecomment-39119853
[hostmanager issue]: https://github.com/smdahlen/vagrant-hostmanager/issues/60