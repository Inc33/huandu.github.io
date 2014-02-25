---
layout: post
title: "Puppet programming tips"
description: ""
category: ""
tags: []
---
{% include JB/setup %}

[Puppet](http://puppetlabs.com) is a great tool to manage all kinds of config files on servers. I use it to manage all company servers.

Puppet is written in ruby. It can be extended by custom modules and plugins using ruby or its configuration file (the .pp file). It's not difficult to write a workable module or plugins. However I still need to be very careful not to get trapped by puppet pitfalls.

### Improve file syncing performance ###

I have a directory with 7000+ files. When I use `file` resource to sync directory files recursively, puppet will use about 70s to finish it.

Most time is wasted in comparing file md5 sum values in source and destination directory contributes. I can use shell command to `cp` directory to avoid calculating md5 sum.

    exec { "cp $dst_dir":
      command => "rm -rf $dst_dir && cp -prL $src_dir $dst_dir",
      path    => $::path,
      
      # don't execute this command unless $src_dir is changed.
      refreshonly => true,
    }

This `exec` resource is not executed unless it's notified. If any file in source directory is changed, this `exec` resource must be notified.

With this change, puppet can finish syncing directory in a second. The improvement is quite significant.

A side effect: there is no easy way to monitor destination directory file changes. If destination may be changed by scripts other than puppet catalog, it's not easy to detect and fix the change. So this is not a general way to improve `file` resource performance. Use this tip wisely.

### Read class/define arguments from external files ###

Puppet provides a built-in function `file()` to read file  content to string.

Unlike puppet resource runs as root, the `file()` runs as a low privilege user. The user can only read public files. Such files, as well as all of their parent directories, must be readable for everyone.

Another restriction is that `file()` can only read files on puppet master. Thus, only absolute path can be used in this function.

I think it should *not* be used in most cases. Using hiera is more convenient and clean than calling `file()`.

In my own case, I use `file()` to load information from config files managed by git. I write a git hook to extract config files from repository to a public directory for puppet script. Then puppet can read up-to-date config in next run. I use `file()` to gain more control over the config file content, e.g. parsing it with some regular expressions.