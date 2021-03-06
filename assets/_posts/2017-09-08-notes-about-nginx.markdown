---
layout: post
title: "Notes about NGINX"
date: 2017-09-08T00:10:03+05:00
description: Learn how to install, set up, & configure NGINX web server & reverse proxy in NGINX.
keywords: NGINX
comments: true
categories: NGINX
published: false
---

# The basics: terminology

* [TCP VS UDP][difference-between-tcp-and-udp]

  When you click a link, sign in, post a comment, or do anything else, your web browser sends TCP packets to the server and the server sends TCP packets back.

  _TCP(pickup/dropoff)_ : Connection-oriented protocol, packets sent with TCP are tracked and automatic retransmission so no data is lost or corrupted in transit.

  _UDP_ : Connectionless protocol, packets are just sent to the recipient. The sender doesn’t wait to make sure the recipient received the packet—it just continues sending the next packets. If the recipient misses a few UDP packets here and there, they are just lost—the sender won’t resend them.

  Think of TCP as a dedicated scheduled UPS/FedEx pickup/dropoff of packages between two locations, while UDP is the equivalent of throwing a postcard in a mailbox. UPS/FedEx will do their damndest to make sure that the package you mail off gets there, and get it there on time. With the post card, you're lucky if it arrives at all, and it may arrive out of order or late (how many times have you gotten a postcard from someone AFTER they've gotten home from the vacation?)



    ![Python Data-Types]({{ site.url }}/static/img/_posts/learning-nginx/001.png)


[difference-between-tcp-and-udp]:      https://stackoverflow.com/questions/5970383/difference-between-tcp-and-udp