---
layout: post
title:  "Counting MATLAB Figures"
date:   2017-07-03 00:00
categories: [MATLAB]
tags: [tips-tricks]
slug: counting_MATLAB_fugures
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---
I needed to write a few scatter plot images to disk from my MATLAB scripts. I was creating a few hidden figures for this purpose.

```
figure('Visible','off');
```

The problem with this script was that it soon racked up a lot of hidden figures. These hidden figures consume memory and slow down the computer. So I started looking for a way to count the number of figures that are open in MATLAB and found [this](https://stackoverflow.com/questions/4540604/how-do-i-get-the-handles-of-all-open-figures-in-matlab) answer on stackoverflow.com.

Accoding to the post, the solution that would work is -

```
figHandles = get(groot, 'Children');
length(figHandles)
```

An alterate way to prevent the problem from occuring is by reusing the figures. I did this by - 

```
if (~exist('fid2_fig','var'))
    fid2_fig=figure('Visible','off');
end
...
...
...
if (exist('fid2_fig','var'))
    close(fid2_fig);
    clearvars('fid2_fig');
end
```

This keep track of the figures spawned by assigning the figure handles to a variable. If the figure handle exists and is valid in the second loop, there would be no need to spawn an additional figure.

Now, if a figure (visible) is closed, the variable that holds the figure handle does not vanish from the workspace.


```
fid2_fig = 

  handle to deleted Figure
```

One may check if a figure handle is actually valid by -

```
if (~isvaild(fid2_fig))
    fid2_fig=figure();
end
```
