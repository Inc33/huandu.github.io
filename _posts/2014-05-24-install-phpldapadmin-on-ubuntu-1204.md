---
layout: post
title: "Install phpldapadmin on Ubuntu 12.04"
description: ""
category: "software"
tags: [ubuntu, phpldapadmin, ldap, trouble shooting, slapd, memberOf]
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

### Unable to use `memberOf` query ###

`slapd` doesn't provide `memberOf` query by default. I need to enable it manually.

I found a blog post about [how to setup `memberOf` overlay](http://www.schenkels.nl/2013/03/how-to-setup-openldap-with-memberof-overlay-ubuntu-12-04/). It works on my Ubuntu box.

We need following two files to enable `memberOf`.

First file `member.ldif`:

	# member.ldif
	dn: cn=module,cn=config
	cn: module
	objectclass: olcModuleList
	objectclass: top
	olcmoduleload: memberof.la
	olcmodulepath: /usr/lib/ldap
	
	dn: olcOverlay={0}memberof,olcDatabase={1}hdb,cn=config
	objectClass: olcConfig
	objectClass: olcMemberOf
	objectClass: olcOverlayConfig
	objectClass: top
	olcOverlay: memberof

And second file `refint.ldif`:

	# refint.ldif
	dn: cn=module,cn=config
	cn: module
	objectclass: olcModuleList
	objectclass: top
	olcmoduleload: refint.la
	olcmodulepath: /usr/lib/ldap
	
	dn: olcOverlay={1}refint,olcDatabase={1}hdb,cn=config
	objectClass: olcConfig
	objectClass: olcOverlayConfig
	objectClass: olcRefintConfig
	objectClass: top
	olcOverlay: {1}refint
	olcRefintAttribute: memberof member manager owner

After created two files, execute following command on LDAP server.

	ldapadd -Y EXTERNAL -H ldapi:/// -f member.ldif
	ldapadd -Y EXTERNAL -H ldapi:/// -f refint.ldif

If everything goes well, we should get following output.

	root@ldap:~# ldapadd -Y EXTERNAL -H ldapi:/// -f member.ldif
	SASL/EXTERNAL authentication started
	SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
	SASL SSF: 0
	adding new entry "cn=module,cn=config"
	
	adding new entry "olcOverlay={0}memberof,olcDatabase={1}hdb,cn=config"
	
	root@ldap:~# ldapadd -Y EXTERNAL -H ldapi:/// -f refint.ldif
	SASL/EXTERNAL authentication started
	SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
	SASL SSF: 0
	adding new entry "cn=module,cn=config"
	
	adding new entry "olcOverlay={1}refint,olcDatabase={1}hdb,cn=config"

Some important notes:

* `memberOf` query only works with User Group (the `groupOfNames`).
* If there is any User Group created before doing `memberOf` setup, the `member` attribute of such User Groups should be updated to make `memberOf` work.