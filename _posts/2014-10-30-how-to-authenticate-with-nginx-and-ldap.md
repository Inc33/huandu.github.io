---
layout: post
title: "How to authenticate with nginx and LDAP"
description: ""
category: "software"
tags: [nginx, ldap, module, ubuntu]
---
{% include JB/setup %}

I use [nginx-auth-ldap](https://github.com/kvspb/nginx-auth-ldap) to do LDAP authentication. Here is a great post [HTTP Authentication with nginx and LDAP](http://www.allgoodbits.org/articles/view/29) with lots of details. I generally follow it and get things done.

The only thing not mentioned in the post is that we need to run `apt-get install libldap2-dev` to install LDAP library before building nginx.

However, due to some reasons, I have to build it with `dpkg` system. It's a bit complex.

I follow [a serverfault answer](http://serverfault.com/questions/227480/installing-optional-nginx-modules-with-apt-get). This answer contains quite enough information to get things done.

The only problem I have is a build error when building nginx binary package. The command `dpkg-buildpackage -b` reports following error.

	dpkg-buildpackage: source package nginx
	dpkg-buildpackage: source version 1.6.2-4+precise0
	dpkg-buildpackage: source changed by Thomas Ward <teward@dark-net.net>
	dpkg-buildpackage: host architecture amd64
	 dpkg-source --before-build nginx-1.6.2
	dpkg-checkbuilddeps: Unmet build dependencies: libgd2-dev | libgd2-noxpm-dev
	dpkg-buildpackage: warning: Build dependencies/conflicts unsatisfied; aborting.
	dpkg-buildpackage: warning: (Use -d flag to override.)

It requires `libgd2-dev` or `libgd2-noxpm-dev` to continue. OK, I just install `apt-get install libgd2-noxpm-dev` to fix it.
