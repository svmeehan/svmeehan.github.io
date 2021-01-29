---
layout: post
title:  "Setting up a Docker Swarm on your Local Network"
date:   2021-01-29 12:00:04 +0000
categories: docker swarm firewalld
---

## Setting up Some New Virtual Machines

For my swarm I created a master node on my personal laptop along with two worker nodes running on two VMs hosted on my Desktop PC.

For this I used a minimal CentOS 7 image. I had this on on my PC so I used it.

My laptop is running Fedora Workstation 33. 

If you don't use CentOS you will likely not have the issues I had. On Fedora Workstation the required ports are opened by default.

When setting up your machines in Virtualbox you are going to want to set up a bridged connection. This will assign each of you VMs an IP as if they were connected to your local network. I had assigned my Desktop PC a preferred IP based on it's MAC address and this will cause all VMs to be assigned the same IP. If you end up with an IP

Add docker to this machine

get.docker.com

## Adding machines to the Swarm

Execute the following command on your leader machine *node1* to start the swarm.

``` sh
docker swarm init
```

This will add this machine to the node. It will also print a worker join token to the screen. This command must then be run on the other machines on the network in order for them to join the swarm. I didn't want to manually type this so I created a docker image to host this info.

You can execute the following command to run this:

```
docker run --rm -p 80:80 svmeehan/swarm-share
```

On the other nodes you can now execute:

```
$(curl -s *ip-address-of-docker-host*)
```

I created a custom nginx image to share the join key. This probably doesn't meet any organizational security standards but I'm fine with it on my local network.

use ip addr or ip a to confirm you have a 192.168.*.* ip address. 

So if your virtual machines uses an image with systemd () you will probably be using firewalld. if your distro uses iptables you're on your own.

firewall-cmd --list-all #you can see your active zone here
firewall-cmd --zone=public --permanent --add-port=1025-655365/tcp
firewall-cmd --reload #for changes to take effect

