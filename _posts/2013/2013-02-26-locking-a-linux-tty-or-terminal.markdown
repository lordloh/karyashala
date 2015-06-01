---
layout: post
title: "Locking a Linux TTY or terminal"
date: "2013-12-28 00:00"
category: Linux
tags: [tips-tricks, shell]
permalink: locking-a-linux-tty-or-terminal
author: Bharath Bhushan Lohray
---

Most GUI interfaces have a button in a menu somewhere that lets a users lock their screens when they are away. But how do you do this on a terminal instead of the usual GUI?

The simple utility is called `vlock`. executing it shall bring up a screen like -

```
This TTY is now locked.

Please press [ENTER] to unlock.
```

Some useful options to use are are `vlock -a -s`. The `-a` switch prevents the user from witching over to other Virtual Terminals using the `Ctrl` + `Alt` + `F1..F6`. The `-s` prevents `SysRq` from killing the process.

The utility may be installed on Mint / Ubuntu based systems by -

```
sudo apt-get install vlock
```

**Warning! A user may remotely login and kill the vlock process with sudo privileges and unlock the terminal.**
