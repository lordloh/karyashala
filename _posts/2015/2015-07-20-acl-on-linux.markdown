---
layout: post
title:  "ACLs on Linux"
date:   2015-07-20 00:00
categories: [linux]
tags: [tips-tricks]
permalink: acl-on-linux
comments: true
author: Bharath Bhushan Lohray
---
After setting up [syncthing](/weblog/syncthing-experience/) last week, I was concerned about security as I was using the software in a manner not intended by the developers. I was attempting to use one instance of syncthing for multiple users by running it under its own user space and granting it permissions on folders in my home directory.

The problem with adding users to the group is that they have permission on every single folder where I grant write permissions to a group. The way around this is to create a huge number of groups with a permutation of users - horrible idea.

I knew that Linux supported Access Control Lists (ACLs), but never had to use them till now. ACLs permit users to have a fine tuned control over what is shared.

Below are a few elementary examples.

**Get a list of ACL permissions on a folder**

```
bharath@localhost:~$ getfacl /home/bharath
getfacl: Removing leading '/' from absolute path names
# file: home/bharath
# owner: bharath
# group: bharath
user::rwx
group::r-x
group:syncthing:--x
mask::r-x
other::r-x
```

**Grant a user or group permission to a file / folder**

```
setfacl -m u:userName:rwx folderPath
setfacl -m g:groupName:rwx folderPath
```

**Remove a user or a group entry from an ACL**

```
setfacl -x u:userName folderPath
setfacl -x g:groupName folderPath
```

**Revoke all permissions**

```
setfacl --remove-all
setfacl -b
```

**Modify default permissions**

```
setfacl -d -m u:userName:rwx folderPath
```

You can read more about this from the [man pages](http://linux.die.net/man/1/setfacl).
