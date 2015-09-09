---
layout: post
title:  "Node.js HTTP server won't send response body with status code: 204"
date:   2015-07-10 16:19:24
author: sam
categories: nodejs javascript http
---

Because the http status code [*204* means 'no content'][http-rfc], even if you send body text, it won't be sent with the request:

{% highlight javascript %}
var http = require('http');

http.createServer(function (request, response) {
  response.writeHead(204);
  response.end('Body text');
}).listen(8000);
{% endhighlight %}

{% highlight bash %}
curl http://localhost:8000
[nothing]
{% endhighlight %}

But if the status is *200*:

{% highlight javascript %}
var http = require('http');

http.createServer(function (request, response) {
  response.writeHead(200);
  response.end('Body text');
}).listen(8000);
{% endhighlight %}

it's as expected:

{% highlight bash %}
curl http://localhost:8000
"Body text"
{% endhighlight %}

This is not a complaint but an observation that Node.js is opinionated about status codes and their meaning 
(and I didn't expect it to be).

[http-rfc]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html