---
layout: post
title:  "Snap - A Universal Package Manager for Linux"
date:   2021-02-17 12:00:04 +0000
categories: snap linux yaml canonical
---

If you have used any kind of Linux you have probably some experience with a package manager. If you have used Linux for a while you will know there are more package managers than distributions. When they start naming package managers "Yet AnOther User Repository Tool" or yaourt (a now discontinued package manager for Arch) it gives the impression that this may be a problem!

If you have package manager is it's simply a tool that is used to install and remove software with the added bonus of managing dependencies. The two most popular package managers are `apt` usually found on debian based distributions and `yum` (Fedora 33 tells me this is now replaced with `dnf` so add another package manager onto the list).

``` sh
yum install vlc
yum update
```

Now you might be wondering, if it's that simple why would I ever want a universal package manager? Well there are a few reason. Packages can be out of date or even non existent. For example, don't use the version of Docker in your distro's default repository. 

Snap was created by Canonical, the company which created and maintained one of the most popular Linux distributions, Ubuntu. Their motivation for creating this project was because there are so many flavours of Ubuntu that it is hard to manage unique pacakges for each distro.

To install things using snap it pretty simple. first install snap

``` sh
yum install snap
```

Then we can simply install 

```
snap install vlc
```

I had some issues with snap. I imagine this is related to using a canonical tool on a non canonical distro. When I ran things with a `snap run vlc` I got a strange font error. However, when I ran the same command using the debugger `snap run --gdb vlc` it would run after continuing a few times. Weirdly, if I uninstalled it and reinstalled the font issue would reappear until I ran it once with the debugger.

So while I'm not ready to use it for my container images it's worth keeping an eye on. Like most things on Linux there is more than one option. A popular alternative is flatpak. I haven't tested this but it may be worth having a look