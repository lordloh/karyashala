---
layout: post
title:  "Creating & Using Reverse SSH Tunnels"
date:   2017-10-06 00:00
categories: [linux]
tags: [tips-tricks, shell]
slug: reverse-SSH-tunnels
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---

A few years back, I used to SSH into my lab computer from home using the IP address of the Lab computer. Over the years, my university IT personnels restricted inbound access to the networks. I can no longer SSH into my lab computer. I wished that I could use an outbound SSH connection to use the lab computer remotely. A little bit of search revealed that such a thing was indeed possible using SSH Reverse Tunnels.

Creating a reverse tunnel using SSH in pretty simple. A reverse tunnel can be created by executing (labcomputer) -

```
lab_user@labcomputer:~$ ssh -R 65522:localhost:22 myhomecomputer.com
```

The above command creates an SSH connection to my home computer and maps `homecomputer.com:65522` <-> `labcomputer:22` I can then SSH into the labcomputer from myhomecomputer by executing the command -

```
home_user@myhomecomputer:~$ ssh -p 65522 127.0.0.1
```

This command effectively establishes an SSH to `labcomputer` through the tunnel using the mapped ports.

Great! Can I have this reverse SSH tunnel be persistent? Well, SSH connections disconnect all the time. I needed to find a way to reestablish SSH connection on disconnection. Another search revealed that a tool called [`autossh`](http://www.harding.motd.ca/autossh/) existed to do exactly that. However, the problem I faced is that myhomecomputer is behind a firewall and needs a port knock sequence to expose port 22 (see [Securing with Port Knocking](https://bharath.lohray.com/weblog/securing-with-port-knocking/)). I initially decided to ditch `autossh` in favour of a script with an infinite loop.

```bash
#! /bin/bash
while :
do
        knock myhomecomputer.com 64001 && sleep 1 && knock myhomecomputer.com 64002 && sleep 1 && knock myhomecomputer.com 64003 && sleep 1 && knock myhomecomputer.com 64004
        ssh -R 65522:localhost:22 myhomecomputer.com
done
```

The problem with this script is that sometimes the SSH connection would freeze but not disconnect. `autossh` is a script that does more that what the simple bash script above. What I need was a way to execute a local script locally before attempting to establish an SSH connection.

It turns out that openSSH clients do have a directive called `ProxyCommand`. These are intended to proxy SSH and to effectively create proxy chains. So one can SSH `hostSrc > host2 > host3 > hostDst`. This is similar to TOR. However, I decided to use `ProxyCommand` for an entirely different purpose.

My work around was -

### Step 1

I edited the `~/.ssh/config` on the lab machine to add the following

```
host myhomecomputer.com
    HostName myhomecomputer.com
    user user_me
    IdentityFile ~/.ssh/user_me.openSSH

host myhomecomputer
    HostName myhomecomputer.com
    user user_me
    IdentityFile ~/.ssh/user_me.openSSH
    ProxyCommand bash -c 'source knock_myhomecomputer_ssh; ssh -TN -R 65522:localhost:22 myhomecomputer.com'
```

Notice the names - `myhomecomputer` (alias / shortcut)  & `myhomecomputer.com` (actual host). Attempting to SSH using the alias `myhomecomputer` executes the `ProxyCommand` line, which in turn connects to actual host myhomecomputer.com over SSH.

### Step 2
The `knock_myhomecomputer_ssh` is as -

```bash
SLEEP_T=0.75
knock myhomecomputer.com 64001 && sleep $SLEEP_T && knock myhomecomputer.com 64002 && sleep $SLEEP_T && knock myhomecomputer.com 64003 && sleep $SLEEP_T && knock myhomecomputer.com 64004
```

### Step 3
Added the following to my `crontab` on the labcomputer

```
@reboot autossh -f myhomecomputer
```

I now have a persistent SSH connection and a Reverse SSH Tunnel from the labcomputer to myhomecomputer.
