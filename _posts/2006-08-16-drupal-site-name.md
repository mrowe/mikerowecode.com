---
layout: post
title: Setting the site name of a Drupal site
---

I just spent a frustrating couple of hours beating my head against a
Drupal site, trying to change the site name (that appears in the site
banner and title). No matter what I set in the `admin/settings` page,
the site name remained unchanged. The database was being updated
correctly:

    mysql> select * from variable where name = 'site_name'\G
    *************************** 1. row ***************************
     name: site_name
    value: s:20:"My Fancy Drupal Site";

but the old name was still displaying on the site (and every time I
went back in to `admin/settings`, the name had reverted).

Finally, I remembered this section of settings.php:

    /**
     * Variable overrides:
     *
     * To override specific entries in the 'variable' table for this site,
     * set them here. You usually don't need to use this feature. This is
     * useful in a configuration file for a vhost or directory, rather than
     * the default settings.php. Any configuration setting from the 'variable'
     * table can be given a new value.
     */
    $conf = array(
      'site_name' => 'My Drupal Site',
    //  'theme_default' => 'bluemarine',
      'anonymous' => 'Visitor'
    );

**D'oh!**

So I commented out that `'site_name'` line, and everything worked as
expected.
