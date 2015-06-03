---
layout: post
title: "MATLAB Startup Folder"
date: "2013-09-26 00:00"
permalink: matlab-startup-folder
comments: true
tags: [tips-tricks]
category: MATLAB
author: Bharath Bhushan Lohray
---

I have been saving all my work on [Dropbox](https://db.tt/t3wKl3e) for a while now. It is convenient especially when all my work is synced across all my computers. The best advantage I have is that when a computer is formatted or crashes, all I have to do is to install the dropbox client again and my files are automatically downloaded from dropbox.

I decided to make my `Dropbox\MATLAB` the startup folder for MATLAB across all my computers. After a  little bit of searching, I discovered that there is a command that does exactly that – [`userpath`](http://www.mathworks.com/help/matlab/matlab_env/matlab-startup-folder.html).

All I had to do is type the following command in my MATLAB command window.

```
userpath('C:\Users\Bharath\Dropbox\MATLAB');
```

Now, on restarting MATLAB, I find myself in `C:\Users\Bharath\Dropbox\MATLAB` instead of `C:\Users\Bharath\Documents\MATLAB`. So everything I do and save is immediately backed up as long as I am connected to the internet.

More information about MATLAB startup can be found [here](http://www.mathworks.com/help/matlab/matlab_env/matlab-startup-folder.html). There are surprisingly many commands that can be used to customize your MATLAB experience – [see these](http://www.mathworks.com/help/matlab/startup-and-shutdown.html).
