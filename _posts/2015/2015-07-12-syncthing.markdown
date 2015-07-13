---
layout: post
title:  "Syncthing experience"
date:   2015-07-12 00:00
categories: [technology]
tags: [storage, cloud]
permalink: syncthing-experience
comments: true
author: Bharath Bhushan Lohray
---
I have been using Dropbox for a few years now, and as most Dropbox users, ran out of space. During my early days of using Dropbox, I used to invite a lot of friends to Dropbox and earned 250MB/user to the point where I earned ~12 GB. But as time progressed, the returns were diminishing - almost everyone has a Dropbox account now and no one left to invite.

Next, I tried [hubic](https://hubic.com/home/new/?referral=UUUSFV) - which offered 50 GB of space. Moreover, it is located in France - out of the reach of the NSA spies (?). However, it turned out to be slower than Dropbox, but not so slow that I would ditch it. Like Dropbox, it had an Android and Linux app too.

The ideal solution for me would be something that would let me -

* Backup my data against computer crashes, thefts and accidents.
* Backup offsite.
* No central server.
* Linux, Windows, Android and OSX.
* Avoid NSA / illegal govt. tapping.
* Backup to S3.

Bonus -

* Versioning.
* Sharing.
* High speed syncing when on the same LAN.

[Syncthing](https://syncthing.net/) on it's own serves the first five primary objectives and all the secondary objectives. Syncthing runs on a [protocol](https://github.com/syncthing/specs/blob/master/BEPv1.md) similar to bit torrent. It is essentially a peer to peer protocol. The tracker equivalent of syncthing is called a [global discovery server](https://github.com/syncthing/discosrv). There are two global discovery servers that are publically available. I do have an option to install my own as well - makes me feel more confidant about relying on syncthing.

Syncthing is designed to sync selected folders on the computer across authorized devices. An authorized device is another device running syncthing and is allowed to synchronize folders. I may have multiple authorized devices and may choose which authorized devices I choose to sync a given folder with. This is significantly greater control than Dropbox.

Also, unlike Dropbox, I can choose to add patterns for ignoring files, tweak rescan intervals, choose file pull order and versioning.

My syncthing installation is on my always on NAS and is always on. This gives me at least one node that is always on.

On the downside, It is hard to get Syncthing work with multiple users. The recommended method is to get each user run their own instance of Syncthing - that could be a lot of processes and memory and if you are trying this for your friends and family or a small corporate group on AWS, you cannot use a small or medium instance with impunity. I attempted to mitigate this by running Syncthing under it's own user space and adding `syncthing` to every user's group. The shared folders may then remain in the user's home folder with `770` permission mask. While this may not be the ideal solution, I find that it satisfies my needs for now.

I alos came across another file sync application called [Syncany](https://www.syncany.org/). This app lets one sync to S3. Making one of my syncthing nodes common with syncany, I can possibly backup my data to S3 - just an idea. Have not tried this yet.

Please feel free to share ideas or comment on my setup :-)
