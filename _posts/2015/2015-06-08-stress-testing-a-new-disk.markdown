---
layout: post
title: "Stress Testing a New Disk"
date: "2015-06-08 00:00"
permalink: stress-testing-a-new-disk
category: [Linux, hardware, technology]
tags: [tips-tricks]
author: Bharath Bhushan Lohray
comments: true
---

With a growing media collection, I am forced to buy a 2 TB hard disk about ever 18 months. Surprisingly, this seems to follow the Moore's law. Buying a new hard disk can be a daunting task.

1. There are a dozen brands to choose from.
2. Each brand has multiple models with nearly the same specifications.
3. Each disk you can find on Newegg or Amazon has mixed reviews of people claiming it to die within a month to within a year and pople claiming it to work for years.

Once you have read a few horror stories of a disks dying within a year, you will be inclined to avoid buying these drives. However, you shortlisted drives will have exactly zero drives. So, what do we do now? Is there no drive that you can trust?

It is hard to answer this question as an emphatic "Yes" or "No".

The disk manufacturers are more concerned about the statistical failure rate. 1 / 10,000 failing for the manufacturer may seem good. However, as an end user buying at most 2 or 3 disks, you will not likely enjoy the benefits of these impressive statistics. At the rate of 1/10,000 failures, you can expect 0.0003 drives to fail when you buy 3. However since the minimum disks that can fail is 1, you will perceive the failure rate as 33%.

There is an entire branch of statistics dedicated to [reliability](https://en.wikipedia.org/wiki/Reliability_engineering). Your problem is not that of owning a reliable disk (Forget it. It do not exist!), but preserving your data in spite of being stored on unreliable disks. Reliability trends for most manufactured products follows the [bathtub curve](https://en.wikipedia.org/wiki/Bathtub_curve) as shown in the figure (from Wikipedia).

![Bathtub Curve](http://cdn.bharath.lohray.com.s3.amazonaws.com/weblog/im/stress-testing-a-new-disk/Bathtub_curve.svg)
**Bathtub Curve**

The probability of failure of a brand new disks is high. This is primarily due to manufacturing defects. If the drive survives the initial uncertainties through 'infancy', it may fail yet due to random factors (including manufacturing defects and ageing). Now, if you are fortunate and your hard disk survives to a ripe old age (no more than 6 to 7 years - and people may argue), the probability of failure shoots up again. This time around, ageing and fatigue will be the major culprits.

To this end, I have in the past attempted to mitigate my risks by -

1. Buying from a reliable vendor who provides a no question return policy - (viz. Amazon), even if I had to pay a few dollars more.
2. By running a stress test on the disks.

The stress test consists of reading and writing the disks several times continuously for ~ 10 day. This, I believe helps me accelerate the process of initial failure. So, am I saying that I am trying to make my drive fail?? Yes. I am better off with a drive that fails within the first 30 days - so I can return it for a full refund or replacement. Moreover, at this point, I have not committed any important information to the disk for which I do not have a backup copy elsewhere. After the first three months, I gradually increase my reliability and get rid of the backup copies - I know this is a bad idea. I am cutting corners. If I had $$$ to spare, I would have stashed 1 additional copy locally and one copy remotely.

#### The Stress Test

I use a utility called `badblocks` to stress my disks. `badblocks` can fill the disks with patterns and read it back to verify it. These patterns are by default `0xff`, `0xaa`, `0x55` and `0x00`. This should test about every bit on the drive (not withstanding any error corrections).

`badblocks` may be run destructively - It overwrites any data that already exists on the disk (all partitions on the disk!). The basic syntax to run `badblocks` is

```
sudo badblocks -w /dev/sdX
```

You can find more about `badblocks` [here](http://linux.die.net/man/8/badblocks).

#### Personal experience with disks

Over the years, I have bought eleven [Toshiba Canvio](http://amzn.to/1T8nUVr)s. One failed within a month. My personal favourite is the [Canvio Basic 2TB](http://amzn.to/1dZi74I) (best value for money). I like it for the excellent matte finish. Toshiba also has a [Canvio Connect](http://amzn.to/1dZiegC) - that bundles some free cloud storage, but has a glossy finish that looks awful within weeks due to scratches.

I run a NAS with 4 x 4TB [WD Green](http://amzn.to/1dZiR9Z), 1 x [WD Black](http://amzn.to/1QknCfv) and have two [Seagate external USB drives](http://amzn.to/1JB0dCs). I also own 6 [Mushkin SSDs](http://amzn.to/1AZ92Da) that I use for my experimental cluster and two [Seagate Momentus Hybrid](http://amzn.to/1dZjm42) internal HDDs for my laptop. One of the three I bought was making an occasional beeping noise and was returned (this can also happen if the power is insufficient).

To conclude, I would sum up as -

1. Run a stress test on new disks.
2. Buy from reputed vendors - check their return policies.
3. Always keep multiple copies if you can afford it. If it is your projects, homeworks, photographs, home videos or any thing that cannot be replaced, keep a second copy anyway.

Additionally I would like to point out some [research by Google studying large population of hard drives][GOOGLE2007]. According to them, technologies such as [SMART](https://en.wikipedia.org/wiki/S.M.A.R.T.) were not reliable at predicting failures for individual drives. However correlations may only be observed when the population is sufficiently large.

[GOOGLE2007]: http://static.googleusercontent.com/media/research.google.com/en/us/archive/disk_failures.pdf
