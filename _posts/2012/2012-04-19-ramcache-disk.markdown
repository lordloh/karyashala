---
layout: post
title:  "RAMCache Disk"
date:   2012-04-19 00:00
categories: [linux]
comments: true
tag: [tips-tricks, ramdisk]
slug: ramcache-disk
permalink: /:title/
author: Bharath Bhushan Lohray
---

This is all about money! The improved speed is a pleasant side effect. Elastic Block Storage (EBS) backed instance on AWS EC2 are billed by the amount of IO performed on the disks among other things. If you are using EC2, you are paying for all of these -

1. EC2 usage hours
2. EBS Provisioned disk space
3. Data Transfer Out
4. EBS disk I/O


There is not much you can do about the first three, but you can definitely write good code on your web (or any other kind of) application to reduce disk IO. Here is my scheme -


I decided to create a small ramdisk and map it to my web server. The ramdisk folder stored some of the transient data that I would not need across restarts and a few very often used php scripts. I also maintained a disk based copy of these files (rdimage) which I copy over to the ramdisk on booting. I setup a script in /etc/init.d/ to do this. Additionally, the 404 page in the ramdisk folder was a .php file that returned a redirect header to fetch the page from the disk based copy (rdimage) as a fall back. This would only be necessary if the ramdisk failed to mount or files failed to copy.

You may come up with – “Use [Varnish Cache](https://www.varnish-cache.org/) or <some other reverse proxy system>”, but the bottom line is – RAM is always faster than a disk access. Plus you save on disk IOs. All I do is run this blog and a Dynamic DNS update system (something to write about for a future blog post) for my personal use, but if you have scripts that are receiving a 1000+ hits an hour you save on quite a bit of disk IO and not to mention speed up the performance of the disk application.

I had also considered putting symbolic links on the real disk to files on the ramdisk – but immediately gave up on this as this would require a disk IO to read the i-nodes anyhow. So a pure ramdisk approach seemed more attractive.

Here are some of the steps & scripts -

The `/etc/init.d/ramcache` script to create the ramdisk and copy files over.

```
#! /bin/bash
mknod /dev/ram1 b 1 1
mkfs -q /dev/ram1 8192
mkdir /mnt/epmrl0/www/ramdisk
mount /dev/ram1 /mnt/epmrl0/www/ramdisk
cp -p -R /mnt/epmrl0/www/rdimage/* /mnt/epmrl0/www/ramdisk/
```

A symbolic link for this script is created in the directory `/etc/rc<n>.d/` where `<n>` is the runlevel at which this is to be executed. The symbolic links are of the form `S<xy>ramcache`. The `xy` is the order in which the scripts are executed. one must make sure that the script to initilize the ram disk executed before the httpd comes up alive. For example -

```
ln -s /etc/init.d/ramcache /etc/rc3.d/S46ramcache
```

And httpd has a prefix greater than S46.

The lines in `/etc/httpd/conf/httpd.conf`

```
Alias /c2 "/media/data/www/rdimage"
<Directory "/media/data/www/rdimage">
  Options Indexes MultiViews FollowSymLinks +ExecCGI
  AllowOverride None
  Order allow,deny
  Allow from all
</Directory>
Alias /c "/media/data/www/ramdisk"
<Directory "/media/data/www/ramdisk">
  Options Indexes MultiViews FollowSymLinks +ExecCGI
  AddHandler cgi-script .cgi .pl .py
  AllowOverride None
  Order allow,deny
  Allow from all
  ErrorDocument 404 /error/rd_redirect.php
</Directory>
```

Finally the `/var/www/error/rd_redirect.php` for the fallback.

```
<?php
  $pth=implode("/",array_splice(explode("/",$_SERVER["REQUEST_URI"]),2));
  header("Location: /c2/".$pth);
?>
```
