---
layout: post
title:  "Hardcoding Passwords and git"
date:   2016-10-21 00:00
categories: [programming]
tags: [code]
slug: hardcoding-passwords-and-git
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---
Recently I was working on building myself a script to download my securities transactions from [Robinhood](http://share.robinhood.com/bharatl) and save it as a CSV. I found an [unofficial python library](https://github.com/Jamonek/Robinhood) that was reverse engineered from the app. As I started building the script, I realized that I could not host it on git. I had hardcoded my credentials in the code.

My first wish was to be able to add a mark up to the code that git would ignore. Looking around I figured out that git does not yet let users to stop tracking changes to specific lines of the code. Sad. But it would not have been a very effective way as preceding lines may change in future. The only solution is not to hard code usernames and passwords.

The solution I came up with was to store my credentials in a `.json` file and to get python read in the `credentials.json` file into a dictionary.

Here is my solution -

```python
import json

with open('credentials.json') as json_data:
    credentials = json.load(json_data)

login(username=credentials['user'] password=credentials['password']")
```
[Here is the gist](https://gist.github.com/lordloh/4a7047049a522570642fd9d941890222) containing the code.

`credentials.json` is as follows. You may add additional attributed such as API_key etc.

```json
 {
   "user":"bharath",
   "password":"bharath_secret_password"
 }
```

I eventually hosted this python script as a CGI on my apache web server. In this case, you can protect your credentials using the following configuration directives -

```
<Files ~ "credentials.json$">
	Require all denied
</Files>
```

Attempting to access `credentials.json` now results in a 400 Forbidden error.

Armed with this snippet, I am looking forward to publishing the _Hello World_ of my FinTech application.
