---
layout: post
title:  "Suspending a computer over SSH"
date:   2017-04-07 00:00
categories: [linux]
tags: [tips-tricks, shell]
slug: suspending-a-computer-over-ssh
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---
I wanted to use an automated script to suspend all nodes in my ubuntu cluster. I decided to use the SSH to send a `pm-suspend` command to do it. The results were unexpected.

The first time I ran -

```
local_user@hostname:~$ ssh remote_user@remote_host sudo pm-suspend
```

I expected to enter the password for `sudo`. However, it exited with a message -

```
sudo: no tty present and no askpass program specified
```

So, I decided to edit the `sudoers` (using `visudo`) file to ensure that the `pm-suspend` would execute without prompting for the password. I added the follwing line to the `/etc/sudoers` file

```
%sudo   ALL=(ALL) NOPASSWD:/usr/sbin/pm-suspend
```

After this, I tried the same command and succeed at putting the remote machine to sleep. I could see the power LED of the remote machine blinking in the sleep mode. However, the SSH session did not disconnect. This was undesirable for my purpose. Next I modify my command to background the `pm-suspend` process.

```
local_user@hostname:~$ ssh remote_user@remote_host "sudo pm-suspend&"
```

The result was no different. The SSH session did not disconnect. The next attempt was to use `nohup`.

```
local_user@hostname:~$ ssh remote_user@remote_host "nohup sudo pm-suspend"
```

Alas, the SSH session persisted.

## The Solution !!!
Finally, I decided to use the `screen` utility with the `-d` switch to detach the screen. 

```
local_user@hostname:~$ ssh remote_user@remote_host "screen -d -m sudo pm-suspend"
```

This time, not only did the remote host enter the sleep state, but the SSH session terminated as desired.
