---
layout: post
title: "Migrate Gitlab to newly installed server"
description: ""
category: "software"
tags: [ubuntu, gitlab, ruby, unicorn, modernizr-rails]
---
{% include JB/setup %}

[Gitlab](https://gitlab.com/gitlab-org/gitlab-ce) is a great git management system with github-like web interface and lots of outstanding features.

I use Gitlab to manage all company git repositories. Git repositories take about 1GB disk space and contain thousands of commits.

Several days ago, due to some reasons, I migrated this Gitlab to a newly installed Ubuntu 12.04 LTS server. Here is a list of issues I had during migration.

### Update `modernizr-rails` gem version ###

It seems the gem `modernizr-rails` doesn't have version `2.6.2` right now. Maybe its author deletes the version. Unfortunately, Gitlab `6.5.1` depends on this gem. It cannot be installed without this gem.

One solution is to use latest `modernizr-rails`. Modify `Gemfile` and `Gemfile.lock` in Gitlab install directory as following.

    --- Gemfile	2014-04-08 02:33:12.945808785 +0800
    +++ /root/backup-internal-server/app-data/git/gitlab/Gemfile	2014-04-05 20:13:39.082892295 +0800
    @@ -151,7 +151,7 @@
     gem 'jquery-atwho-rails', "~> 0.3.3"
     gem "jquery-rails",     "2.1.3"
     gem "jquery-ui-rails",  "2.0.2"
    -gem "modernizr-rails",  "2.7.1"
    +gem "modernizr",        "2.6.2"
     gem "raphael-rails", "~> 2.1.2"
     gem 'bootstrap-sass', '~> 3.0'
     gem "font-awesome-rails", '~> 3.2'
    
    --- Gemfile.lock	2014-04-08 02:33:40.537809761 +0800
    +++ /root/backup-internal-server/app-data/git/gitlab/Gemfile.lock	2014-04-05 20:13:39.086892296 +0800
    @@ -279,7 +279,7 @@
         method_source (0.8.2)
         mime-types (1.25.1)
         minitest (4.7.5)
    -    modernizr-rails (2.7.1)
    +    modernizr (2.6.2)
           sprockets (~> 2.0)
         multi_json (1.8.4)
         multi_xml (0.5.5)
    @@ -604,7 +604,7 @@
       launchy
       letter_opener
       minitest (~> 4.7.0)
    -  modernizr-rails (= 2.7.1)
    +  modernizr (= 2.6.2)
       mysql2
       omniauth (~> 1.1.3)
       omniauth-github

### Increase `unicorn` timeout ###

After I set up and started Gitlab on new server, I found it became super slow and took nearly all CPU time doing something I didn't know.

I checked all Gitlab logs under its `log/` directory. I found there were lots of errors in unicorn log `unicorn.stderr.log` like following.

    E, [2014-04-08T03:48:00.898605 #1061] ERROR -- : worker=0 PID:1485 timeout (31s > 30s), killing

Why nearly all requests cannot finish in 30s? After some investigations, I found Gitlab was trying to build event list for Dashboard page. It iterated all commits in every git repositorie. It could take several minutes to finish it.

I changed the `timeout` setting in `config/unicorn.rb` from `30` to `120`. Once Gitlab succeeded building list, the list was cached for future use. No more expensive operation after that.