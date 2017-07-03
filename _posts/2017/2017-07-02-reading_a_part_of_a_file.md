---
layout: post
title:  "Reading a part of a file"
date:   2017-07-03 00:00
categories: [linux]
tags: [tips-tricks, shell]
slug: reading_a_part_of_a_file
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---
Recently I had a problem with a very large file I downloaded and wanted to have an alternative option to re-downloading it. The checksum of the file with the problem was incorrect. So I decided to look for ways to compute the MD5 checksums for parts of the file.

## How to read a part of the file?
```
dd if=<filename> skip=<number of blocks> bs=<block size> count=<no of blocks>
```

So, to read a file from byte 1025 to byte 4096,

```
dd if=lorem_ipsum.txt skip=1024 bs=1 count=3072
```

## Now for the md5sum...

```
dd if=lorem_ipsum.txt skip=1024 bs=1 count=3072 | md5sum
```

