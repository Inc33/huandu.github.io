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

And another trifle difference is that `memberOf` query doesn't work in `slapd`. I have to use `gidNumber`, which is not readable at all.

Following is my configuration working with `slapd` on a Ubuntu 12.04 box.

	Name:        Company Name
	Host:        your.ldap.domain
	Port:        389
	Account:     cn=$login,ou=users,dc=your,dc=ldap,dc=domain
	Password:    ******
	Base DN:     ou=users,dc=your,dc=ldap,dc=domain
	LDAP filter: (&(objectClass=posixAccount)(gidNumber=501))
	
	On-the-fly user creation: yes
	
	Attributes
		Login attribute:      cn
		First name attribute: givenName
		Last name attribute:  sn
		Email attribute:      mail

Some notes:

* `cn` must be a valid redmine username, which cannot contain any space. So use `cn=huandu` instead of `cn=Huan Du`.
* `ou=users` part may need to be changed. I write it because I put all redmine users under the `ou=users` directory.
* `gidNumber=501` part means all users in a group which gid is 501 are valid for redmine. As I cannot make `memberOf` query work with `slapd`, I use gid number directly as a workaround.
