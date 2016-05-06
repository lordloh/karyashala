---
layout: post
title: "How do I mount an SD card image that has multiple partitions?"
date: "2013-01-06 23:21"
category: [linux]
comments: true
tags: [tips-tricks, shell, diskImage]
slug: how-do-i-mount-an-sd-card-image-that-has-multiple-partitions
permalink: /:title/
author: Bharath Bhushan Lohray
---

Mounting an image of a file system is pretty simple -

```
$ sudo mount imageFile.img mountpointFolder/
```

However, this will fail to work if the image file has multiple partitions. In my case the image of an sd card that I created by -

```
$ sudo dd if=/dev/sdd of=diskImage.img bs=4M
```

After a little search I found a utility called [kpartx](http://christophe.varoqui.free.fr/) which solved my problem. `kpartx` can be installed from the default repositories of ubuntu and other similar distributions.

We may use file to check if the file we are trying to mount is an image with multiple partitions -

```
$ file -s diskImage.img
diskImage.img: x86 boot sector; partition 1: ID=0xc, active, starthead 1, startsector 63, 144522 sectors; partition 2: ID=0x83, starthead 0, startsector 144585, 6988275 sectors, code offset 0x0
```

We may then use `kpartx -l` to dry run `kpartx` and check the loop devices that would be created if `kpartx` is run.

```
$ sudo kpartx -l diskImage.img
loop5p1 : 0 144522 /dev/loop5 63
loop5p2 : 0 6988275 /dev/loop5 144585
loop deleted : /dev/loop5
```

Now, we run kpartx (-v for verbose and -a to add loop devices).

```
$ sudo kpartx -a -v diskImage.img
add map loop5p1 (252:10): 0 144522 linear /dev/loop5 63
add map loop5p2 (252:11): 0 6988275 linear /dev/loop5 144585
```

The loop devices are created at `/dev/loop<someNumber>`. The partitions are mapped at `/dev/mapper/loop<someNumber>p<partitionNumber>`.These mapped partitions may then be mounted -

```
$ sudo mount -o ro /dev/mapper/loop5p1 tmp1/
$ sudo mount -o ro /dev/mapper/loop5p2 tmp2/
```
