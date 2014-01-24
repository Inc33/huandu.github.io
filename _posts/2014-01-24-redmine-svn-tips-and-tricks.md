---
layout: post
title: "Redmine SVN Tips and Tricks"
description: ""
category: ""
tags: [redmine, svn, nginx]
---
{% include JB/setup %}

My company uses [redmine](http://www.redmine.org) to manage projects as well as SVN repositories. Here are tips and tricks I encountered in daily uses.

### Fix HTTP 502 error ###

Default Worker MPM in apache is not thread safe. When a SVN repository contains thousands of files and more than one users update/commit to it, users can see HTTP 502 error several times per day. Using Prefork MPM instead of the Worker MPM can fix it.

On Ubuntu, Prefork MPM can be installed by `apt-get`.

    sudo apt-get install apache2-mpm-prefork

### Fix HTTP 500 error ###

One day, I saw following error message in a newly created SVN repository when committing changes. At the same time, I can checkout and update without any problem.

    svn: E175002: Commit failed (details follow):
    svn: E175002: Server sent unexpected return value (500 Internal Server Error) in response to MKACTIVITY request for '/the-svn-name/!svn/act/2ebf0723-deae-4264-99aa-6a1c15bbe8a6'

The solution, in short, is to make sure the SVN repository name is the same as the associated redmine project id.

Redmine can authenticate SVN user using redmine password. However, it only works when SVN repository name is exactly the same as redmine project id.

This behavior is well [documented](http://www.redmine.org/projects/redmine/wiki/Repositories_access_control_with_apache_mod_dav_svn_and_mod_perl) on redmine's official site. I also knew it many years ago. The only thing confuses me is that the SVN and redmine user link status in project management page may show wrong information. Even if it shows a user is linked, redmine still cannot authenticate this user if project id doesn't match SVN repository name.

For instance, I have a redmine project `foo` and an SVN repository `bar`. Both redmine and SVN have a user named `player`. Then, once I add `bar` to `foo`, I can see the `player` is linked automatically. SVN checkout/update does work right in this case. The only thing I cannot do is to commit changes.

### Use Nginx in front of Apache ###

Redmine only provides a built-in apache extension for authentication. I have to use apache to communicate with SVN to link SVN and redmine users. However, nginx is the major front-end in company. I don't want apache to listen on 80 port. Therefore, I setup a reverse proxy in nginx in front of apache.

There is one down side in this solution. If I visit SVN url in browser, I'll be redirected to a url with apache port. For example, if my SVN url is `http://my-svn/` and apache listens port 9080, then I'll see `http://my-svn:9080/` after authentication.

Such redirection is not harmful, just a bit annoying. I search internet and finally find a solution.

The solution requires a small modification in apache's config file.

    PerlLoadModule Apache::Redmine

    Listen 9080
    
    <VirtualHost *:9080>
        # explicitly remove :9080 in returning url.
        RequestHeader edit Destination :9080/ / early
        
        # omit other config entries...
    </VirtualHost>
