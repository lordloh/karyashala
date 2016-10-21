---
layout: post
title:  "Retrieving Historical Stock prices."
date:   2016-10-21 00:00
categories: [programming]
tags: [code]
slug: yql-historical-stock-data
permalink: /:slug/
comments: true
author: Bharath Bhushan Lohray
---
Yahoo Open Data Tables is probably the last free source of financial information available after Google shut down it's finance API a few years back. In a recent project to analyze my stocks trades, I decided to cache 10 year historical prices form the [Yahoo Open Data Tables](https://developer.yahoo.com/yql/console/?q=show%20tables&env=store://datatables.org/alltableswithkeys#h=select+*+from+yahoo.finance.historicaldata+where+symbol+%3D+%22YHOO%22+and+startDate+%3D+%222016-10-01%22+and+endDate+%3D+%222016-10-30%22).

Yahoo API queries are syntactically similar to SQL. For instance, to retrieve the stock prices for "YHOO" for the month of October 2016, I would structure my query as -

```SQL
select * from yahoo.finance.historicaldata where symbol = "YHOO" and startDate = "2016-10-01" and endDate = "2016-10-30"
```

However, an attempt to set `startDate = "2006-01-01"` and `endDate = "2016-10-01"` gets me -

```json
{
  "query": {
    "count": 0,
    "created": "2016-10-21T05:09:05Z",
    "lang": "en-US",
    "results": null
  }
}
```
additionally, the diagnostic information reveals that the error thrown is -
```json
{
  "name": "yahoo.finance.historicaldata",
  "verb": "select",
  "content": "Too many instructions executed: 50220753"
}
```
So, the way around was to use map the time range into several 365 day ranges and finally merge the results (reduce). To this end I wore a python function to fetch the data and a wrapper that would perform the map-reduce.

Here is my code -

```python
import json
import urllib
import time
from datetime import datetime, timedelta, date

def yql_stock_history(symbol,start_date,end_date):
    endpoint = "https://query.yahooapis.com/v1/public/yql"
    env = "store://datatables.org/alltableswithkeys"
    response_format = "json"
    query='select * from yahoo.finance.historicaldata where symbol = "'+symbol+'" and startDate = "'+start_date+'" and endDate = "'+end_date+'"'
    params = {'format':response_format, 'env':env, 'q':query}
    req_data = urllib.parse.urlencode(params).encode('ascii')
    try:
        res = urllib.request.urlopen('https://query.yahooapis.com/v1/public/yql',req_data)
        data = res.read().decode('utf-8')
        res.close()
    except urllib.error.URLError as e:
        print(e.code)
        rise(e.code)
    json_parsed = json.loads(data)['query']['results']
    if (json_parsed != None):
        return json_parsed['quote']
    else:
        return None

def yql_stock_long_history(symbol, start_date, end_date):
    start_d=datetime.strptime(start_date, '%Y-%m-%d')
    end_d=datetime.strptime(end_date, '%Y-%m-%d')
    diff=end_d - start_d
    max_days = 365
    if (diff.days < 0):
        return None
    elif(diff.days > max_days):
        data_part = []
        s = start_d
        while((end_d-s).days >= max_days):
            price_history=yql_stock_history(symbol, str(s), str(s+timedelta(max_days)))
            s=s+timedelta(days = max_days+1)
            if (price_history == None):
                continue
            data_part.append(price_history)
        price_data = [b for sublist in data_part for b in sublist]
        price_history = yql_stock_history(symbol, str(s), str(s+timedelta(max_days)))
        data_part.append(price_history)
        price_data = [b for sublist in data_part for b in sublist]
        return price_data
    elif(diff.days == 1):
        return [yql_stock_history(symbol, start_date, end_date)]
    else:
        return yql_stock_history(symbol, start_date, end_date)
```
[Here is the gist](https://gist.github.com/lordloh/97f2eb5e6e0c6f1291c54b99dae30e8d) containing the code.

The wrapper `yql_stock_long_history` is the wrapper that performs the map reduce if the query is too large. Feel free to leave comments on the gist page.
