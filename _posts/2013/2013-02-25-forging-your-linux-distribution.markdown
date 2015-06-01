---
layout: post
title: "Forging your Linux Distribution"
date: "2013-02-25 00:00"
category: Linux
tags: [tips-tricks]
permalink: forging-your-linux-distribution
author: Bharath Bhushan Lohray
---

Recently, I had to install a software that was supported on Ubuntu 12.10 and refused to install itself on Mint 14 even though Mint 14 is based on Ubuntu 12.10. I had to find a way to get the installer script believe that the underlying distro was Ubuntu 12.10 and not Mint 14. Looking around forums, I discovered that one of the ways how the distro is detected is from the contents of the file `/etc/os-release` and `/etc/lsb-release`.

I started by backing up these two files in a safe location so I may restore them once I am done with the installer script and modified the lsb-release file to -

```
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=12.10
DISTRIB_CODENAME=Quantal
DISTRIB_DESCRIPTION="Ubuntu 12.10 Quantal Quetzal"
```

The original file from Mint -

```
DISTRIB_ID=LinuxMint
DISTRIB_RELEASE=14
DISTRIB_CODENAME=nadia
DISTRIB_DESCRIPTION="Linux Mint 14 Nadia"
```

And re ran the installer to have it detect Ubuntu instead of an unsupported distribution. Much of the editing was guesswork. I suspect that the installer was not looking for anything more than the first two lines from the file.
