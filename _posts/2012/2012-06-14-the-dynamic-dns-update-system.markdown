---
layout: post
title:  "The dynamic DNS update system"
date:   2012-06-14 00:00
categories: Linux
permalink: the-dynamic-dns-update-system
author: Bharath Bhushan Lohray
---
Most ISPs provide subscribers with a dynamic IP which changes every time you disconnect from the internet and reconnect. The problem of a changing IP is not so perceptible with a cable internet as most of the subscribers keep their modems hooked up and powered up 24 x 7. So the individual IPs are held by users almost indefinitely. Keeping a modem powered up 24 x 7 is still not a perfect solution as one might get disconnected due to a power failure or a fault on the cable. The bottom line is that unless you have a static IP provided by your ISP (usually for some extra $$), you cannot be assured of having a fixed IP at all.

### Why do you need a fixed IP?

If all you do is surf the internet and check mails like an ideal user, you do not need one. But on the other hand if you are a hacker, who runs a server at home and connects to it from remote locations, you definitely want one. But as hacker minds go, you probably will try to find the lowest cost solution. Ridiculous! Paying the ISP for a static IP indeed! It happens that there are such solutions in existence - http://dnslookup.me/dynamic-dns/ lists a number of dynamic DNS services providers that provide this service for f(r)ee.

### What am I trying to do?

Reinvent the wheel! For the fun, experience and bragging rights. And I indeed learnt a few new things about apache, linux permissions and demons in the process. The system I describe here is meant for my personal use. It is not intended to be scalable nor does it have a feature set as rich as the one from some of the service providers mentioned [here](http://dnslookup.me/dynamic-dns/).

### The Solution

My solution is based on [dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) – a light weight DNS server, [Apache](http://httpd.apache.org/) - the second most popular web server on the internet and a simple [bash](http://www.gnu.org/software/bash/) script with some configuration file edits.

dnsmasq – The initital attempt to build this system was on an EC2 micro instance. Due to the very limited memory available, I decided to choose a DNS server with the smallest foot print possible. dnsmasq is designed to serve a small network updating hosts names with the IP addresses dished out by the DHCP server. It does not support records such as CNAME, TXT, SOA etc. Hence it’s capability is pretty limited. However, for my purpose of just being able to refer to hosts by their names despite of any change in the IP address, such a limited service is sufficient.

The PHP Script – To set up the service, the dnsmasq and apache web server with php support were first installed. A simple php script was set up update a hosts file. The PHP script maintains a JSON file with the host names, IP address and the last update request time stamp. If the incoming update request is redundant, the JSON file is updated with a new time stamp and the host file and dnsmasq is left alone. If the incoming update request warrants a host file update, the JSON file and the host file is updated and dnsmasq is restarted.

### Implementation

The php script implementation may be found here. Below are examples of the JSON file and the hosts file.

/media/data/www/ramcache/ip.txt:

```
{
    "host1.h.lohray.com": {
        "ip": "129.118.243.10",
        "update": "2012-04-20 20:45:59"
    },
    "host2.h.lohray.com": {
        "ip": "75.110.204.100",
        "update": "2012-05-09 07:30:06"
    },
    "host3.h.lohray.com": {
        "ip": "50.19.108.105",
        "update": "2012-04-19 04:54:21"
    }
}
```

/media/data/www/ramcache/hosts.dns:

```
129.118.243.10 host1.h.lohray.com
75.110.204.100 host2.h.lohray.com
50.19.108.105 host3.h.lohray.com
```

Once the hosts file is changed, dnsmasq needs to be restarted to be reloaded the new hosts file and start serving DNS requests according to the new data. An Authoritative DNS may set a TTL. This directs other caching DNS servers to cache the results for the specified number of seconds. In this setup, the TTL is set to seven seconds. This would at least cache the DNS long enough to complete a web server request (HTML, CSS, JS, Images etc) and short enough to change and deploy the DNS host name quickly.

from /etc/dnsmasq.conf:

```
local-ttl=7
addn-hosts=/media/data/www/ramcache/hosts.dns
```

The first line specifies the TTL time in seconds and the second line indicates the location of additional hosts file. This is used to keep the default hosts file of dnsmasq clean. The file hosts.dns needs to be written by PHP and requires appropriate permissions and ownership. In this setup, the file is owned by apache and group apache and root is a part of the apache group and the permission on the file is -rw-rw-r–.

The ip.php file restarts the dnsmasq. However, ip.php runs as user apache. Restarting a service requires root privileges.

```
exec("nohup sudo service dnsmasq restart",$opLines);
```

This is fixed by granting user apache privilege to use sudo and restart the service without using a password.

```
apache ALL=NOPASSWD: /sbin/service dnsmasq restart
```

Also, by default, sudo requires a tty/pty to execute. Running a command from a php script with sudo requires this to be waived by adding the following line in /etc/sudoers

```
Defaults:apache !requiretty
```

Lastly, we use nohup to background the script that restarts the dnsmasq service. This allows the php script to continue to execute without waiting for dnsmasq restart script to complete and lets the restart script continue execution after the php script terminates.

RAM Cache backup – The PHP script, JSON file and the hosts file may all be placed in a RAM disk to save disk IOs and improve performance as suggested in the [RAMCache Disk article](http://bharath.lohray.com/weblog/ramcache-disk/).

### Authentication & Security

A dynamic DNS update system must have some minimum level of security to prevent malicious users from tampering with the DNS. A basic system would involve a username and a password. The system implemented here has a username, a list of host names that the user is authorized to update and a secret key that is shared between the server and the client. The credential file is a JSON file of the format as shown below -

```
{
    "user1": {
        "id": [
            "host1.h.lohray.com",
            "host2.h.lohray.com"
        ],
        "key": "secretkey1"
    },
    "user2": {
        "id": [
            "host3.h.lohray.com"
        ],
        "key": "secretkey2"
    }
}
```

The request is formed by

```
user=user1&id=host1.h.lohray.com&timeST=2012-06-14%2015:58:27&hash=2bd2a24f7c89869491bb9695aefda8da3daee00f
```

The hash itself is generated at the client end by running the SHA1 on

```
host1.h.lohray.com
2012-06-14 15:58:27
secretkey1
```

The username, host name, time stamp and the hash are sent to the server. The shared secret key however is not sent. At the server end, the same hash is generated using the servers copy of the shared secret key that corresponds to the user. The server also verifies if the user is authorized to updated the specified host.

The time stamp in the request is checked against the current time. Any requests older than ~15 seconds is discarded as a replay attack.

### Periodic Update

A useful way to periodically update the server from a client is to use a cron job that runs every 15 minutes to one hour. A bash script that would do the required task using curl may be found here.

Winding up, we have a Dynamic DNS update system with sufficient security, ability to handle multiple hosts and multiple users, resistant to replay attacks, makes use of a RAM disk to improve performance and reduce disk IO costs (AWS – EBS).
