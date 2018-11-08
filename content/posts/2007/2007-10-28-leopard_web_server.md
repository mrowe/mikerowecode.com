---
layout: post
title: Making "personal web sharing" work after Leopard upgrade
date: "2007-10-28"
url: "/2007/10/leopard_web_server.html"
---

My upgrade to Leopard has gone mostly well (if you don't mind waiting
half a day for spotlight to index everything, then another half a day
for time machine to do its first back up). But tonight I tried to
access my "personal website" via the local Apache server, and got a
"Forbidden" message.

It turns out Leopard includes Apache 2.2 (up from the 1.3 in Tiger)
and its configuration now lives in

    /private/etc/apache2 

(not `/private/etc/httpd` as in Tiger and earlier). However, the
upgrade did not bring across my user configuration file
(`/private/etc/httpd/users/mrowe.conf`).

The following commands fixed this for me:

    cp /private/etc/httpd/users/mrowe.conf /private/etc/apache2/users/
    echo 'Include /private/etc/apache2/users/*.conf' >> /private/etc/apache2/httpd.conf
    apachectl graceful

(Obviously you would use your short username where I have "`mrowe`".)

I haven't tried PHP yet...

__Update__: it turns out adding that `Include
/private/etc/apache2/users/*.conf` line to `httpd.conf` is not
necessary. It is taken care of by the line:

    Include /private/etc/apache2/extra/httpd-userdir.conf

earlier in `httpd.conf` that I hadn't noticed. You still need to copy
your user.conf from `/private/etc/httpd/users` to
`/private/etc/apache2/users`.
