---
layout: post
title: Upgrade of moinmoin (wiki) in debian sid to 1.3x
---

Upgrade of [moin][] in debian sid is not quite as smooth as it could
be... so, apart from running the backup and migrate scripts as
described in `/usr/share/doc/moin/README.migration.gz`, I had to:

* copy `/usr/share/moin/server/moin.cgi` to `/home/www/wiki/`

* create a `/etc/moin/mywiki.py` (from `/etc/moin/moinmaster.py`)
    and customise it more or less like the existing
    `/home/www/wiki/moin_config.py`

* add an entry to `/etc/moin/farmconfig.py` for the new config file

[moin]: http://moinmoin.wikiwikiweb.de/ "MoinMoin"
