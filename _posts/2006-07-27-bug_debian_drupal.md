---
layout: post
title: Bug in Debian Drupal package
---

There's a small bug in the just-updated [Debian][1] [Drupal][2]
package in sarge (see bug #368835). If you just installed the updated
deb from security.debian.org (4.5.3-6.1sarge1), you may see this
message when trying to access your drupal site:

[1]: http://www.debian.org/
[2]: http://drupal.org/

    Parse error: parse error, unexpected T_STRING in /usr/share/drupal/includes/file.inc on line 106

This is caused by a missing semi-colon in file.inc (on line 106,
surprisingly enough). This patch fixes it:

    --- file.inc	2006-07-27 21:42:57.000000000 +1000
    +++ file.inc.NEW	2006-07-27 21:42:51.000000000 +1000
    @@ -102,7 +102,7 @@
           fclose($fp);
         }
         else {
    -	  $message = t("Security warning: Couldn't write .htaccess file. Please \
        create a .htaccess file in your %directory directory which contains the \
        following lines: <code>%htaccess</code>", array('%directory' => theme('placeholder', \
        $directory), '%htaccess' => '<br />'. str_replace("\n", '<br />', \
        check_plain($htaccess_lines))))
    +	 $message = t("Security warning: Couldn't write .htaccess file. Please \
        create a .htaccess file in your %directory directory which contains the \
        following lines: <code>%htaccess</code>", array('%directory' => $directory, \
        '%htaccess' => '<br />'. str_replace("\n", '<br />', \
        check_plain($htaccess_lines))));
           form_set_error($form_item, $message);
     	  watchdog('security', $message, WATCHDOG_ERROR);
         }

This appears to have already been reported in bug #[380022][3].

[3]: http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=380022
