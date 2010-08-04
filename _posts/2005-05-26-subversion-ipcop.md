---
layout: post
title: Subversion and IPCop
---

In the default configuration, [IPCop][]'s transparent proxy does not
allow [subversion][] to work (when using WebDAV--ssh would be fine of
course). 

Although the [squid][] proxy supports WebDAV, subversion uses some
non-standard methods that squid blocks.

For example, trying to check out a module fails:

    svn: REPORT request failed on '/svn/mojain/!svn/vcc/default'
    svn: REPORT of '/svn/mojain/!svn/vcc/default': 400 Bad Request (http://...)

Adding the following lines to squid.conf (which on IPCop 1.4.6 is
located at /etc/squid/squid.conf) allows svn to work:

    extension_methods request REPORT
    extension_methods request MKACTIVITY
    extension_methods request CHECKOUT
    extension_methods request MERGE

You need to restart squid for this to take effect:

    /usr/local/bin/restartsquid

[IPCop]: http://www.ipcop.org/
[subversion]: http://subversion.tigris.org/
[squid]: http://www.squid-cache.org/
