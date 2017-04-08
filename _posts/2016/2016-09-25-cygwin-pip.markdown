---
layout: post
title:  "pip for Cygwin"
date:   2016-09-25 00:00
categories: [linux]
tags: [tips-tricks, shell]
slug: cygwin-pip
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---

I have been using cygwin for a while. To say the least, I get to do X11 forwarding on windows. Windows 10 now has an experimental feature called Bash for Windows. I think this was released along with the Windows 10 Anniversary update. However, I have not succeeded in getting X11 to run on it.

This post is about how to install `pip` for python on cygwin. Cygwin does not have a package that provides pip as Ubuntu does. But getting pip on Cygwin is really easy. [This answer](http://stackoverflow.com/a/31958249/482176) on Stack Overflow outlines the process.

```
python -m ensurepip
```

Then do the same for python 3

```
python3 -m ensurepip
```

Now, upgrade pip to the latest version.

```
pip install --upgarede pip
```

The problem with this is that it makes pip2 the default pip and you loose access to pip3. I got around this by -

```
python3 -m pip install --upgrade pip
```

Phew !!! I can finally install [`youtube-dl`](https://rg3.github.io/youtube-dl/) without hassles -

```
pip3 install youtube-dl
```
