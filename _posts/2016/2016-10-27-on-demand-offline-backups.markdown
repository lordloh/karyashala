---
layout: post
title:  "On Demand Offline Backups"
date:   2016-10-27 00:00
categories: [linux]
tags: [tips-tricks, shell]
slug: on-demand-offline-backups
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
excerpt_separator: <!--more-->
---
I have a few monthly digital magazine subscriptions that I have been getting for years. I have saved and aggregated on my hard disk. Recently, I lost a chunk of these due to my mistake at the terminal. As a precaution, I had these sync to my cloud storage space on [hubic](https://hubic.com/home/new/?referral=UUUSFV) (Offer 25GB of storage space + 50 with 5 referrals). I thought I hand a second copy secured. However, as I logged in to the hubic web interface, I saw files vanishing right before my eyes. The hubic client program syncing between my computer and the cloud was sending instructions to get rid of files that were no longer on the computer. By the time I could log in via SSH and terminate the client program, it was too late. Unlike [Dropbox](https://db.tt/F8PSoGj3), hubic does not keep deleted files to undelete. Years of aggregated magazine subscriptions lost in minutes.
<!--more-->
This made me see the necessity to have offline backups.

One easy way was to set `cron` tasks to periodically copy certain folders to an external drive via `rsync`. However, leaving an external drive connected does turns this "offline" approach into "online". I felt, it would be ideal if I could plug in my drive into my NAS whenever, I wanted and this would cause a backup job to start running. I looked around and discovered that I could use [`udev`](https://en.wikipedia.org/wiki/Udev), but reading more, I decided I did not want to dive into `udev` for this.

I finally settled on an idea, where I plugin my hard disk into the NAS whenever, I wanted to back up and that night, the selected folders would back up to the hard drive. The next morning, I could unplug the disk and voila, I have my little offline backup system.

I set about implementing this using a shell script that would run every night and check, if my external USB backup, is plugged in or not. If found, it would initiate a backup sequence. Here is the shell script that I used -

```bash
#! /bin/bash
PART_ID="926fc38e-bc7e-467a-953e-25c6555939a0"
now=$(date)
echo "===== Running at $now ====="
if [ -b /dev/disk/by-uuid/"$PART_ID" ]; then
        echo "Partition Found!"
        mkdir ~/$PART_ID
        mount $PART_ID
        if [ $? -eq 0 ]; then
                echo "Mounted"
                # Run backups
                rsync -r "/home/bharath/hubic/Datasheets" ~/$PART_ID/.
                rsync -r "/home/bharath/hubic/Harvard Business Review" ~/$PART_ID/.
                rsync -r "/home/bharath/hubic/IEEE Computer Magazine" ~/$PART_ID/.
                rsync -r "/home/bharath/hubic/Linux Journal" ~/$PART_ID/.
                rsync -r "/home/bharath/hubic/MagPi" ~/$PART_ID/.
                rsync -r "/home/bharath/hubic/Make Magazine" ~/$PART_ID/.
                rsync -r "/home/bharath/Photos" ~/$PART_ID/.
                rsync -rl "/home/bharath/Datasets" ~/$PART_ID/.
                sync
                umount $PART_ID
                pushb "Offline backup done."
                if [ $? -eq 0 ]; then
                        echo "Unmounted"
                        rm -Rf ~/$PART_ID
                        now=$(date)
                        echo "===== Done at $now ====="
                fi
        fi
else
        echo ":-("
fi
```
[Here](https://gist.github.com/lordloh/2305d23f898837e5bac7c9d7dae42cc5) is the gist containing the code.

To have this script work, I had to add a line allowing users to mount the disk in the `/etc/fstab` file.

```
UUID=926fc38e-bc7e-467a-953e-25c6555939a0       /home/bharath/926fe38e-bc7e-467a-959e-25c6535939f0      ext4    noauto,noatime,nodiratime,relatime,user 0       1
```

Apart from this, I had to set the user `bharath` (me!) `rwx` permissions on the entire disk. A big problem with doing this is that If, I move the disk to a different computer - one that does not have the user `bharath` (me!) created with the user id of 1000, someone else, who has `UID=1000` would have the `rwx` permissions. This is something, I would have to live with till I figure a way around this or write my own portable journaling file system.

To perform a nightly check, I would have to add something like the following line to my crontab -

```
0 4 * * * /home/bharath/do_offline_backup.sh >> offline_backup.log
```

On a side note, comparing hubic to Dropbox, I see hubic having a huge price advantage -

| hubic               | Dropbox        | AWS S3 (Oregon)|
|---------------------|----------------|----------------|
|  €10/year (100 GB)  | $100/year (1TB)| $370/year (1TB)|
|  €50/year (10 TB)   |                | + Data transfer cost |
\* 2016 October Prices

And hubic servers are hosted in France, we can reasonably hope that this in out of reach of the various 3 letter government agencies of the US.

The downside of hubic are -

1. Speed - it is slightly slower than dropbox.
2. UI - not as great a UI as dropbox.
3. Versioning - no versioning or undelete.
4. Dropbox paper - no on website editors with hubic.

If all you want to do is have an online cloud backup, this is the service for you.
