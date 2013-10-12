---
layout: post
title: "First Look At ZeroRPC"
description: ""
category: "software"
tags: [zerorpc, zeromq, python, node.js]
---
{% include JB/setup %}

[ZeroRPC](http://zerorpc.dotcloud.com/) is a new open-srouce RPC library released by [dotCloud](http://dotcloud.com/). It's super light weight comparing with other RPC frameworks like [Thrift](http://thrift.apache.org/).

As its name implied, library users can use "zero" effort to implement RPC calls. Sample code looks quite straight forward.

I copy some sample code from official site and add my comments in this post to demonstrate how easy ZeroRPC is.

Start a server and implement a RPC call.

    import zerorpc
    
    # Declare a standard python class and implement
    # an ordinary method. It implies that ZeroRPC
    # is able to expose any existing standard class
    # to remote. It's amazing!
    class HelloRPC(object):
        def hello(self, name):
            return "Hello, %s" % name
    
    # Create a server, bind address and run.
    # There is no extra setting or tuning needed.
    s = zerorpc.Server(HelloRPC())
    s.bind("tcp://0.0.0.0:4242")
    s.run()
{:.language-python}

Call RPC method.                                                                

    import zerorpc
    
    # Create a client and connect to a server.
    c = zerorpc.Client()
    c.connect("tcp://127.0.0.1:4242")
    
    # Call remote method in the way as calling a local
    # method. Surprisingly convenient!
    print c.hello("RPC")
{:.language-python}

I like ZeroRPC's design. Plus it says it's designed to be fast and scalable. I think it's quite worth trying in future.