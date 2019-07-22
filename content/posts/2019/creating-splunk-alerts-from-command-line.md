---
title: "Creating Splunk Alerts (aka 'Saved Searches') from the command line"
date: 2019-07-22T17:24:13+10:00
draft: true
---


[Splunk][] [Alerts][] (also called saved searches) are a great way to
have Splunk send you data on a scheduled basis, or when certain
conditions are met (e.g. a metric crosses a threshold). While these
alerts can easily be created in the web UI (by clicking "Save
As/Alert" in a search) in many cases would be nice to do it
programmatically. This makes it easy to set up alerts for many
individual searches, while keeping everything under source control.

[Splunk]: https://www.splunk.com/en_us/software/splunk-enterprise.html
[Alerts]: https://docs.splunk.com/Documentation/Splunk/7.3.0/Alert/Definescheduledalerts

## Specifying the search

First of all, let's specify our search. We will put each search in its
own text file. As an example, here is a really simple search that
counts the number of 404 errors by `sourcetype`:

```
index=* | stats count(eval(status="404")) AS count_status BY sourcetype
```

This is the same search definition as you would enter in the Splunk
search app UI. It's a good idea to test and refine the search
interactively until you're happy with it, then save it in a file
called `my_search.txt`.

## The Splunk API

Next we're going to write some Python to drive the Splunk API. The
following code assumes you have a valid auth token in the
environment variable `SPLUNK_AUTH_TOKEN`. Here is a little helper
script you can use to set this variable (assuming you have `xmllint`
installed):

    
{{< highlight bash >}}
 #!/bin/sh
 #
 # login to splunk and set SPLUNK_AUTH_TOKEN
 #
 # Usage: eval $( ./splunk-login.sh ) 
 #
  
 SPLUNK_HOST="https://splunk.int.corp:8089" 
  
 read -p "Username: " USERNAME 
 read -s -p "Password: " PASSWORD 
 echo >&2 
  
 response=$( curl -s -d "username=${USERNAME}&password=${PASSWORD}" -k ${SPLUNK_HOST}/services/auth/login ) 
  
 SPLUNK_AUTH_TOKEN=$( echo $response | xmllint --nowarning --xpath '//response/sessionKey/text()' - 2>/dev/null ) 
 if [[ $? -eq 0 ]] ; then 
     echo "export SPLUNK_AUTH_TOKEN=${SPLUNK_AUTH_TOKEN}" 
 else 
     echo $response | xmllint --xpath '//response/messages/msg/text()' - >&2 
     echo >&2 
 fi 
{{< /highlight >}}

You'll also need to install the [Splunk SDK for Python][]. This should
be as simple typing `pip install splunk-sdk` as depending on how your
environment is configured.

Ok, on to the code!

[Splunk SDK for Python]: https://dev.splunk.com/python

{{< highlight python "linenos=table" >}}
#!/usr/bin/env python

import os
from splunklib import client

splunk_host = os.getenv("SPLUNK_HOSTNAME", "splunk.int.corp")
splunk_token = os.getenv("SPLUNK_AUTH_TOKEN")
splunk_app = 'my_app'

service = client.Service(host = splunk_host, token = splunk_token, app = splunk_app)
{{< / highlight >}}


Great, we now have a reference to the Splunk API service. So how do we use the SDK to
create a saved search? The [Splunk API documentation][] is slightly
terrifying. Luckily, we don't need to worry about the vast majority of
the available parameters. Let's create an alert that runs on a
schedule and sends its results to a webhook:

[Splunk API documentation]: https://docs.splunk.com/Documentation/Splunk/latest/RESTREF/RESTsearch#saved.2Fsearches

{{< highlight python "linenos=table,linenostart=12,hl_lines=6-7 13 14-15" >}}
filename = 'my_search.txt'
search_name = os.path.splitext(os.path.basename(filename))[0]
search = open(filename, 'r').read()

params = {
    'actions': 'webhook',
    'action.webhook.param.url': 'http://splunk-webhook-service/',
    'alert_comparator': 'greater than',
    'alert_threshold': '0',
    'alert_type': 'number of events',
    'alert.digest_mode': '0',
    'alert.suppress': '0',
    'cron_schedule': '0 1 * * *',
    'dispatch.earliest_time': '-30d@d',
    'dispatch.latest_time': 'now',
    'display.general.type': 'statistics',
    'display.page.search.mode': 'fast',
    'display.page.search.tab': 'statistics',
    'is_scheduled': '1',
    'request.ui_dispatch_app': splunk_app,
    'request.ui_dispatch_view': 'search'
}

service.savedsearches.create(search_name, search, **params)
{{< / highlight >}}

And that's it! Easy.

There's a few things in that dictionary of parameters to the API call
that are worth calling attention to.

 * The first two lines (**#17-18**) specify the action to trigger for
   our alert: in this example, a webhook and the URL that Splunk will
   POST results to. You could also specify `'actions': 'email'` and
   `'action.email.to'` to send the alert as an email instead__*__.
 
 * Line **#24** specifies when to run the saved search, as a crontab
   entry. This example is "daily at 1:00 am" (in the Splunk server's
   time zone).
 
 * Lines **#25-26** define the time range over which to run the search
   (in this case "last 30 days").
 
 * The digest mode and alert threshold settings (lines **#20** and
   **#22** ) cause this alert to send results every time it is invoked,
   rather than depending on a condition being met. Creating a
   conditional alert is left as an exercise for the reader...
 
(*) _Note that I have not yet tested this, so I'm not sure which other
parameters are required._


## Fully worked example

The above code is all you actually need, but here's a slightly
expanded example that accepts command line arguments and multiple
search files. It also deletes any existing search of the same name
(i.e. your new search replaces the old one), and randomises the
`crontab` spec slightly to spread out load on the Splunk server.

{{< highlight python "linenos=table" >}}
#!/usr/bin/env python 
 
import os, argparse, random 
from splunklib import client 
 
splunk_host = os.getenv("SPLUNK_HOSTNAME", "splunk.int.corp") 
splunk_token = os.getenv("SPLUNK_AUTH_TOKEN") 
 
def create_alert(search_name, search, splunk_app, webhook_host): 
    crontab = "%s 1 * * *" % random.randint(1, 59) # pick a random minute during 01:01 - 01:59 
    params = { 
        'actions': 'webhook', 
        'action.webhook.param.url': webhook_host, 
        'alert_comparator': 'greater than', 
        'alert_threshold': '0', 
        'alert_type': 'number of events', 
        'alert.digest_mode': '0', 
        'alert.suppress': '0', 
        'cron_schedule': crontab, 
        'dispatch.earliest_time': '-30d@d', 
        'dispatch.latest_time': 'now', 
        'display.general.type': 'statistics', 
        'display.page.search.mode': 'fast', 
        'display.page.search.tab': 'statistics', 
        'is_scheduled': '1', 
        'request.ui_dispatch_app': splunk_app, 
        'request.ui_dispatch_view': 'search' 
    } 
 
    service = client.Service(host = splunk_host, token = splunk_token, app = splunk_app) 
    savedsearches = service.saved_searches 
    try: 
        savedsearches.delete(search_name) 
        print("Deleted old version of %s" % search_name) 
    except Exception: 
        pass 
 
    savedsearches.create(search_name, search, **params) 
    print("Created alert '%s' to be triggered at %s" % (search_name, crontab)) 
 
 
if __name__ == '__main__': 
    parser = argparse.ArgumentParser(description=""" 
        Create Splunk alerts. Assumes there is a valid auth token in SPLUNK_AUTH_TOKEN 
        (e.g. `eval $(./splunk-login.sh )`). Default Splunk API hostname (splunk.int.corp)  
        can be overridden by setting SPLUNK_HOSTNAME. 
    """) 
    parser.add_argument('files', metavar='file', type=str, nargs='+', help='a file that contains a search definition') 
    parser.add_argument('--webhook_url', nargs='?', dest='webhook_host',  
                        default='https://splunk-webook-service.int.corp/', 
                        help='the URL of the webhook the alert will be configured to POST to') 
    parser.add_argument('--splunk_app', nargs='?', dest='splunk_app', 
                        default='search',  
                        help='the name of the Splunk app that will own the search (defaults to "search")') 
 
    args = parser.parse_args() 
 
    for f in args.files: 
        name = os.path.splitext(os.path.basename(f))[0] 
        search = open(f, 'r').read() 
        create_alert(name, search, args.splunk_app, args.webhook_host) 
{{< /highlight >}}



## Conclusion
 
So despite the somewhat lacking official documentation, creating
Splunk saved searches is actually pretty straightforward. Thanks to
_Alexander Leonov_ for this post that got me headed in the right
direction:
https://avleonov.com/2019/01/17/creating-splunk-alerts-using-api/
 
 
 
