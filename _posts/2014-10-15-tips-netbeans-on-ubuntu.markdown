---
author: gualtierotesta
comments: true
date: 2014-10-15 19:00:39+00:00
layout: post
link: https://gualtierotesta.wordpress.com/2014/10/15/tips-netbeans-on-ubuntu/
slug: tips-netbeans-on-ubuntu
title: 'Tips: NetBeans on Ubuntu'
wordpress_id: 323
categories:
- Netbeans
- Tools
tags:
- debian
- installation
- java
- jdk
- jre
- linux
- netbeans
- oracle
- ubuntu
---

Ubuntu Linux is a very nice software development platform and NetBeans fit very well in it.

Nevertheless few tips can make our developer life easier. These tips can be applied to other Linux distributions.

**1. Do not use apt-get**

Apt-get is the Ubuntu/Debian tool to download and install programs from the repositories.

It should not be used to install the tools you need for development, NetBeans included.

Reasons:



	
  * Repositories contains old versions

	
  * Dependencies force you to install not needed or incompatible software


Get installation files from the main sources and install them manually in a common dir like /opt.

**2. Install NetBeans on a user writable dir**

NetBeans needs to update files in its own installation directory during software updates. I suggest to use /opt as the main installation dir.

If /opt is now user writable, execute the following command

[code language="bash"]sudo chmod 777 /opt```

**3. Watch the /tmp size**

NetBeans use /tmp for temporary operations like, for example, updating Central Maven repository information. While doing this update, NetBeans saves big temporary dirs under /tmp and the update fails if /tmp has no space available.

The /tmp dir is usually small when it is mounted on a dedicated partition.

Reserve at least 1.5GByte free space on the /tmp.

See [Bug 162313](https://netbeans.org/bugzilla/show_bug.cgi?id=162313http://) for more details.

**4. Save NetBeans cache in RAM**

NetBeans saves configuration, user preferences and project files status in the NetBeans user dir which is located in $HOME/.netbeans/<version>.

Part of this information, mainly related to projects is considered to be a cache (= it can be recreated if deleted); the cache can be located in a separated dir with the command line --cachedir.

With at least 4 GByte RAM, we can map the cache dir to a temporary RAM on disk using Ubuntu native ram filesystem.

My preferred approach is to

- mount the /tmp to a tmpfs type filesystem, by adding the following line in the /etc/fstab


[code]tmpfs /tmp tmpfs defaults,noatime,nosuid,nodev,noexec,mode=1777,size=2048M 0 0```


This will “map” the /tmp dir into RAM up to 2GByte (see tip #3).

- map the cachedir to a /tmp/ subdir, by creating a NetBeans invocation script as the following:

[code language="bash"]#!/bin/sh
export VERSION=netbeans-8.0.1
export RAMDISK=/tmp/$VERSION
export OPTIONS=" --cachedir $RAMDISK --laf Nimbus "
/opt/$VERSION/bin/netbeans $OPTIONS &
```

The script will invoke NetBeans with the appropriate options.

Note: I like Nimbus on Ubuntu Unity user interface.

**5. Use a SSD drive**

SSD drives are an impressive speed improvement. Software development requires reading and writing hundred of files. Speed is essential to have quick feedback during our test and compilations cycles.

The SSD drive can be your main hard drive or an external one (possibly on a fast USB connections). Just place both NetBeans installation dir and your projects files on the SSD unit.

Remember to add _noatime_ option on the mount instructions (see /etc/fstab) to avoid useless write access to the disk.
