---
layout: post
title: "Using network bonding to broadcast message"
description: ""
category: "technical"
tags: [ubuntu, linux, network bonding]
---
{% include JB/setup %}

My company rents two apartments as office. Network of these apartments is separated. Each apartment has a standalone internet connection through PPP. It's not possible to route message in one network to another through internet. However, these apartments are quite near to each other - in fact, they are door-to-door. I can use a 10m cable to connect two office network.

I have an internal server as svn/git/local test server. There is a strong need to enable both offices to access the server without any specific setting. People may walk from one to another and want to use exactly the same domain/IP for services.

Here, I use a technology called [network bonding](https://help.ubuntu.com/community/UbuntuBonding) to implement it.

First, I buy a new NIC adapter for the server.

Second, I use two cables to connect the server with both office routers. Of course, one cable is quite long and tricky.

Third, I [set up network bonding](http://www.howtoforge.com/network_bonding_ubuntu_6.10) on the server. I set both NIC address to `192.168.1.2` with `mode=3`. The bonding interface will broadcast all messages over both adapters. As network in two offices is completely separated, broadcasting message will not cause any visible problem, but wasting some traffic.