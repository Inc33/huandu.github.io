---
layout: post
title: "Use `tput` to change terminal style"
description: ""
category: "software"
tags: [tput, linux]
---
{% include JB/setup %}

Use `tput` to change terminal style

In the past, I usually use `\033[0;1m` and `\033[m` pair to change terminal text color. It works but sometimes annoying. In my Mac Terminal, use escaped color can cause Terminal fail to show wrapped command lines and buggy when show bash history. `tput` works perfect in any case. Plus, it's more powerful and readable.

See its man page for more details. 