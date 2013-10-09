---
layout: post
title: "Soft assertion on iOS simulator"
description: ""
category: "technical"
tags: [ios, soft assertion, debug]
---
{% include JB/setup %}

I was thinking of how to implement soft assertion due to an annoying bug. Luckily, I find some quite useful solutions ([1](http://stackoverflow.com/questions/37299/xcode-equivalent-of-asm-int-3-debugbreak-halt) [2](http://iphone.m20.nl/wp/2010/10/xcode-iphone-debugger-halt-assertions/)) which really work fine on iOS simulator.

The simplest solution is to execute `__asm__("int $3");`. However, all known solutions only work on iOS simulator now. I cannot find out a cross platform solution.