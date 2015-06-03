---
layout: post
title: "Running MATLAB with a high priority."
date: "2013-04-29 00:00"
category: [MATLAB, Linux]
tags: [tips-tricks, shell]
permalink: running-matlab-with-a-high-priority
comments: true
author: Bharath Bhushan Lohray
---

MATLAB provides a parallel processing toolbox that can help speed up algorithms that may be parallelized. However, if you are interested in getting a little more out of your machine, you can run MATLAB (on GNU/Linux) with a high priority. This can be done by starting MATLAB with a `nice` value of `-20`.

```
sudo nice -n -20 sudo -u bharath /usr/local/MATLAB/R2012b/bin/matlab -nodesktop -nosplash
```

One may modify the `/etc/security/limits.conf` file to allow users without `sudo` privileges to set nice values of less than 0. nice values range from -20(highest) to 19(lowest). The default nice value is 0 (normal priority).

Here is my question about this on [unix.SE](http://unix.stackexchange.com/questions/72934/how-do-i-start-a-process-with-a-nice-value-of-20-and-not-give-it-root-privilege). [Control Groups](https://wiki.archlinux.org/index.php/Cgroups) might be something else that one may want to look at.
