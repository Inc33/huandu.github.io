---
layout: post
title: "Ubuntu common tasks"
description: ""
category: "software"
tags: [ubuntu, command, hostname, xfs, optimization]
---
{% include JB/setup %}

I'm new to Ubuntu. Here are lots of common tasks I did in Ubuntu. I write this post to help myself to remember how to complete them next time.

### Change host name ###

Use `hostname` to change the name temporarily.

    sudo hostname new-name

Edit `/etc/hostname` to change the name permanently. Just note, it will take effect after restart.

### Install necessary packages ###

Ubuntu doesn't contain any building tool, nor some fundamental library header files. Here is what I need to prepare before starting using open source software.

Install `build-essential`.

    sudo apt-get install build-essential

Install `zlib`.

    sudo apt-get install zlib1g-dev 

Install `PCRE`.

    sudo apt-get install libpcre3 libpcre3-dev

### Format a drive as XFS ###

First, install XFS support.

    sudo apt-get install xfsprogs

Then, use `fdisk` to create new partition. If the disk was mounted, remember to unmount it by `umount`.

    sudo fdisk /dev/sdb

Then, make XFS. Assumes the partition 1 is the newly created partition.

    sudo mkfs.xfs -L the-label /dev/sdb1

Finally, mount this partition to a mount point.

    sudo mount -t xfs /dev/sdb1 /data

Don't forget to edit `/etc/fstab` to mount it at every time server boots.

    echo '/dev/sdb1 /data xfs defaults 0 0' >> /etc/fstab

It's said [XFS performs very bad with write barrier](http://www.mysqlperformanceblog.com/2009/03/02/ssd-xfs-lvm-fsync-write-cache-barrier-and-lost-transactions/). If XFS is used on cloud hard disk, which provides 99.99% availability and 3 hot copies, it may be safe to disable barrier.

    sudo mount -t xfs -o nobarrier /dev/sdb1 /data

There is another article about [XFS optimization](https://pracops.com/wiki/index.php/XFS_optimisation). It ends up with following recommended options.

    # Making the filesystem:
    sudo mkfs.xfs -f -l size=128m,lazy-count=1 filesystem
    
    # Mounting the filesystem:
    sudo mount -o logbufs=8,logbsize=256k,osyncisdsync,barrier,largeio,noatime,nodiratime filesystem

### Resolve "command not found has crashed" ###

I'm using a newly installed Ubuntu 12.04 server. Each time I enter a wrong command, I will see an error message saying.

    Sorry, command-not-found has crashed! Please file a bug report at:
    https://bugs.launchpad.net/command-not-found/+filebug
    Please include the following information with the report:

    command-not-found version: 0.2.44

This error message is quite annoying and totally useless.

Following solution comes from [this post](http://ivaniliev.com/sorry-command-not-found-has-crashed/).

    export LANGUAGE=en_US.UTF-8
    export LANG=en_US.UTF-8
    export LC_ALL=en_US.UTF-8
    locale-gen en_US.UTF-8
    sudo dpkg-reconfigure locales

### Setup `rsync` daemon ###

First, install `rsync` through `apt-get`.

    apt-get install rsync

Here is a full sample coming from [this post](http://www.hartenstine.com/771/install-rsync-on-ubuntu-server-12-04-lts/).

Sample `/etc/rsyncd.conf`:

    motd file = /etc/rsyncd.motd

    [servername]
    path = /home/username
    comment = This is the path to folder on the server
    uid = nobody
    gid = nobody
    read only = false
    auth users = username
    secrets file = /etc/rsyncd.scrt

Sample `/etc/rsyncd.motd`:

    any message you want

Sample `/etc/rsyncd.scrt`:

    username:password

Sample `/etc/default/rsync`:
    
    RSYNC_ENABLE=true
    RSYNC_NICE=’10′
    RSYNC_IONICE=’-c3′

Finally, start `rsync` service.

    sudo service rsync start

### List installed packages ###

Use `dpkg -l` to show all installed packages.

### Fully remove a package ###

`apt-get remove` only removes package content. If this package contains some generated configuration files, `apt-get remove` won't work as expected.

Use `apt-get purge` to remove everything in a package.