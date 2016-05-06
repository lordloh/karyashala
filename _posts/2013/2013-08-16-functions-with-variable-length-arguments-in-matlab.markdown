---
layout: post
title:  "Functions with variable length arguments in MATLAB"
date:   2013-08-16 00:00
categories: MATLAB
tags: [code]
comments: true
slug: functions-with-variable-length-arguments-in-matlab
permalink: /:title/
author: Bharath Bhushan Lohray
---
Here is a small MATLAB script that demonstrates creating a function with variable length arguments.

```
function rval=testfn(varargin)
  rval=0;
  for i=1:nargin
  display(varargin(i));
end
```

executing this shall output -

```
>> testfn('Hello','World',1,2,3,[4,5;6,7])

    'Hello'

    'World'

    [1]

    [2]

    [3]

    [2x2 double]
```

Now, if we want a function that returns the sum of all the arguments -

```
function rval=testfn(varargin)
  rval=0;
  for i=1:nargin
    rval=rval+varargin{i};
  end
```

```
>> testfn(1,2,3,4,5)

ans =

    15
```

Notice `varargin{i}`. Since it is not possible to predict the data type that shall be passed to the function, MATLAB uses the cell data type. If we attempt to pass a string along with numbers, we shall get an exception. Also -

```
>> testfn(1,2,3,[4,5;6,7])

ans =

    10    11
    12    13
```

If this is not the result you are looking for, you will have to run a lot of checks on the input data before you use it. See [MATLAB help page on Data Type Identification](http://www.mathworks.com/help/matlab/data-type-identification.html). This is a lot of overhead to process. More so, if your function is in a loop and is being called multiple times. So it is best not to use this unless you have no other viable alternatives.
