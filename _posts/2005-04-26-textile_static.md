---
layout: post
title: Blosxom static rendering and the Textile plugin
---

There's a known problem with the Textile plugin for blosxom in static
rendering mode. <http://groups.yahoo.com/group/blosxom/message/4052>

I reproduce the relevant patch here:

    --- plugins/1textile2   (revision 21)
    +++ plugins/1textile2   (working copy)
    @@ -51,6 +51,7 @@
     #   image_rel_base     relative image filenames relative to this

     sub blostile {
    +    local $_;
         my $str = shift @_;
         my $ctx = shift @_;
         if (!$textile) {
