---
layout: post
title: "Install Symfony on Mac OSX"
description: ""
category: "software"
tags: [mac, symfony, php, brew]
---
{% include JB/setup %}

Mac OSX is not developer-friendly (by default). I have to do many things to make some developer tools or frameworks work.

Today, I'm trying to make [Symfony](http://symfony.com/) work for some reasons. The installation process is quite long.

Here is the list of steps.

**Pre-requisition**

Some basic requirements before start. If you start with a clean Mac OSX, you have to install all pre-requisition software.

1. Install XCode from [Apple Store](https://itunes.apple.com/en/app/xcode/id497799835?mt=12) or [Apple Developer Center](http://developer.apple.com/xcode/).

2. Install XCode Command Line Tools. It can be found in XCode's menu `Preferences -> Download -> Components`. Sometimes XCode will notify you to install it during its first run.

3. Install [HomeBrew](http://brew.sh/).

		ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"

**Install PHP and related tools**

1. Install php. The quickest way is to run following command.

		curl -s http://php-osx.liip.ch/install.sh | bash -s 5.5
	
	Using Homebrew to install php is [incredibly complex](https://github.com/josegonzalez/homebrew-php/blob/master/README.md). So don't use it in this step.
	
	BTW, don't forget to create `/private/etc/php.ini` from default template.

2. Install [PECL](http://pear.php.net/).

		curl http://pear.php.net/go-pear.phar > go-pear.phar
		php -d detect_unicode=0 go-pear.phar
	
3. Install [ICU](http://site.icu-project.org/). PHP extension `intl` depends on it.

		brew install icu4c
	
	**NOTE**: Watch out `brew` output. At the end of the output, `brew` should tell you where the ICU libraries locate like following.
	
		Generally there are no consequences of this for you. If you build your
		own software and it requires this formula, you'll need to add to your
		build variables:
	
	    	LDFLAGS:  -L/usr/local/opt/icu4c/lib
	    	CPPFLAGS: -I/usr/local/opt/icu4c/include
	
		==> Summary
		🍺  /usr/local/Cellar/icu4c/51.1: 235 files, 58M
	
	In this case, the ICU dir is `/usr/local/opt/icu4c/`.

4. Install PHP `intl` extension.

		brew install autoconf
		sudo pecl install intl
	
	`PECL` will ask you ICU dir. Input what you get in last step.

5. Enable `intl` in php.ini. Add following line to extensions section in php.ini.

		extension=intl.so

**Install Symfony**

OK. Finally we can start to install Symfony…

1. Download Symfony [here](http://symfony.com/download).
2. Unpack Symfony to a directory.

Symfony should be able to work so far. Follow Symfony [quick tour](http://symfony.com/doc/current/quick_tour/the_big_picture.html) to play with it.

**Install Symfony Component**

Symfony uses [php composer](http://getcomposer.org/) to install components.

	curl -sS https://getcomposer.org/installer | php