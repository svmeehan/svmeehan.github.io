---
layout: post
title:  "Setting up a Docker Swarm on your Local Network"
date:   2021-02-08 12:00:04 +0000
categories: docker swarm firewalld
---

## Setting up Some New Virtual Machines

For my swarm I created a master node on my personal laptop along with two worker nodes running on two VMs hosted on my Desktop PC.

For this I used a minimal CentOS 7 image. I had this on on my PC so I used it.

My laptop is running Fedora Workstation 33. 

If you don't use CentOS you will likely not have the issues I had. On Fedora Workstation the required ports are opened by default.

When setting up your machines in Virtualbox you are going to want to set up a bridged connection. This will assign each of you VMs an IP as if they were connected to your local network. I had assigned my Desktop PC a preferred IP based on it's MAC address and this will cause all VMs to be assigned the same IP. If you end up with an IP

## Add docker to this machine

Installing Docker is actually very simple these days. Usually, you won't want to use your package manager's repo. To ensure you get the latest version check out the script at [get.docker.com](http://get.docker.com).

Run it in your terminal with:

``` sh
$(curl -s get.docker.com)
```
or as recommended in the script
``` sh
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

## Adding machines to the Swarm

Execute the following command on your leader machine *node1* to start the swarm.

``` sh
docker swarm init
```

This will add this machine to the node. It will also print a worker join token to the screen. This command must then be run on the other machines on the network in order for them to join the swarm. I didn't want to manually type this so I created a docker image to host this info.

I found the most efficient way to get nodes to join was to host the join key on a webserver on the first node. To do this we run a few linux commands on the initialized node:

``` sh
docker swarm join-token worker | sed 1d > index.html #use manager in place of worker if desired
```

The sed in the above command just removes the first line from the output. You can then execute the following command to host the script:

```
docker run --rm -d --name token-share -p 80:80 -v $(pwd)/index.html:/usr/share/nginx/html/index.html nginx
```

On the other nodes you can now execute:

```
$(curl -s *ip-address-of-docker-node*)
```
This method of key sharing probably doesn't meet any organizational security standards but I'm fine with it on my local network.

# cleaning up with Docker

Once we have all our nodes added we want to clean up the nginx container. All we need to do is stop it and it will be deleted as we started it with an `--rm` argument. Just run:

``` sh
docker container stop token-share
```

We then probably want to refresh the join key we run:

``` sh
docker swarm join-token --rotate worker
docker swarm join-token --rotate manager
```

This ensures that your key going forward is different than the one you just publicly shared. Finally confirm the correct number of nodes are in your cluster:

``` sh
docker node ls
```

## Configuring your Firewall for a Swarm Node

If your virtual machines use an image with systemd (it probably does) you can run the following commands to make the required changes to firewalld.

``` sh
firewall-cmd --list-all #you can see your active zone here
firewall-cmd --zone=public --permanent --add-port=1025-655365/tcp -add-port=1025-655365/udp
firewall-cmd --reload #for changes to take effect
```

Opening all the above ports is overkill and kind of eliminates the purpose of using a secure distribution like CentOS. You can find out the exact ports to open in Docker's own [tutorial](https://docs.docker.com/engine/swarm/swarm-tutorial/#open-protocols-and-ports-between-the-hosts).

Not having the correct ports open can lead to some unusual behavior. I was able to have my CentOS join the swarm but they couldn't start containers.

Enjoy your Swarm!