---
layout: post
title:  "Securing with Port Knocking"
date:   2017-04-07 00:00
categories: [linux]
tags: [tips-tricks, shell]
slug: securing_with_port_knocking
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---
Recently I discovered that my log files started growing. The biggest among them was `btmp`, which was over 600MB. Attempting to run

```
watch sudo last -f /var/log/btmp  | head
```

showed several attempts per second to brute force into my machine -

```
root     ssh:notty    61.177.172.12    Sun Apr 30 03:21   gone - no logout
root     ssh:notty    61.177.172.12    Sun Apr 30 03:20 - 03:21  (00:00)
root     ssh:notty    61.177.172.12    Sun Apr 30 03:20 - 03:20  (00:00)
root     ssh:notty    61.177.172.12    Sun Apr 30 03:20 - 03:20  (00:00)
root     ssh:notty    61.177.172.12    Sun Apr 30 03:20 - 03:20  (00:00)
root     ssh:notty    61.177.172.12    Sun Apr 30 03:20 - 03:20  (00:00)
root     ssh:notty    218.65.30.38     Sun Apr 30 03:20 - 03:20  (00:00)
root     ssh:notty    218.65.30.38     Sun Apr 30 03:20 - 03:20  (00:00)
root     ssh:notty    61.177.172.12    Sun Apr 30 03:20 - 03:20  (00:00)
root     ssh:notty    218.65.30.38     Sun Apr 30 03:20 - 03:20  (00:00)

```

A search for a solution caused me to conclude that such attempts happen on many public facing server and few solutions exist, such as - using a firewall and allowing only certain hosts login. However, such a thing is not possible to implement on my home server. So, I decided to use port knocking to secure my machine.

Installing a port knocker on ubuntu is as simple as -

```
sudo apt install knockd
```

One would also need `iptables` and `iptables-persistent`. Next configure iptables as -

```
sudo iptables -A INPUT -s 192.168.1.0/24 -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j DROP
```

This allows SSH traffic from my local network, viz. 192.168.1.0/24. It further allows traffic from already established connections through. Finally it drops all traffic to SSH that in not already accepted.

We now configure `knockd` in the file `/etc/knockd.conf`

```
[options]
      LogFile = /var/log/knockd.log
      interface = eno1

[openSSH4knocker]
      sequence    = 64001,64099,64024,64005
      seq_timeout = 30
      start_command = /sbin/iptables -I INPUT 1 -s %IP% -p tcp --dport 22 -j ACCEPT
      tcpflags    = syn
      cmd_timeout = 180
      stop_command = /sbin/iptables -D INPUT -s %IP% -p tcp --dport 22 -j ACCEPT

[openSSH4everyone]
      sequence    = 64005,64025,64098,64010
      seq_timeout = 30
      command     = /sbin/iptables -I INPUT 1 -p tcp --dport 22 -j ACCEPT
      tcpflags    = syn
      cmd_timeout = 180
      stop_command = /sbin/iptables -D INPUT -p tcp --dport 22 -j ACCEPT
```

This executes an iptables command to allow traffic through from the IP that knocked on the ports - 64001, 64099, 64024, 64005 in a sequence - openSSH4knocker. On timeout the inserted rule is dropped. This lets the knocking host a window of opportunity to setup a SSH connection and keep it active. The openSSH4everyone opens SSH for all anyone for a period of 180s.

## How to knock?
### Windows
There are several knocker clients. I chose to use [KnockKnock](https://sourceforge.net/projects/knockknock/). It is not a very good client as it allows on more than one sequence to be stored. I have discovered that I need to keep a second instance of the program in a separate folder to send a second sequence or to a different host. Moreover, I cannot ping a host by name or set the interval between knocks. I absolutely need an IP address. Should I find a better knocker, I would ditch this immediately.

### Linux
Linux has client called knock that is easy to use -

```
knock -v host 64001:tcp 64099:tcp 64024:tcp 64005:tcp
```

It would be convenient to store this command with the port sequence in a script.

### Android
I am pretty happy with [Port Knocker](https://play.google.com/store/apps/details?id=com.xargsgrep.portknocker) on android. It does provide an option to set delays between knocks. This is important as TCP does not guarantee sequence of packet delivery.
