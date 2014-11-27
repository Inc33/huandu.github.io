---
layout: post
title: "Traps when using puppet directory environment"
description: ""
category: "software"
tags: [puppet, ubuntu, directory environment, trap]
---
{% include JB/setup %}

Starting with puppet 3.7, it keeps on asking me to use [directory environment](https://docs.puppetlabs.com/puppet/latest/reference/environments_configuring.html) to replace old config file enviroment. As config file environment will be [deprecated in puppet 4](https://docs.puppetlabs.com/puppet/latest/reference/environments_classic.html#config-file-environments-are-deprecated), it's time for me to do migration soon.

Migration process is not smooth (as usual). I list traps here to make sure I will not be blocked when installing new servers (especially puppet master) in future.

### Trap 1: Directory environment won't work without `environmentpath` config ###

I was idiot.

In my case, I use all default settings in puppet except `modulepath`. So I simply rename `modulepath` to `basemodulepath` to get rid of warning. Everything looks good after the change until I add my first directory environment. I spend quite a lot of time to find out why my environment variable doesn't work.

I really hope I would notice the statement that [`environmentpath` is required to enable directory environment feature](https://docs.puppetlabs.com/puppet/latest/reference/environments_configuring.html#environmentpath) earlier...

### Trap 2: Puppet will fail silently if `manifest` path is wrong ###

What will happen if I set a wrong `manifest` path in environment? The answer is nothing to happen.

Puppet may be too smart in this case. When it cannot find manifest directory, it will use the default directory `/etc/puppet/manifests` which is created during installation and empty. So if `manifest` is wrong, all puppet clients will use a totally empty puppet file and do nothing.

As I finished directory environment migration (including resolving trap 1) without touching any config, it was expected to me to see no change after running `puppet agent`. I didn't find any issue until I changed several config files next day. It was quite frustrating to find out why config file changes wouldn't take effect.

Finally I found there was a typo in environment config file. I set a wrong `manifest` directory. It should be `manifests` but I typed `manifest`. Ooops!

### Trap 3: Don't `import` files in the same folder of `site.pp` ###

As soon as I correctly finished directory environment config, I saw following log when I ran `puppet agent`.

    Error: Could not retrieve catalog from remote server: Error 400 on SERVER: Could not parse for environment production: Import loop detected for /path/to/loader.pp at /path/to/site.pp:40 on node FQDN.OF.MY.NODE

Thanks to the error log. I was able to do a quick search on it and found the cause. Puppet master will merge all puppet files in manifest directory as one big file. If I do any `import` in site.pp, I will get this error.

I don't like big puppet file. I can simply create a sub directory in manifest directory and copy all imported files to it. Of course, I need to modify all `import` path.
