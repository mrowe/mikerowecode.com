New home for a blog

My blog has moved to a dedicated new home:
[http://www.mikerowecode.com/](http://www.mikerowecode.com/)

All appropriate redirects are in place, but please check your feed
reader to be sure. I've done a far-reaching survey of a wide range of
users and clients--ok, well, actually myself and one friend, both
using [NetNewsWire][8]--and it seems that it works fine when the feed
is accessed directly, but if you have it syncing via NewsGator it
doesn't correctly propagate the new feed URL. It *does* follow the
redirect to get the feed content, but doesn't to push the changed URL
back to the client. I'd be interested to
[hear](mailto:mikerowe@mikerowecode.com) about experiences with other
readers.

### A word about what's behind curtain

The new site is built from text files using the [blosxom][1]
publishing system. The text files are formated using John Gruber's
[Markdown][2], with punctuation fixed by his [SmartyPants][3].

I use a number of plugins for blosxom to get things working the way I
want. These include `archives` and `recententries` to provide the
navigation options in the sidebar, `entries_index` to maintain article
time stamps and `atomfeed` to produce, er, an [atom feed][4]. :) 

Blosxom runs in "static" mode to generate the site locally, and then I
`rsync` it to my web server, where it's served as static HTML.

#### Why blosxom?

It probably seems like a strange choice, when there are so many
"advanced" alternatives such as [Drupal][5] (which was my previous
system), WordPress, MovableType, Blogger, etc., etc. But a couple of
things convinced me that blosxom was the way to go.

First, my needs are minimal. I just want to publish the stuff I write with
the minimum of fuss and overhead. I wanted a publishing system that
would get out of the way.

Second, there is something very appealing about keeping things in
plain text. I can write in [emacs][6] (which is of course the One True
Editor), manage changes with [git][7], search with grep (or
spotlight). The directory layout is the same on my hard disk as on the
public server. There's no database to worry about backing up.

Finally, since I'm serving static HTML, in the (admittedly
far-fetched) event that this site becomes wildly popular and sees huge
amounts of traffic, scaling will be trivial. :)

[1]: http://www.blosxom.com/
[2]: http://daringfireball.net/projects/markdown/
[3]: http://daringfireball.net/projects/smartypants/
[4]: http://mikerowecode.com/index.atom
[5]: http://drupal.org/
[6]: http://www.gnu.org/software/emacs/
[7]: http://git.or.cz/
[8]: http://www.newsgator.com/Individuals/NetNewsWire/
