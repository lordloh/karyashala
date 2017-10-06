---
layout: post
title:  "Very Large Hand Disk Issues"
date:   2016-10-27 00:00
categories: [hardware]
tags: [tips-tricks]
slug: very-large-hard-disk-issues
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---

An year back, I had written about [stress testing new hard disk drives](/weblog/stress-testing-a-new-disk/). In the process of phasing out old disks, I bought a new [4TB Seagate USB 3 disk](http://amzn.to/2e1QH0W) from Costco for $99. And as usual, I put it through stress tests...

```
time sudo badblocks -vw /dev/sdf && pushb "badblocks Done"
```

...and here are the results.

_The last part of the command is to send me a push notification on [Pushbullet](https://pushbullet.com/) once the tasks are run. [Here](https://github.com/lordloh/pushb) is my implementation of the __pushb__ utility_

```
Checking for bad blocks in read-write mode
From block 0 to 3907018582
Testing with pattern 0xaa: done
Reading and comparing: done
Testing with pattern 0x55: done
Reading and comparing: done
Testing with pattern 0xff: done
Reading and comparing: done
Testing with pattern 0x00: done
Reading and comparing: done
Pass completed, 0 bad blocks found. (0/0/0 errors)

real    5289m17.742s
user    43m2.892s
sys     83m14.560s
```

So, what is the problem?

```
real    5289m17.742s
```

That is over 3 and a half days to go through the disk 8 times. That is about 11 hours to read the entire disk or write the entire disk (~ 2h 45min / TB). Now, imagine a 6TB or an 8TB USB 3 disk. The issue here is not the time, but the error rates. Disks tend to have 1 unrecoverable read error per 100TB of reading the disk. This parameter remains the same across disk sizes.

Below is an extract from a Seagate datasheet. Other manufacturers have similar figures.

![Seagate Datasheet](http://cdn.bharath.lohray.com/weblog/im/very-large-hard-disk-issues/seagate_error_rates.png)

Now, if you are using a 1TB disk, you can read the disk 100 times before you have an error. For a 4TB disk, this goes down to 25 times and 12.5 times for an 8TB disk.

Another problem is that failure of a single disk now puts 4TB of data at stake as opposed to 2TB an year and a half ago. So, if you rest trusting your data to a single disk, you have set yourself up for an unpleasant surprise.

So, to reiterate the conclusions form my last article -

1. Always stress test new disks.
2. Keep more than one backup
3. Vary your disk collections by age and brands
4. Phase out your old disks
