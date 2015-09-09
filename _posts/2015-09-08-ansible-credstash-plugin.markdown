---
layout: post
title:  "Ansible secrets: death to Vault, long live Credstash!"
date:   2015-09-07 22:03:24
author: scott
categories: ansible credstash secrets
---

I've been using Ansible for a while now. It's a great tool -- it's quick, pretty intuitive and, most importantly, 
allows you to build up and tear down services on top of a clean slate pretty rapidly.

One thing, however, that I'm not a huge fan of is <a href="http://docs.ansible.com/ansible/playbooks_vault.html" 
target="_blank">Ansible Vault</a>. Ansible Vault is used for encrypting files full of variables that you mightn't want 
to store in cleartext anywhere -- API keys for PagerDuty, SSH keys that you use to pull code from GitHub, or something 
similar.

While Vault works fine for many use cases, I find it a bit cumbersome to use at times: you've got to encrypt entire 
variable files at once. That includes your unencrypted variables too, unless you store those in separate files. This 
means that you need to decrypt and re-encrypt every time that you want to change a variable, and makes git diffs obtuse 
and difficult to parse. For example, instead of seeing:

{% highlight yaml %}
---
my_nice_number: 123
actually_a_password_lol: XXXXXXX
my_nicer_list:
+  - 123
   - 234
   - 345
{% endhighlight %}

You see this awful mess:

{% highlight yaml %}
$ANSIBLE_VAULT;1.1;AES256
- 21341203481230498123049128340918234123471234912308471239048729
34987234908712349012387412390847123904871234091238741239048712
34901238741239084712940812749012387412390487123490874234123049
+ 2123412341234123481293401298340109821340-239841290387419023879
2340982340123490871234908712OMG0BINARY0IS0SO0OBTUSE09319048723
328459675342985679823475689723465897432KILLMENOWPLS89762348976
{% endhighlight %}

Aaaaaargh!

Then I found a pretty handy utility called <a href="https://github.com/LuminalOSS/credstash">Credstash</a>.
It's a small utility that retrieves passwords stored in Amazon's 
<a href="http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html">DynamoDB</a> based on user 
credentials. Basically, certain users/roles are given access to a decryption key in Amazon 
<a href="https://aws.amazon.com/kms/">KMS</a> and Credstash is a nice wrapper which bundles that all together into a 
nice command-line tool and Python library which encrypts and decrypts passwords for you. In their own words:

> CredStash is a very simple, easy to use credential management and distribution system that uses AWS Key Management
> Service (KMS) for key wrapping and master-key storage, and DynamoDB for credential storage and sharing.

Nifty.

I liked Credstash, so I decided to use it with Ansible. I implemented a pretty basic Ansible 
<a href="http://docs.ansible.com/ansible/playbooks_lookups.html">lookup plugin</a> for it. With it, and a little bit of 
set-up, you can dynamically look up secrets via Ansible like this:

{% highlight yaml %}
- name: "Test credstash lookup plugin -- get the secret password"
  debug: msg="Credstash lookup! {% raw %} {{ lookup('credstash', 'my-password') }} {% endraw %}"
{% endhighlight %}

Or just put it straight in your variables file like before:

{% highlight yaml %}
---
my_nice_number: 123
actually_a_password: {% raw %} "{{ lookup('credstash', 'my-password') }} {% endraw %}"
my_nicer_list:
   - 123
   - 234
   - 345
{% endhighlight %}
Not bad!

I raised a <a href="https://github.com/ansible/ansible/pull/11778">pull request</a> against Ansible adding the 
functionality and happily, they merged it. Nice! 😎
