---
layout: post
title:  "Creating a compressed image of a disk."
date:   2013-08-25 00:00
categories: Linux
tags: [tips-tricks]
permalink: creating-a-compressed-image-of-a-disk
author: Bharath Bhushan Lohray
---
While playing around with embedded devices, I often end up doing some things that crash my system and the only quick way to recover is by reinstalling the OS. Reinstalling the OS is pretty time consuming and not to mention that you loose all your custom settings.

A quick work around this that I like to do is to dump the image of the entire SD card on the hard drive.

```
dd if=/dev/sdd of=mysdcard.img bs=1M
```

Now, if something goes wrong, I can just reverse this and get back to a last know stage.

```
dd if=mysdcard.img of=/dev/sdd bs=1M
```

You may also maintain several snapshots. But these images are pretty large (16GB for a 16GB SD card!). So, it would be good to compress it, unless you have a lot of hard disk space and do not want to take the time to do all the compression.

A simple way to compress the image is -

```
dd if=/dev/sdd bs=1M | gzip -c -9 > mysdcard.img.gz
```

This shall give you a smaller file.  However, as pointed out by Sandeep on [his blog](http://www.linuxweblog.com/dd-image), this shall store everything. including the garbage. A solution provided is to fill the disk with zeros before imaging it. Sectors full of zeros shall compress well you will have a smaller compressed file.

```
dd if=/dev/zero of=tempZeroFill.del bs=1M
rm tempZeroFill.del
```

and get with compressing your image.

However, a draw back with doing this on an SD card is that you are wearing out your flash memory one cycle closer to it’s end. An alternative, but more complex approach would be to-

* Image the SD card (or any other disk) without compression.
* Mount individual partitions from the image file using `kpartx` ([see this article](http://bharath.lohray.com/weblog/how-do-i-mount-an-sd-card-image-that-has-multiple-partitions/)).
* Now fill the mounted image with zeros.
* Delete the zerosFill file.
* unmount the image.
* Compress and save the image.
If you have access to batter utilities like ghost, you can save all this trouble.

A compressed image file may be written to an SD card at a later time by -

```
gunzip -c mysdcard.img.gz | sudo dd of=/dev/sdd bs=1M
```
