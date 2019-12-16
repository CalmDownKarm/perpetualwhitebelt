---
date: 2018-10-16T09:00:00+06:30
lastmod: 2018-10-16T09:00:00+06:30
title: A twitter bot under 20 lines of code and 30 lines of configuration
tags:
  - Technical
slug: twitterbot
---

A project underway at the lab in IIITDelhi that I'm volunteering at involves collecting a decently large collection of tweets. The bot needs to run at regular intervals, hit the search API with a series of keywords and store the returned tweets. More than that, it needed to be written completely in python and fairly difficult for a bored undergrad to break. I chose Gramex - since I work for the company that builds it, and I hadn't had a chance to test our TwitterRESTHandler yet. 
The source code is available [here](https://github.com/Hitkul/Twitter_gramex_bot), but the entire thing runs in 2 files - a `gramex.yaml` configuration file that configures the endpoints and the scheduler and a `twitterbot.py` file.

```yaml
schedule:
  run-every-hour:
    function: twitterbot.make_requests   # Log the current time
    minutes: '*/1' # Every minute
    hours: '*'
    startup: '*'
    thread: true

url:
  twitter: 
    pattern: /$YAMLURL/twitter/(.*)
    handler: TwitterRESTHandler
    kwargs:
      key: ... # API Keys from twitter
      secret: ...
      access_key: ...
      access_secret: ...
      transform:
        writetodisk:
          function: twitterbot.writedisk
    redirect:
      header: Referer
      url: /$YAMLURL/

variables:
  URLROOT: http://localhost:9988/twitter/search/tweets.json?q=
  keywords:
    - gramener
    - python
```
The gramex.yaml file defines 1 endpoint at `/twitter/` which exposes the TwitterRestHandler - by hitting this endpoint with the following arguments `/search/tweets.json` tells it to use the search API and setting `?q=<keyword>` tells it which keywords to search for. Unfortunately, I ended up hardcoding the url in the yaml itself - this would need to be changed if the bot is ever run on a different port - had I been able to use javascript, I would probably have just used relative urls instead. 
It also defines a scheduler which calls the `make_requests` function in `twitterbot.py` and schedules it to run every minute. setting `thread:` to `true` means that the function will run in a seperate thread - but the function itself runs on a single thread - if the set of keywords ever expands by a lot, we'd have to run each request through a threadpool/seperate process, but at the moment it isn't necessary. 
For completeness's sake, here's the python file as well, though there's nothing particularly special here

```python
import os
import json
import requests
from gramex.config import variables

def make_requests():
    for keyword in variables['keywords']:
        r = requests.get(''.join([variables['URLROOT'], keyword]))
    return r.status_code # Yes, this will only return the last status code - 
                         # but each request will get logged anyway so this return 
                         # is essentially meaningless. 

def writedisk(result, handler):
    dir = os.path.dirname(os.path.abspath(__file__))
    with open(os.path.join(dir, 'tweets.jsonl'), 'a') as out:
        for status in result['statuses']:
            json.dump(status, out)
    return result
```
