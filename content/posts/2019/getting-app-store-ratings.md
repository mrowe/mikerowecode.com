---
title: "Getting app store ratings without authentication"
date: 2019-04-15T09:21:56+10:00
---

Both the Apple iTunes and Google Play app stores provide extensive
APIs for accessing all sorts of metrics and analytics about your apps.
This is great, but they require authentication--which is a bit of a
pain when you're trying automate.

So I went looking for a _quick and dirty_ way to get current app
ratings from public endpoints. 

## iTunes Connect

As it turns out, Apple provide a "real" API for this:

```
$ curl -s https://itunes.apple.com/au/lookup?id=<my-app-id> | jq .results[].averageUserRating
```


Thanks to user [chaoscoder][] on _Stack Overflow_ for [this answer][],
and also to [alberto-m][] for the "country" tip in [this reply][], for
setting me in the right direction.

[chaoscoder]: https://stackoverflow.com/users/648023/chaoscoder
[this answer]: https://stackoverflow.com/questions/5604886/how-to-get-info-from-the-apple-itunes-app-store-and-mac-app-store
[alberto-m]: https://stackoverflow.com/users/293918/alberto-m
[this reply]: https://stackoverflow.com/questions/5604886/how-to-get-info-from-the-apple-itunes-app-store-and-mac-app-store#comment76530867_5620144


**BIG FAT CAVEAT**: Specifying a country implies that you are only
getting the ratings _for that country_. As far as I can tell, there is
no unauthenticated way to get the aggregate rating for all countries
(other than enumarating all countries in which your app is for sale,
and getting them one by one).

## Google Play

Sadly, Google do not seem to provide a similar public API. They do
make the ratings available on the app's store page however, and it
_appears_ to be wrapped in a `div` tag with a distinct `aria-label`
attribute value:

```
<div class="BHMmbe" aria-label="Rated 3.5 stars out of five stars">3.5</div>
```

We can use this fact, and the fact the page is well
formatted/validated HTML, to apply an XPath query to extract the
rating:

```
$ curl -s 'https://play.google.com/store/apps/details?id=<my-app-id>&hl=en' | \
      xmllint --nowarning --html --xpath '//div[starts-with(@aria-label, "Rated")]/text()' - 2>/dev/null
```
    
**EVEN BIGGER FATTER CAVEAT**: this is super fragile and makes all sorts of
assumptions about how the Play store renders its web pages, is
completely unauthorised by Google, and _could break at any time_. YMMV
etc.

## Wrapping it up in a script

We can put all that together in a bit of Python that can be run as a
scheduled job, and maybe ship its logs to Splunk for later analysis:

```python
#!/usr/bin/env python

import sys, urllib, json, logging
from lxml import etree

apple_app_id = "my-app-id"
google_app_id = "my-app-id"

def setup_custom_logger(name):
    formatter = logging.Formatter(fmt="%(asctime)s [%(levelname)s] %(pathname)s %(message)s", datefmt="%Y-%m-%d %H:%M:%S")
    handler = logging.StreamHandler(stream=sys.stdout)
    handler.setFormatter(formatter)
    logger = logging.getLogger(name)
    logger.setLevel(logging.DEBUG)
    logger.addHandler(handler)
    return logger

def get_itunes_rating(app_id):
    response = urllib.urlopen("https://itunes.apple.com/au/lookup?id=%s" % app_id)
    try:
        data = json.loads(response)
        return data["results"][0]["averageUserRating"]
    except (TypeError, ValueError, KeyError, IndexError):
        logger.warn("Response did not contain expected JSON: %s" % response)
        return ""

def get_google_play_rating(app_id):
    response = urllib.urlopen("https://play.google.com/store/apps/details?id=%s&hl=en" % app_id)
    try:
        data = etree.HTML(response)
        return data.xpath("//div[starts-with(@aria-label, \"Rated\")]/text()")[0]
    except (ValueError, IndexError):
        logger.warn("Response did not contain expected XML: %s" % response)
        return ""

if __name__ == '__main__':
    logger = setup_custom_logger("get-app-ratings.py")
    logger.info("apple_store_rating=%s" % get_itunes_rating(apple_app_id))
    logger.info("google_store_rating=%s" % get_google_play_rating(google_app_id))
```

which produces output like:

```
$ ./get-app-ratings.py
2019-04-08 17:19:57 [INFO] ./get-app-ratings.py apple_store_rating=3.5
2019-04-08 17:19:58 [INFO] ./get-app-ratings.py google_store_rating=3.5
```

Getting that stdout output into Splunk is left as an exercise for the
reader...
