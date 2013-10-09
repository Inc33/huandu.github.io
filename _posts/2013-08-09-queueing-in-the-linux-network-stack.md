---
layout: post
title: "Queueing in the Linux Network Stack"
description: ""
category: "technical"
tags: [linux, network stack, queue]
---
{% include JB/setup %}

[This article](http://www.coverfire.com/articles/queueing-in-the-linux-network-stack/) explains how different queues work in the Linux network stack.

A funny fact is that queue length is not the larger the better. If queue is too long, latency will be increased. Sometimes, especially in some performance critical situation, reporting queue full error to upper layer is better than caching all coming data in internal buffer. With a suitable queue length, upper layer can manage sending pace and package priority better. For instance, a well-tuned upper layer can slow down large file transmission and let small but more urgent packages go first.

This article refers several important concept in Linux kernel and network stack, like BQL (Byte Queue Limits), Bufferbloat, QDisc, etc. It's worth reading more than once once I have more understanding in this area.