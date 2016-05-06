---
layout: post
title: "Push Notifications from MATLAB"
date: "2015-06-03 00:00"
slug: push-notifications-from-matlab
permalink: /:title/
category: [linux, MATLAB]
tags: [tips-tricks, communications]
author: Bharath Bhushan Lohray
comments: true
---

I often have to run programs in MATLAB that takes several minutes to hours to run. Most of these times, I am using the parallel processing toolbox and have my computer so bogged down that even moving the mouse is a painfully slow. These are the times I am forced to take a break and walk away from the computer.

I always wanted MATLAB to have a feature that would send me an email when a program execution terminates. While MATLAB supports emailing, it is pretty had to setup especially if the network admin has blocked ports and protocols. Moreover, you would have to leave your credentials in the script in plain text. I was planning to create a PHP based web app that MATLAB could call and have the PHP app send an email or a push notification. While push notification seemed a much better idea, I was not looking forward to developing my own andriod app for it.

Thankfully, I found a service called Pushbullet ([Pushbullet](http://www.pushbullet.com/)). Pushbullet provides an [API](https://docs.pushbullet.com/) that allows one to quickly and easily develop apps to send push notifications to mobile devices. Ofcourse, you will need to install the [pushbullet apps](https://www.pushbullet.com/apps) on to your mobile devices. Additionally pushbullet allows users to interact with phone notifications form their PCs using browser plugins or system applications.

Using the pushbullet API, I developed an easy to use MATLAB script to send push notifications from MATLAB to my phone. The source may be found on my github repository - [pbNotifier](https://github.com/lordloh/pbNotifier). You may also get it from [MATLAB Central File Exchange](https://www.mathworks.com/matlabcentral/fileexchange/47882-pushbullet-notifier).

### Getting Started

To start using the script, signup for a pushbullet account - https://www.pushbullet.com/. Once you have the account setup, get your account specific access token from - https://www.pushbullet.com/account

Create an object of the `pbNotifer` class

```pbO = pbNotifier(accessToken)```

Once you have created the object, you may call the notify method.

```return = pbO.notify(message)```

This sends the message to all devices registered on your account.

### Example

```
pbO = pbNotifier('zGtDxXr1EP3euf1R2q5i8GxUb3SpoOjg');
...
...
pbO.notifier('Execution Complete')
```

![Notification Bar Push Notification](https://raw.githubusercontent.com/lordloh/pbNotifier/res/img/NotificationBar.png "Notification Bar Push Notification")

![Notification Bar Push Notification Slide](https://raw.githubusercontent.com/lordloh/pbNotifier/res/img/SlideingNotification.png "Notification Bar Push Notification Slide")

![Pushbullet App](https://raw.githubusercontent.com/lordloh/pbNotifier/res/img/pushbulletApp.png "Pushbullet App")
