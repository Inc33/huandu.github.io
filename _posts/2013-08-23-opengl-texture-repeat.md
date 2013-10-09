---
layout: post
title: "OpenGL Texture Repeat"
description: ""
category: "technical"
tags: [opengl, texture]
---
{% include JB/setup %}

Use [`glTexParameteri()`](http://www.opengl.org/sdk/docs/man/xhtml/glTexParameter.xml) set GL_TEXTURE_WRAP_S/GL_TEXTURE_WRAP_T/GL_TEXTURE_WRAP_R to GL_REPEAT and make texture rectangle to a value larger than texture itself. It can make texture repeat.