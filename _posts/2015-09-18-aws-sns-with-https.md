---
layout: post
title:  "HTTPS Endpoints with AWS SNS"
date:   2015-09-18 11:00:00:00
author: ben
categories: AWS SNS 
---
*"Meh, signed certificates, who needs em?"*

AWS SNS apparently...

# Background
During my VPN project I was using AWS's Simple Notification Service to notify my endpoints when changes were made to the mesh.

# For the world to post to... unless...
An unfortunate consequence of this meant that the endpoints needed HTTPS open to [an ever changing list](https://forums.aws.amazon.com/ann.jspa?annID=2347) of IP addresses to receive SNS traffic.

We decided a nice way to get around this would be to use HTTP Basic Auth, which is supported by HTTPS subscriptions (only) to SNS.

Upon attempting to subscribe a HTTPS endpoint to an SNS topic in the AWS console, it appeared to just hang, with no confirmation status showing.

# CA by some file name...
Further research showed that if you want to use HTTPS subscriptions with SNS, you need a valid SSL certificate signed by one of [these CA's](http://docs.aws.amazon.com/sns/latest/dg/SendMessageToHttp.https.ca.html), which are inconveniently displayed in a terrible format, making it super tricky to find any that will be guaranteed to work.

Usually there is a way around this for AWS IP addresses, [AWS publish a list of their current IP addresses and ranges in JSON format](https://ip-ranges.amazonaws.com/ip-ranges.json), however the SNS ones don't seem to be there at time of writing.

Basic auth won't work with HTTP subscriptions, so we've resorted to a security group based off the IPs in that forum post for now, listening on an obscure port, that will break if AWS ever change the SNS IP ranges...
