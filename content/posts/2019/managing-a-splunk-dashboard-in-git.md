---
title: "Managing a Splunk Dashboard in Git"
date: 2019-04-23T19:09:21+10:00
draft: true
---

Splunk dashboards are great! There's all sorts of useful insight you can gain for your system logs, and graphing them in convenient information "radiators" makes these insights more visible to your team. They do involve a lot of pointing-and-clicking though, and it makes me pretty nervous that all that fiddly work is sitting unmanaged in a web UI.

Luckily, you don't have to live that way. Splunk provides a comprehensive REST API for many search- and data-related actions. It's kind of buried in the [documentation][] under "Knowledge", but the API contains an endpoint that allows you to GET and POST the XML definition of a dashboard:

[documentation]: https://docs.splunk.com/Documentation/Splunk/latest/RESTREF/RESTknowledge#data.2Fui.2Fviews

```
https://<host>:<mPort>/servicesNS/{user}/{app_name}/data/ui/views/{dashboard_name}
```

(Note that `mPort` may be different to the port you use to access the Splunk UI in a web browser. By default the Splunk API uses port 8089.)

GETting the XML source for a dashboard works great, but I have found it to be a little picky about the XML you try to POST back to Splunk. In particular, you're going to be sending XML as "form-encoded" data, so you need to be careful about escaping certain characters. In many cases, you won't be able to just upload the XML exactly as you downloaded it. More on this later.

## Authentication

Splunk uses token-based [authentication][], which is preferable to hard coding a username and password in your script, but it does mean there's an extra step involved.

[authentication]: https://docs.splunk.com/Documentation/Splunk/latest/RESTUM/RESTusing#Authentication_and_authorization

To keep things as secure as possible, I recommend prompting the user for a username/password interactively and never storing them on disk (or in your shell history). For example, this script will output a token that you can `eval` in your shell to set an ENV variable subsequent scripts can use:

{{< highlight python "linenos=table" >}}
#!/bin/sh
#
# login to splunk and set SPLUNK_AUTH_TOKEN
#
# Usage: eval $( ./splunk-login.sh )

SPLUNK_HOST="https://splunk:8089"

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
{{< / highlight >}}

We can then pass that to further API requests as an HTTP header:

```
curl -H "Authorization: Splunk ${SPLUNK_AUTH_TOKEN}" \
  https://splunk:8089/servicesNS/username/app_name/data/ui/views?output_mode=json
```

## Getting dashboard XML

Ok, great, we can now interact with the REST API. You probably don't want to be building your dashboard from scratch in XML, so I'll assume you have set up something in the Splunk UI. The first thing we will do is get its source XML from the API. This script will do that, and send the result to `stdout`:


{{< highlight python "linenos=table" >}}
#!/bin/sh
#
# Get the XML source of a Splunk dashboard
#

if [[ -z "$1" ]] ; then
    echo "Usage: $0 <dashboard_name>"
    exit 1
fi

SPLUNK_HOST="https://splunk:8089"

DASHBOARD_AUTHOR="username"
DASHBOARD_APP="app_name"
DASHBOARD_NAME="$1"

if [[ -z $SPLUNK_AUTH_TOKEN ]] ; then
    eval $( `dirname $0`/splunk-login.sh )
fi

curl -s \
     -H "Authorization: Splunk ${SPLUNK_AUTH_TOKEN}" \
     -k \
     "${SPLUNK_HOST}/servicesNS/${DASHBOARD_AUTHOR}/${DASHBOARD_APP}/data/ui/views/${DASHBOARD_NAME}?output_mode=json" \
    | jq --raw-output '.entry[].content | .["eai:data"]' \
    | xmllint - --pretty
{{< / highlight >}}

We call the script like this:

```
$ ./get-dashboard-source.sh my-dashboard > my-dashboard.xml
```

(Make sure you set the variables for Splunk host, dashboard author, and app name appropriately.)

Now we have our dashboard definition in a file that we can commit to git! I feel safer already.

## Uploading dashboard XML to Splunk

What if we want to make changes to the dashboard? We can just go point and click around in the web UI as usual, then download and commit another version. That's better than nothing. But for many things (changing search strings, tweaking dashboard panel settings, even duplicating and rearranging panels) it's far easier to edit the XML directly.

So go ahead and open the XML file in your [favourite editor][] and tweak away.

[favourite editor]: https://www.gnu.org/s/emacs/

When you're happy, this script will upload the XML to Splunk and update the dashboard definition:

{{< highlight python "linenos=table" >}}
#!/bin/sh
#
# Update a Splunk dashboard with new source XML
#

if [ -z "$1" -o -z "$2" ] ; then
    echo "Usage: $0 <dashboard_name> <dashboard_source.xml>"
    exit 1
fi

SPLUNK_HOST="https://splunk:8089"

DASHBOARD_AUTHOR="username"
DASHBOARD_APP="app_name"
DASHBOARD_NAME="$1"
DASHBOARD_SOURCE="$2"

if [[ -z $SPLUNK_AUTH_TOKEN ]] ; then
    eval $( `dirname $0`/splunk-login.sh )
fi

data_file=$(mktemp)
cat <<EOF > $data_file
eai:data=$( sed -e 's/%/%25/g' -e 's/&gt;/>/g' -e 's/&lt;/</g' < ${DASHBOARD_SOURCE} | tr -d '\n' )
EOF

curl  \
     -H "Authorization: Splunk ${SPLUNK_AUTH_TOKEN}" \
     -X POST \
     --data @${data_file} \
     -k "${SPLUNK_HOST}/servicesNS/${DASHBOARD_AUTHOR}/${DASHBOARD_APP}/data/ui/views/${DASHBOARD_NAME}"

{{< / highlight >}}

This is all pretty straight forward, but you might want to take a close look at line #24, which has a lot going on. Reading from right to left, we are:

 * removing newline characters
 * from the source XML file
 * expanding "greater than" and "less than" entities to `>` and `<` characters
 * URL encoding literal `%` characters
 * and putting the lot in a field called `eai:data`

This is all to deal with the fact that Splunk expects us to send XML as form-encoded data in the POST request. There _may_ be other characters I haven't noticed yet that trip up this encoding/decoding process, but those three (`>`, `<`, and `%`) are particularly common in Splunk dashboard XML and will definitely cause problems.

To use it:

```
$ ./set-dashboard-source.sh my-dashboard my-dashboard.xml
```

Et voilà!


## Next steps

These are the basic building blocks, and enough to implement a manual version control workflow for Splunk dashboards. But it sure would be nice if it was all a bit more seamless.

A couple of obvious bits of further automation would be:

 * a git hook to upload dashboard XML on commit/push
 * a script to ensure the live dashboard matches what's in git before
   making local changes
 * some sort of validation of the XML before uploading (but the API
   will just refuse to accept invalid XML, so this isn't really
   necessary)

