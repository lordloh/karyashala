---
layout: post
title:  "How to re-read the partition table without removing the SD card"
date:   2013-08-23 00:00
categories: Linux
permalink: how-to-re-read-the-partition-table-without-removing-the-sd-card
author: Bharath Bhushan Lohray
---
Say you take a blank SD card, plug it into a computer. It may now appear as `/dev/sdd`. Let us now dump an image with two partition on this card

```
dd if=sdcard.img of=/dev/sdd bs=4M
```

Once this is done, we do not see `/dev/sdd1` and `/dev/sdd2` immediately.

Each time I did this, I had to take the card out of the slot and put it back in. Until I stumbled across this…

```
sudo /sbin/blockdev --rereadpt /dev/sdd
```

This re-reads the partition table without having to remove and re-insert  the card.

Another solution I came across is -

```
sudo partprobe /dev/sdd
```

Yet another way -

```
sudo sfdisk -R /dev/sdd
```

I only found `partprobe` to work on mounted partitions. The others returned a partition busy error.
