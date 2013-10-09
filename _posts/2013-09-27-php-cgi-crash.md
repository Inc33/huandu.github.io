---
layout: post
title: "php cgi Crash"
description: ""
category: "technical"
tags: [php-fpm, bug]
---
{% include JB/setup %}

I have a live game using php-fpm. The php-fpm crashes every 3 ~ 5 days. The root cause is unknown. However, I just find [this bug](https://bugs.php.net/bug.php?id=61558) with similar (but not the same) symptom.

In this bug, it says the config

	catch_workers_output = yes

is account to the crash. Changing the value to `no` can fix it.

I'm trying this now. Let me see whether the issue is fixed.

Update: The issue is not fixed. I'm keeping on investigating. :(