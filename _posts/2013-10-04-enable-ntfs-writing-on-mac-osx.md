---
layout: post
title: "Enable NTFS writing on Mac OSX"
description: ""
category: "technical"
tags: [mac, ntfs, fstab]
---
{% include JB/setup %}

NTFS disk is not writable by default on Mac, but there is an (experimental?) feature on Mac OSX to enable writing. [This article](http://reviews.cnet.com/8301-13727_7-57588773-263/how-to-manually-enable-ntfs-read-and-write-in-os-x/) explains how to enable it.

Just note following.

- /etc/fstab doesn't exist by default on Mountain Lion. It needs to be created.
- There may be a file /etc/fstab.hd existing on some Mac computers. It's NOT the one we need to edit. Ignore it.
- In /etc/fstab, replace NAME referred in article to the NTFS disk name. Don't forget it.