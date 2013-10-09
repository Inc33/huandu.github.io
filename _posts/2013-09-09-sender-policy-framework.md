---
layout: post
title: "Sender Policy Framework"
description: ""
category: "technical"
tags: [spf, dns, spam]
---
{% include JB/setup %}

[Sender Policy Framework (SPF)](http://en.wikipedia.org/wiki/Sender_Policy_Framework) is a technology (or spec) in DNS protocol to prevent unauthorized MX usage, a.k.a spam-free.

SPF is a text-like field in DNS. Mail service providers (e.g. gmail) can read this field to verify sender. A typical SPF record looks like following (this sample comes from wikipedia):

	seminar-for-you.ru.        14400   IN      TXT     "v=spf1 a mx ip4:55.11.65.20/2 ip4:90.2.123.112/2 ip4:176.33.87.19/2 ip4:212.63.89.33/2 -all"
