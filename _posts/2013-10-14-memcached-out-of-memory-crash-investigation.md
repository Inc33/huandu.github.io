---
layout: post
title: "Memcached Out-Of-Memory Crash Investigation"
description: ""
category: "technical"
tags: [linux, memcached, OOM, crash]
---
{% include JB/setup %}

I have two servers which are running `memcached` for years without any crash. Recently weeks, I see several `memcached` crashed due to out-of-memory killer on these two servers.

After investigation, I find that memory overcommit setting on these two servers is set to 0, which means `malloc()` will always return an address regardless whether there is any available memory. Plus that there are php-cgisand I put more traffic on both of them recent several months. I guess php-cgi processes may consume too much memory and finally cause the crash.

I change [memory overcommit settings](http://man7.org/linux/man-pages/man5/proc.5.html) according to linux man page.

    echo 2 > /proc/sys/vm/overcommit_memory
    echo 80 > /proc/sys/vm/overcommit_ratio

I also reduce php-cgi process max count to a smaller number. Hopefully situation can be better.

P.S. Here is a good article about [how to investigate and fix out-of-memory issue](http://www.hskupin.info/2010/06/17/how-to-fix-the-oom-killer-crashe-under-linux/). It is the top article in google result when I search out-of-memory related keywords. It's fundamental and worth reading.
