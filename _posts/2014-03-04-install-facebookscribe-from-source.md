---
layout: post
title: "Install facebook/scribe from source"
description: ""
category: "software"
tags: [facebook, scribed, thrift, fb303, hack]
---
{% include JB/setup %}

Facebook [scribe](https://github.com/facebook/scribe) is a distributed log system. It implements a log daemon `scribed` which can receive log message in thrift protocol and write to disk or remote peers per config. It runs on thousands of machines and reliably delivers tens of billions of messages a day in facebook.

I encounter lots of trouble when installing `scribed`. Finally, after several attempts, I find a way to install it without any error on Ubuntu server 12.04.

Here we go.

### Step 1: Install pre-required packages. ###

    apt-get install libboost-dev libboost-test-dev libboost-program-options-dev libboost-system-dev libboost-filesystem-dev libevent-dev automake libtool flex bison pkg-config g++ libssl-dev python-dev

### Step 2: Install latest `thrift`. ###

`scribed` is a [thrift](http://thrift.apache.org/) server. Lastest version of `scribed` (till Mar 4, 2014) depends on `thrift` 0.9.1. I tried to use 0.9.0 but it doesn't compile.

The official `thrift` [0.9.1 package](http://www.apache.org/dyn/closer.cgi?path=/thrift/0.9.1/thrift-0.9.1.tar.gz) doesn't compile on my Ubuntu server. I search for this issue and find it could be a `thrift` bug. So I have to download `thrift` source repository to checkout correct code.

    git clone https://git-wip-us.apache.org/repos/asf/thrift.git thrift-master
    
    cd thrift-master
    
    # NOTE: Both a branch and a tag are named as "0.9.1".
    # If I want to checkout the tag 0.9.1, I have to write
    # use full revision name.
    git checkout refs/tags/0.9.1

    ./bootstrap.sh
    ./configure
    make
    make install
    
    # /usr/local/lib is not in lib search path. i want to
    # change it to /usr/lib, but fb303 hard-coded writes
    # /usr/local/lib in its Makefile.
    # so i have to link .so files manually here. :(
    ln -sf /usr/local/lib/libthrift-0.9.1.so /usr/lib/libthrift-0.9.1.so
    ln -sf /usr/local/lib/libthrift.a /usr/lib/libthrift.a
    ln -sf /usr/local/lib/libthrift.la /usr/lib/libthrift.la
    ln -sf /usr/local/lib/libthriftnb-0.9.1.so /usr/lib/libthriftnb-0.9.1.so
    ln -sf /usr/local/lib/libthriftnb.a /usr/lib/libthriftnb.a
    ln -sf /usr/local/lib/libthriftnb.la /usr/lib/libthriftnb.la
    ln -sf /usr/local/lib/libthriftnbz-0.9.1.so /usr/lib/libthriftnbz-0.9.1.so
    ln -sf /usr/local/lib/libthriftz.a /usr/lib/libthriftz.a
    ln -sf /usr/local/lib/libthriftz.la /usr/lib/libthriftz.la
    ln -sf /usr/lib/libthriftnb-0.9.1.so /usr/lib/libthriftnb.so
    ln -sf /usr/lib/libthrift-0.9.1.so /usr/lib/libthrift.so
    ln -sf /usr/lib/libthriftz-0.9.1.so /usr/lib/libthriftz.so

### Step 3: Install latest `fb303` from `thrift` source. ###

`thrift` contains a sub library called `fb303`. `scribed` depends on `fb303` too.

    # assume we are in thrift-master/
    cd contrib/fb303
    ./bootstrap.sh
    ./configure
    make
    make install

There was a compile error in php binding on my server. I used `--without-php` flag to avoid the error. I don't know whether it's a generic issue.

### Step 4: Install `scribed`. ###

Finally, it's time to install `scribed`. I sufferred a lot in this step due to several `scribed` issues.

First, `configure` script of `scribed` can detect `boost` library's version but cannot detect its correct lib path. I have to set `--with-boost` manually if `boost` is not in `/usr/local`.

Second, as of `boost` 1.46.*, `boost-filesystem` depends on `boost-system`. `--with-boost-system` flag should be set if I set `--with-boost` flag.

Third, making `scribe` will always fail due to a Makefile bug during linking. The only workaround I can see is to do link manually.

    git clone https://github.com/facebook/scribe.git scribe-master
    
    cd scribe-master
    ./bootstrap.sh
    
    # if boost is installed through apt-get, it's located in
    # /usr/include and /usr/lib which are not scribe's default.
    ./configure --with-boost=/usr --with-boost-system=boost_system-mt --with-boost-filesystem=boost_filesystem-mt
    
    # make will always fail. use the workaround solution from
    # https://github.com/facebook/scribe/issues/57
    # this issue is really sucks.
    if ! make; then
        cd src/ &&
            g++  -Wall -O3  -o scribed store.o store_queue.o conf.o file.o conn_pool.o scribe_server.o network_dynamic_config.o dynamic_bucket_updater.o  env_default.o  -L/usr/local/lib -L/usr/local/lib -L/usr/local/lib -lfb303 -lthrift -lthriftnb -levent -lpthread  libscribe.a libdynamicbucketupdater.a  -L/usr/lib -lboost_system-mt -lboost_filesystem-mt &&
        cd -
    fi
    
    make install

That's all. Enjoy `scribed`!
