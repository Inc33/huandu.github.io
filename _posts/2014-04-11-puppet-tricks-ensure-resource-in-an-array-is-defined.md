---
layout: post
title: "Puppet trick: Ensure resource in an array is defined"
description: ""
category: "technical"
tags: [puppet, iteration, resource]
---
{% include JB/setup %}

Puppet language is a config language. There is no `for` loop unless puppet's future parser is enabled. I don't want to discuss future parse here. I want to introduce a trick to resolve a recent issue related to iteration.

What I want to do is to ensure an array of resource is defined.

For instance, I have several groups `group1`, `group2`, etc. Group name can be any valid name. I need to ensure all groups are created.

    $groups1 = [group1, group2]
    group { $groups1:
        ensure => present,
    }
    
    # group1 is defined twice.
    $groups2 = [group1, group3]
    group { $groups2:
        ensure => present,
    }

Above code doesn't work as both `$groups1` and `$groups2` have `group1`.

Use `defined()` function can check if one resource is defined or not, but it doesn't work with array. A practical way (my trick) is to create a helper resource to iterate all resources in an array.

    define group_helper (
        $groups,
        $index  = 0,
    ) {
        include stdlib
        
        if $index < size($groups) {
            $group = $groups[$index]
            
            if !defined(Group[$group]) {
                group { $group:
                    ensure => present,
                }
            }
        
            # define next resource in the array.
            # note that the resource name must be unique.
            group_helper { "group_helper::${index}::${size}::${name}":
                groups => $groups,
                index  => $index + 1,
            }
        }
    }

With this helper resource, define groups can be very simple and safe.

    $groups1 = [group1, group2]
    group_helper { "groups1":
        groups => $groups1,
    }
    
    $groups2 = [group1, group3]
    group_helper { "groups2":
        groups => $groups2,
    }