---
layout: post
title:  "Bootstrap Like Responsive CSS"
date:   2016-12-12 00:00
categories: [webworks]
tags: [web]
slug: bootstrap-like-responsive-css
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
excerpt_separator: <!--more-->
---
Several years back, I created my web based CV. It was an awesome website and I was very proud of it. Below are a few screen shots. I was very proud of this and told the first person I met to check it out. He whipped out his phone and went to the URL - https://bharath.lohray.com/cv. The page I saw was a huge disappointment!
<!--more-->

![Education - as seen on a desktop](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/education_pg_dt.png)

__Education - as seen on a desktop__


![Project - as seen on a desktop](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/projects_pg_dt.png)

__Project - as seen on a desktop__


![Work - as seen on a desktop](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/work_pg_dt.png)

__Work - as seen on a desktop__

While the CV website looked fantastic on a desktop, they did poorly on a mobile device.

![Education - as seen on a mobile device](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/education_pg_m.png) ![Project - as seen on a mobile device](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/projects_pg_m.png) ![Work - as seen on a mobile device](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/work_pg_m.png)

I had not given a single thought to how the site would look on a mobile device. After all the efforts I had put to get it to work, I had no heart to tweak (rewrite from scratch) the page any further. Now, several years later, I decided to rewrite my online CV this time with the "Mobile First" philosophy in mind. Over the years I discovered that using a pre-built CSS framework was the most predictive way to build a page that would works across all device sizes. I took a special liking to [Bootstrap CSS](https://getbootstrap.com/css/). After a few projects on Bootstrap, I discovered that I was using Bootstrap CSS, jQuery, Bootstrap JS and jQuery UI and add jQuery Mobile to the mix if it were a mobile website.

In spite of all the libraries bloating my page, I still did not get every thing I wanted. I was hesitant to modify any of Bootstrap CSS files as this would be a major hurdle to upgrading Bootstrap with newer versions. So, I decided to take the best of Bootstrap CSS and make it my own. And the best of Bootstrap CSS is it's responsiveness!

```css
/*
A CSS skeleton extracted from bootstrap.css to define responsive classes.
Make sure you define classes for each screen size and you should have a responsive design :-)
*/

/* print */
@media print {
}

/* xs */
@media (max-width: 767px) {
}

/* sm+ */
@media (min-width: 768px) {
}

/* md+ */
@media (min-width: 992px) {
}

/* lg */
@media (min-width: 1200px) {
}

/* sm */
@media (min-width: 768px) and (max-width: 991px) {
}

/* md */
@media (min-width: 992px) and (max-width: 1199px) {
}

/* some deprecated IE stuff - don't worry about it! */
@-ms-viewport {
}
```
[Here](https://gist.github.com/lordloh/88ed45ed8875251e92ba9614300bf180) is the gist containing the code.

This CSS skeleton extracted from `bootstrap.css` lets me fill in my styles across the various screen sizes and bring responsiveness to my pages with the greatest ease. Here are the results of my 3-2-1 fall back multi-column CSS - very impressive!

![Large device](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/projects_pg_lg.png)

![Large device](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/projects_pg_md.png)

![Large device](https://s3.amazonaws.com/cdn.bharath.lohray.com/weblog/im/bootstrap-like-responsive-css/projects_pg_sm-xs.png)
