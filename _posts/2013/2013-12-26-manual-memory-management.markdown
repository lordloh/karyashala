---
layout: post
title: "Manual Memory Management."
date: "2013-12-26 00:00"
slug: manual-memory-management
permalink: /:title/
comments: true
tags: [tips-tricks, shell]
category: [linux]
author: Bharath Bhushan Lohray
---

While using MATLAB for image processing, I sometimes run out of memory. On Windows, I have no choice but to restart MATLAB and hope that the garbage is cleared. While using the Linux platform, I can do better, I can force the caches clear.

To check for the currently available free memory -

Check free memory from the shell
```
bharath@chrysophylax:~$ free -m
             total       used       free     shared    buffers     cached
Mem:         15955       2854      13100          0        110       1243
-/+ buffers/cache:       1500      14455
Swap:        31248          0      31248
```

To clean up the cache, we first run sync to force any delayed writes to sync to disk.

Then we execute the following command to clear caches and free memory -

Shell command to clear caches

```
bharath@chrysophylax:~$ echo 1 | sudo tee /proc/sys/vm/drop_caches
```


After this we may run free to notice the difference -

Check free memory from the shell

```
bharath@chrysophylax:~$ free -m
             total       used       free     shared    buffers     cached
Mem:         15955       1964      13990          0          0        399
-/+ buffers/cache:       1565      14390
Swap:        31248          0      31248
```

`tee` echoes the output to `stdout`. This may be taken care of by executing shell command to clear caches


```
b..h@ch..x:~$ echo 1 | sudo tee /proc/sys/vm/drop_caches > /dev/null
```

Notice the increased free memory and decreases cache and buffer memory. I found a few more variations here. Writing different values to `/proc/sys/vm/drop_caches` can cause different thing.

||
--|--|--|
0	| 1	|Frees Page Caches (echo 1)
1	| 0	|Frees dentries and inodes (echo 2)
1	| 1	|Frees Page Caches, dentries and inodes (echo 3).



I have heard from many people that the RAM you are not using is RAM wasted. So this trick is absolutely unnecessary and drags down system performances. After all, caches exist for a reason. Over time I have come to agree with them. Forcefully clearing the cache makes sense only under very specific circumstances. And absolutely do not run something like this as a frequent cron job.
