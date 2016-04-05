---
layout: post
title: "bash auto completions and colours"
date: "2013-11-14 00:00"
permalink: bash-auto-completions-and-colours
tags: [tips-tricks, shell]
comments: true
category: [Linux]
author: Bharath Bhushan Lohray
---

Recently I had a problem with bash not auto completing the commands I was typing while I pressed the `Tab` key beyond simple commands. Previously, bash used to auto complete commands while I typed `sudo apt-get ins<tab>`. to install. It also stopped completing package names. A little hunting for the solution brought me to [this stack exchange question and it’s solution](http://askubuntu.com/questions/86375/apt-get-autocomplete).

The solution as mentioned by [Ian Hincks](http://askubuntu.com/users/43704/ian-hincks), was exactly what I wanted. I had deleted my .bashrc and never got it back. I simply acquired a new copy by copying a default from /etc/skel/.bashrc to my home directory and assigning it execute permissions, if it does not have them already. Also it is nice to make sure that you own the .bashrc file in your home directory. Improper permissions are the cause of a good number of linux havocs that exist.

```
cp /etc/skel/.bashrc ~
chmod 644 ~/.bashrc
```

Since `.bashrc` is executed on starting bash, I had to close my current terminal emulator window and start a new one for the changes to take effect.

This however assumes that I had the `bash-completion` package installed.
