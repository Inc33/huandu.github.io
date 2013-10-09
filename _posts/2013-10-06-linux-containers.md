---
layout: post
title: "Linux Containers"
description: ""
category: "software"
tags: [lmctfy, lxc, linux, container]
---
{% include JB/setup %}

Several weeks ago, I was looking for a tool to limit a linux process CPU and memory usage, but failed. Today, I suddenly find [Linux Container](http://lxc.sourceforge.net/) or google's newly open-source project [lmctfy](https://github.com/google/lmctfy) may be the tool I want.

In short, the Linux Container can manage (or spawn) process with given CPU and memory quota. It works like a virtual machine, but much lighter.

Linux Container manages system resource, thus it's not possible to run two separated containers at the same time. And if there is any process running outside container consuming moderate system resource, container may be affected.

Linux Container's performance is unknown. I don't know how much resource container will take to manage other processes.