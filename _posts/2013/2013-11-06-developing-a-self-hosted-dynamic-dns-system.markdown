---
layout: post
title:  "Developing a self-hosted Dynamic DNS System"
date:   2013-11-06 00:00
category: [Linux, WebWorks]
tags: [diy, dns]
permalink: developing-a-self-hosted-dynamic-dns-system
author: Bharath Bhushan Lohray
---
![Simple Network](http://cdn.bharath.lohray.com/weblog/im/the-dynamic-dns-update-system/dynDNSHead.png)

I had written a post on developing a [dynamic DNS system using Apache, PHP and dnsmasq](http://bharath.lohray.com/weblog/the-dynamic-dns-update-system/) over an year ago. I had deployed this on my AWS EC2 instance. However, a while back, I got a mail from AWS reporting an abuse. It turned out that my dnsmasq server was configured as a publicly accessible recursive DNS server. Such a server could be used to launch a reflector attack. I found fixing this issue on dnsmasq pretty troublesome. So I decided to use the popular DNS server – [BIND](https://www.isc.org/downloads/bind/) along with Apache and PHP.

BIND is the most popular name server on the internet and had a lot of documentation and community support. BIND also is far more versatile, although not as light weight as dnsmasq. The BIND server reads global the configuration options from `/etc/named.conf`.  Apart from this, BIND also reads from `/etc/named.rfc1912.zones`.  This file defines various zones which the BIND server serves authoritatively. The zones defined in this file further refer to other zone files that contain the actual Resource Records. An example of a zone from the `named.rfc1912.zones` is as shown below.

```
zone "h.lohray.com" IN {
        type master;
        allow-update{ none; };
        file "/etc/named.zones/h.lohray.com";
};
```

Below is an example of a zone file containing the actual resource records  h.lohray.com.

```
$TTL 2 ; default TTL for zone
$ORIGIN h.lohray.com. ; base domain-name
@ IN SOA ns1.h.lohray.com. user.gmail.com. 2013110706 3h 15m 3w 3h
  IN NS ns1.h.lohray.com.
ns1 IN A 50.19.101.183
host1 IN A 129.118.11.125
host2 IN A 75.111.162.91
host3 IN A 129.118.11.74
```

The dynamic DNS system generates these resource records using a PHP script. On receiving an update about a changed IP address [shown as 1], the zone file is updated and the BIND server is sent a signal to reload the zone [shown as 3]. The diagram of the system is  shown below.

![Dynamic DNS update System](http://cdn.bharath.lohray.com/weblog/im/the-dynamic-dns-update-system/dynDNS.png)
**Dynamic DNS System**

The zone may be reloaded by either restarting the BIND server or by running the command -

```
sudo rndc reload h.lohray.com
```

A PHP script is served on an Apache web server to handle requests for IP updates. Such a script should take care as to properly authenticate update requests, detect and foil malicious requests to update resource records. This may be done by verifying a salted hash of the request. This request is sent as -

```
id=host1&amp;user=user1&amp;hash=55d6967380cdb475c76c3d7e83d074c71999264c&amp;timeST=2013-11-05+21%3A43%3A28
```

The SHA1 hash is calculated on-

```
host1\n
2013-11-05+21:43:28\n
ShArEd-SeCrEt-KeY
```

The date acts as a salt. The server verifies the date to make sure that a replay attack is not being carried out. Once a hash is generated for a request, it is allowed a window of 15 seconds to be received and processed by the server. The  ShArEd-SeCrEt-KeY is not transmitted. The PHP Script I used may be found here. This script also maintains a user and their corresponding secret keys in a json format file. Each user is restricted to modify resource records for certain hosts.

```
{
    "user1": {
        "id": [
            "host1",
            "host2",
            "host3",
        ],
        "key": "ShArEd-SeCrEt-KeY"
    },
    "user2": {
        "id": [
            "host4"
        ],
        "key": "ShArEd-SeCrEt-KeY-2"
    },
    "user3": {
        "id": [
            "host5"
        ],
        "key": "ShArEd-SeCrEt-KeY-3"
    }
}
```

For the PHP script to work, Apache need to have permission to execute -

```
sudo rndc reload h.lohray.com
```

This may be done by permitting apache to execute this command without password and without the need for a TTY. This may be done by modifying the suoders files using visudo. Modify the following lines -

```
Defaults: apache !requiretty
```
to allow the command to  run without the need for apache to have access to a TTY. Without this, attempts to run `sudo` shall throw an error similar to – `“sorry, you must have a tty to run sudo @ sudoers_policy_main() ./sudoers.c:448“`

```
apache ALL = NOPASSWD: /usr/sbin/rndc reload h.lohray.com
```

to permit apache to reload the zone file as root without the need for a password.

For automatic updates, I created bash shell script that uses curl libraries to send requests to update the IP address. Find the script [here](http://bharath.lohray.com/sources/ipupdate.sh.txt). This script is scheduled to execute every 15 minutes using the `crontab` as follows -

```
47 2,8,14,20 * * * /home/user/ipupdate.sh
```

Notice the use of off setted numbers in minutes. It is not desirable to have all the hosts updated at the same time say at HH:00 or HH:15 to avoid a surge of load on the server.
