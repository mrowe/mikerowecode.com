---
layout: post
title: A Patch to make blosxom's entries_index plugin work with symlinks
date: "2005-04-26"
url: "/2005/04/entries_index_symlink.html"
---

I've been spending some time reorganising my blog and general revision
management. Part of this has been to bring it all into a Subversion
server, which went well, and to set up blosxom to statically render
(so I can just publish HTML files, rather than hit the CGI script for
every page view).

This presented a small problem: I use a Mac as my primary machine, but
I'm deploying to a linux host. I wanted to set up svn hooks to
actually do the render (rather than render locally and upload as a
separate step). This gives me a nice workflow for blogging, as I can
write away and work on stuff for as long as I like, and when I'm ready
to publish I just commit to svn and it automagically gets published on
the site.

Anyhow, the problem... because I want to (a) work in a svn-managed
directory locally, (b) view the site locally, which under Mac OS X
defaults to ~/Sites, and (c) publish to ~/public_html on the linux
box, I need to use symlinks. But this didn't work with blosxom's
static rendering. :(

It turns out that blosxom, or in my case the entries\_index plugin,
uses File::Find, which defaults to _not_ following symlinks. So
without further ado, herewith and henceforth, a patch to entries_index
to cause it to follow symlinks:

    Index: plugins/entries_index
    ===================================================================
    --- plugins/entries_index	(revision 21)
    +++ plugins/entries_index	(working copy)
    @@ -18,9 +18,39 @@
       1;
     }
     
    +my(%files, %indexes);
    +
    +sub process_files {
    +    my $d; 
    +    my $curr_depth = $File::Find::dir =~ tr[/][]; 
    +    if ( $blosxom::depth and $curr_depth > $blosxom::depth ) {
    +      $files{$File::Find::name} and delete $files{$File::Find::name};
    +      return;
    +    }
    + 
    +    $File::Find::name =~ m!^$blosxom::datadir/(?:(.*)/)?(.+)\.$blosxom::file_extension$!
    +      and $2 ne 'index' and $2 !~ /^\./ and (-r $File::Find::name)
    +        # to show or not to show future entries
    +        and (
    +          $blosxom::show_future_entries
    +          or stat($File::Find::name)->mtime <= time
    +        ) 
    +          and ( $files{$File::Find::name} || ++$reindex )
    +            and ( $files{$File::Find::name} = $files{$File::Find::name} || stat($File::Find::name)->mtime )
    +              # Static
    +              and (
    +                param('-all') 
    +                or !-f "$blosxom::static_dir/$1/index." . $blosxom::static_flavours[0]
    +                or stat("$blosxom::static_dir/$1/index." . $blosxom::static_flavours[0])->mtime < stat($File::Find::name)->mtime
    +              )
    +                and $indexes{$1} = 1
    +                  and $d = join('/', (blosxom::nice_date($files{$File::Find::name}))[5,2,3])
    +                    and $indexes{$d} = $d
    +                      and $blosxom::static_entries and $indexes{ ($1 ? "$1/" : '') . "$2.$blosxom::file_extension" } = 1;
    +}
    +
     sub entries {
       return sub {
    -    my(%files, %indexes);
     
         if ( open ENTRIES, "$blosxom::plugin_state_dir/.entries_index.index" ) {
           my $index = join '', <ENTRIES>;
    @@ -32,34 +62,7 @@
         for my $file (keys %files) { -f $file or do { $reindex++; delete $files{$file} }; }
     
         find(
    -      sub {
    -        my $d; 
    -        my $curr_depth = $File::Find::dir =~ tr[/][]; 
    -        if ( $blosxom::depth and $curr_depth > $blosxom::depth ) {
    -          $files{$File::Find::name} and delete $files{$File::Find::name};
    -          return;
    -        }
    -     
    -        $File::Find::name =~ m!^$blosxom::datadir/(?:(.*)/)?(.+)\.$blosxom::file_extension$!
    -          and $2 ne 'index' and $2 !~ /^\./ and (-r $File::Find::name)
    -            # to show or not to show future entries
    -            and (
    -              $blosxom::show_future_entries
    -              or stat($File::Find::name)->mtime <= time
    -            ) 
    -              and ( $files{$File::Find::name} || ++$reindex )
    -                and ( $files{$File::Find::name} = $files{$File::Find::name} || stat($File::Find::name)->mtime )
    -                  # Static
    -                  and (
    -                    param('-all') 
    -                    or !-f "$blosxom::static_dir/$1/index." . $blosxom::static_flavours[0]
    -                    or stat("$blosxom::static_dir/$1/index." . $blosxom::static_flavours[0])->mtime < stat($File::Find::name)->mtime
    -                  )
    -                    and $indexes{$1} = 1
    -                      and $d = join('/', (blosxom::nice_date($files{$File::Find::name}))[5,2,3])
    -                        and $indexes{$d} = $d
    -                          and $blosxom::static_entries and $indexes{ ($1 ? "$1/" : '') . "$2.$blosxom::file_extension" } = 1;
    -      }, $blosxom::datadir
    +      { wanted => \&process_files, follow => 1 }, $blosxom::datadir
         );
     
         if ( $reindex ) {

Phew.

There's probably a neater way to do that, but it Works For Me.

