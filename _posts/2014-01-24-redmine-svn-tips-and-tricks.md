---
layout: post
title: "Redmine SVN Tips and Tricks"
description: ""
category: "software"
tags: [redmine, svn, nginx, apache2, prefork]
---
{% include JB/setup %}

My company uses [redmine](http://www.redmine.org) to manage projects as well as SVN repositories. Here are tips and tricks I encountered in daily uses.

### Fix HTTP 502 error ###

In my company, I use nginx as reverse proxy in front of apache. I can see several 502 errors nearly everyday.

Using Prefork MPM instead of the Worker MPM can fix it. Default Worker MPM in apache is not thread safe. When a SVN repository contains thousands of files and more than one users update/commit to it, users can see HTTP 502 error several times per day.

On Ubuntu, Prefork MPM can be installed by `apt-get`.

    sudo apt-get install apache2-mpm-prefork

Note that apache is installed with Workder MPM by default unless php is installed. So it could be confusing that redmine svn works well on some machines but not well on others.

### Fix svn COPY request 502 error ###

The root cause of this 502 error is the same as other 502 error. Installing Preform MPM can resolve it.

I highlight this specific issue here to remind myself. When I saw this issue for the first time, I tried to look for solutions over internet. All I got was to change `Destination` HTTP header or set correct `ServerAlias` in apache config. Such solutions might help in many cases except my issue.

If using svn with redmine extension (the Redmine.pm), don't forget to install apache with Prefork MPM.

### Fix HTTP 500 error ###

One day, I saw following error message in a newly created SVN repository when committing changes. At the same time, I can checkout and update without any problem.

    svn: E175002: Commit failed (details follow):
    svn: E175002: Server sent unexpected return value (500 Internal Server Error) in response to MKACTIVITY request for '/the-svn-name/!svn/act/2ebf0723-deae-4264-99aa-6a1c15bbe8a6'

The solution, in short, is to make sure the SVN repository name is the same as the associated redmine project id.

Redmine can authenticate SVN user using redmine password. However, it only works when SVN repository name is exactly the same as redmine project id.

This behavior is well [documented](http://www.redmine.org/projects/redmine/wiki/Repositories_access_control_with_apache_mod_dav_svn_and_mod_perl) on redmine's official site. I also knew it many years ago. The only thing confuses me is that the SVN and redmine user link status in project management page may show wrong information. Even if it shows a user is linked, redmine still cannot authenticate this user if project id doesn't match SVN repository name.

For instance, I have a redmine project `foo` and an SVN repository `bar`. Both redmine and SVN have a user named `player`. Then, once I add `bar` to `foo`, I can see the `player` is linked automatically. SVN checkout/update does work right in this case. The only thing I cannot do is to commit changes.
