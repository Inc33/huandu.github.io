---
layout: post
title: "Gitlab LDAP integration with slapd"
description: ""
category: "software"
tags: [ubunt, gitlab, ldap, slapd]
---
{% include JB/setup %}

I'm using Gitlab 6.5.1 in my company. Per [Gitlab's official LDAP guide](https://gitlab.com/gitlab-org/cookbook-gitlab/blob/master/doc/open_LDAP.md), it can be integrate with LDAP in a few steps.

The most important part in the guide is to modify following lines in `config/gitlab.yml` to correct LDAP value.

	  ## LDAP settings
	  ldap:
	    enabled: true
	    host: 'gitlab.dev'
	    base: 'dc=gitlab,dc=dev'
	    port: 389
	    uid: 'uid'
	    method: 'plain' # "ssl" or "plain"
	    bind_dn: 'dc=gitlab,dc=dev'
	    password: 'gitlabldap'
	    
	    # custom filter. it doesn't exist by default.
	    user_filter: '(memberOf=cn=gitlab,ou=groups,dc=gitlab,dc=dev)'

Gitlab can set LDAP filter with `user_filter`, though it's not documented in official guide nor in the example file.

Make sure every LDAP user, who wants to use Gitlab, has a `mail` attribute. User cannot login without this attribute.

Once LDAP is correctly setup, all existing users can be mapped to LDAP user if both username and email match those of LDAP user.

Note that a Gitlab can use both LDAP password and Gitlab standard password for login. There is no way to disable standard login without modifying Gitlab code.

Here is a patch to [disable standard sign in](https://github.com/alerque/gitlabhq/commit/e02413da0339e1620d28335badd3c6f3eee10e8c). I tried it and worked fine for me. However, Gitlab team doesn't want to accept this patch per [issue discussion](Disable regular auth and only allow LDAP?). Their justification is unclear to me.