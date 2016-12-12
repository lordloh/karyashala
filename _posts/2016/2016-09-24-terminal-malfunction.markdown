---
layout: post
title:  "Terminal Malfunction"
date:   2016-09-24 00:00
categories: [linux]
tags: [tips-tricks, shell]
slug: terminal-malfunction
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---

Occasionally, something I do at the terminal causes the terminal to misbehave at the end of the output. This usually happens when I display a binary file. For example -

```
cat /bin/bash
```

Sometimes, you can reset the terminal from the menu. A few times, this has not worked either. At such times, I usually quit the terminal and start a new window. I did not have to. All I needed to do was -

```
stty sane
```

And the terminal was back to normal behaviour - input echo and all. What does `stty sane` do?

```
sane   same  as  cread -ignbrk brkint -inlcr -igncr icrnl icanon iexten echo echoe echok -echonl -noflsh -ixoff -iutf8 -iuclc -ixany imaxbel -xcase -olcuc -ocrnl opost -ofill onlcr -onocr -onlret nl0 cr0 tab0 bs0 vt0 ff0 isig -tostop -ofdel -echoprt echoctl echoke -extproc -flusho

all special characters to their default  values
```

Effectively, this command resets the terminal to all default values. This should save me a lot of trouble in future.
