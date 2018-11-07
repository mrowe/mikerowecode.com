---
layout: post
title: The finer points of maintaining a firewall
date: "2006-10-02"
url: "/2006/10/firewall_finer_points.html"
---

Last Saturday, I learnt something. (Not that this is an unusual
occurrence, of course.) I was trying to upgrade my [Ubuntu][1] box to
"[edgy eft][2]", and was having trouble downloading packages. It
turned out that my ADSL had gone down. And up. And down. *sigh* So,
after a few reboot cycles of the modem and my firewall, I was back up
again.

[1]: http://www.ubuntu.com/
[2]: https://help.ubuntu.com/community/EdgyUpgrades
[3]: http://www.debian.org/

The upgrade completed without too much pain (nice one, [Debian][3] and
Ubuntu), so I turned my attention to some web site work I needed to
do. Only could get through to my web server. Or mail server. Great, I
thought, I just get my 'net connection back up and my hosting service
goes down. Good excuse to watch the Grand Final, if nothing else. :)

Several hours later, football over for another year and my servers
still not accessible, I thought I'd better look into it a bit more
closely. My hosting service provides ssh access to the Xen console of
my machines, so I tried that. And it worked. And what I saw was many
kernel messages about packets from a "bogon" IP address being blocked.
And the IP in question resolved to an iinet.net.au (my ADSL provider)
domain name. Hmm.

After a bit of investigation, it turned out that with the outage in
the morning, my ADSL IP had changed from a 203.214.*.* network to
124.168.*.*. And this netblock has until relatively recently been
"reserved" by [IANA][4]. As it happens, [Shorewall][5] keeps a static
list of "bogon" (i.e not normally seen on the public Internet) IP
addresses in a configuration file, and this list included the rather
sweeping netblock of 96.0.0.0/3, which results in everything from 96/8
up to 126/8 being blocked. But at the start of this year, IANA
released the class As from 121/8 to 126/8 to APNIC, who gave them
mostly to large Australian ISPs as far as I can tell, and evidently
some those (like iiNet) are starting to roll them out to customers.

[4]: http://www.iana.org/assignments/ipv4-address-space
[5]: http://www.shorewall.net/

Anyway, a quick poke around with Google turned up this updated [bogons
file][6], so I installed that and restarted my firewall, and
everything was fine again. It all goes to show, nothing is simple in
the world of internetworking.

[6]: ftp://shorewall.net/pub/shorewall/errata/2.0.10/bogons
