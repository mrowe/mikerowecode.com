---
layout: post
title: Making PHP work after Leopard upgrade
---

Sure enough, PHP didn't work after upgrading my mac to Leopard. Easy
to fix though:

    $ diff -u /private/etc/apache2/httpd.conf.ORIG /private/etc/apache2/httpd.conf
    --- /private/etc/apache2/httpd.conf.ORIG	2007-10-29 20:25:54.000000000 +1100
    +++ /private/etc/apache2/httpd.conf	2007-10-29 20:26:08.000000000 +1100
    @@ -111,7 +111,7 @@
     LoadModule alias_module libexec/apache2/mod_alias.so
     LoadModule rewrite_module libexec/apache2/mod_rewrite.so
     LoadModule bonjour_module     libexec/apache2/mod_bonjour.so
    -#LoadModule php5_module        libexec/apache2/libphp5.so
    +LoadModule php5_module        libexec/apache2/libphp5.so
     #LoadModule fastcgi_module     libexec/apache2/mod_fastcgi.so

and then 

    apachectl graceful

Perhaps surprisingly, MySQL (installed from the ["official"
package][1]) continued to work without a hitch.

[1]: http://dev.mysql.com/downloads/mysql/5.0.html#macosx-dmg
