---
layout: post
title:  "Searching through Bash History?"
date:   2016-04-05 00:00
categories: [linux]
tags: [tips-tricks, shell]
slug: Searching-through-bash-history
permalink: /:title/
comments: true
author: Bharath Bhushan Lohray
---
I am a frequent MATLAB user and have always liked the feature of recalling the command history after typing the first few characters. For instance, if my command history is -

```
java -jar htmlcompressor-1.5.3.jar -r --compress-js --compress-css -o /data/blog/karyashala/_site/ /data/blog/karyashala/_site
ls -alh
find . | gerp -i signed
ps aux | grep -i java
kill 12345
```

Now, after executing the last command, should I want to recall the first command, I can type -

```
java <up arrow>
```

and have the command `java -jar htmlcompressor-1.5.3.jar -r ...` populated at the shell prompt.

Unfortunately this is not the default behaviour of Bash in most Linux installations. However, this may be remedied with just 2 lines of configuration in the `~/.inputrc` file -

```
"\e[A": history-search-backward  ## up-arrow
"\e[B": history-search-forward
```

Make sure than the file `.inputrc` has execute permissions. Logout and log back in or restart the terminal and make your life a lot easier.
