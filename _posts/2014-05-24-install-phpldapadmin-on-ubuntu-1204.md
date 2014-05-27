---
layout: post
title: "Install phpldapadmin on Ubuntu 12.04"
description: ""
category: "software"
tags: [ubuntu, phpldapadmin, ldap, trouble shooting]
---
{% include JB/setup %}

There is a great installation guide: [How To Install and Configure a Basic LDAP Server on an Ubuntu 12.04 VPS](https://www.digitalocean.com/community/articles/how-to-install-and-configure-a-basic-ldap-server-on-an-ubuntu-12-04-vps). I can finish most work just follow this guide. I don't want to repeat installation steps here. I just list some special problems I encountered.

### Error: Fail to contact LDAP server ###

After successfully installed and configured phpldapadmin, I tried my first login to the admin console. The login was successful, but I see an error message.

	Fail to contact LDAP server

The most common cause is that one phpldapadmin config is wrong.

	// if "my.ldap.server" is not wrong, we'll see that error.
	$servers->setValue('server','host','my.ldap.server');

Unfortunately, it was not the cause in my case. My host name was correct. I also tried to write full LDAP url like `ldap://my.ldap.server:389`. It didn't work either.

After a few investigations, I found LDAP access was blocked by firewall. The port 389 was not open for public IP. LOL

### Error: This base cannot be created with PLA ###

I also saw following error after installation.

	dc=example,dc=com 
	This base cannot be created with PLA. 

The root cause was that phpldapadmin failed to detect LDAP configurations in `apt-get install` script. It should be a rare issue according to phpldapadmin FAQ.

Resolved this issue by modifying following line in phpldapadmin `/etc/phpldapadmin/config.php`.

	// modify it to a correct value.
	$servers->setValue('server','base',array('dc=example,dc=com'));


To be continued...