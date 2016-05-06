---
layout: post
title: "Improving apt-get performance."
date: "2015-07-01 00:00"
slug: apt-get-performance
permalink: /:title/
category: [linux]
tags: [tips-tricks]
author: Bharath Bhushan Lohray
comments: true
---

For the last few years, I have had half a dozen computers at home - including Raspberry Pis. Updating these computers required a lot of redundant bandwidth. While I was aware of caching solutions, I had not used them. I decided to try `apt-cacher` but soon ditched it in favour of `apt-cacher-ng`. `apt-cacher-ng` worked well for me for a few weeks until suddenly the updates became slow and stopped. I would be stuck at `Fetching Headers` for hours. Initially I thought that my cache needed cleaning. I tried nuking the cache, but that did not help. Next I tried disabling the proxy, but that did not work either.

Further investigation across internet forums, I discovered that this is likely to happen if I used IPv6 to fetch updates. Apparently the IPv6 is network is not wide spread yet :-(. The solution that worked for me came from this [unix.SX question](https://unix.stackexchange.com/questions/9940/convince-apt-get-not-to-use-ipv6-method). I attempted to update using the command -

```  
sudo apt-get update -o Acquire::http::No-Cache=True -o Acquire::ForceIPv4=true
```

and it worked. Then, I got my proxy back and attempted to update using -

```
sudo apt-get update -o Acquire::ForceIPv4=true
```
and this did not work. I realized that this forced an IPv4 connection to my proxy, which incidentally happened to be running on the same computer, but did not force proxy to make IPv4 connection to the remote repositories. I realized that I had to force my `apt-cacher-ng` to to only make IPv4 connections.

Looking through the documentation, I found - the [How To avoid use of IPv4 (or IPv6) where possible?](https://www.unix-ag.uni-kl.de/~bloch/acng/html/howtos.html#howto-outproto) While the document was of little use, I discovered that I could edit ` /etc/apt-cacher-ng/acng.conf` file and add the following line to force only IPv4 outgoing connection to remote repositories.

```ConnectProto: v4```

Once I had this in place, I restarted my `apt-cacher-ng`  service using `sudo apt-cacher-ng restart` and attempted to update using the plain `sudo apt-get update`. And it worked. And fast!

Hope this post helps alleviate someone's frustrations. I am glad I found this solution. I was considering trying out [`squid-deb-proxy`](https://launchpad.net/squid-deb-proxy). This would have been a futile waste of time.
