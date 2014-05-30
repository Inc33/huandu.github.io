---
layout: post
title: "Redmine LDAP integration with slapd"
description: ""
category: "software"
tags: [redmine, ldap, ubuntu, slapd]
---
{% include JB/setup %}

There is [an official guide](http://www.redmine.org/projects/redmine/wiki/RedmineLDAP) talking about redmine LDAP integration. I use the recommended way in this guide to configure LDAP, but it doesn't work.

After a few investigation, I find the root cause is that `Login attribute` field in `Attributes` section should be `cn` instead of `sAMAccountName`. Maybe `sAMAccountName` is only valid for Microsoft Active Directory.

Following is my configuration working with `slapd` on a Ubuntu 12.04 box.

	Name:        Company Name
	Host:        your.ldap.domain
	Port:        389
	Account:     cn=$login,ou=users,dc=your,dc=ldap,dc=domain
	Password:    ******
	Base DN:     ou=users,dc=your,dc=ldap,dc=domain
	LDAP filter: (&(objectClass=posixAccount)(memberOf=cn=redmine,ou=groups,dc=your,dc=ldap,dc=domain))
	
	On-the-fly user creation: yes
	
	Attributes
		Login attribute:      cn
		First name attribute: givenName
		Last name attribute:  sn
		Email attribute:      mail

Some notes:

* `cn` must be a valid redmine username, which cannot contain any space. So use `cn=huandu` instead of `cn=Huan Du`.
* `ou=users` part may need to be changed. I write it because I put all redmine users under the `ou=users` directory.
* `memberOf=...` part depends on how User Group is configured. In my LDAP server, I use User Group `cn=redmine,ou=groups` to include all redmine users. Note that `slapd` doesn't enable `memberOf` overlay by default, read my previous post to find out [how to enable `memberOf` overlay](http://huandu.github.io/software/2014/05/24/install-phpldapadmin-on-ubuntu-1204/).
