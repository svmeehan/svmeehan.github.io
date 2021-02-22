---
layout: post
title:  "Snap - A Universal Package Manager for Linux"
date:   2021-02-08 12:00:04 +0000
categories: snap linux yaml canonical
---

If you have used any kind of Linux you have probably some experience with a package manager. If you have used Linux for a while you will know there are more package managers than distributions. When they start naming package managers "Yet AnOther User Repository Tool" or yaourt (a now discontinued package manager for Arch) it gives the impression that this may be a problem!

If you have package manager is it's simply a tool that is used to install and remove software with the added bonus of managing dependencies. The two most popular package managers are `apt` usually found on debian based distributions and `yum` (Fedora 33 tells me this is now replaced with `dnf` so add another package manager onto the list).

``` sh
yum install vlc
yum update
```

Now you might be wondering, if it's that simple why would I ever want a universal package manager? The 

Snap was created by Canonical, the company which created and maintained one of the most popular Linux distributions, Ubuntu. Their motivation for creating this project was because there are so many flavours of Ubuntu ()

A brief explanation of how packages are currently maintained may be of interest. Depending on the flavor of your distribution, you may be used to seeing packages with a .deb (Debian, Ubuntu etc;) or .rpm (RHEL, CentOS, Fedora, etc;)