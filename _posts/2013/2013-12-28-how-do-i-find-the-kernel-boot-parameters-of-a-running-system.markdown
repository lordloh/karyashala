---
layout: post
title: "How do I find the kernel boot parameters of a running system?"
date: "2013-12-28 00:00"
permalink: how-do-i-find-the-kernel-boot-parameters-of-a-running-system
category: Linux
comments: true
tags: [tips-tricks]
author: Bharath Bhushan Lohray
---

Recently, while debugging a display, I had to change the kernel boot parameters several times and needed to know what boot parameters were used to boot the running kernel. After some search I discovered the trick (from [this post](http://askubuntu.com/questions/32654/how-do-i-find-the-boot-parameters-used-by-the-running-kernel)) -

```
cat /proc/cmdline
```
