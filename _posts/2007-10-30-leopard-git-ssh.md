---
layout: post
title: Leopard breaking MacPorts git over ssh
---

As I have been [twitting][1] recently, git over ssh stopped working
for me after the upgrade to Leopard:

    $ git pull
    percent_expand: NULL replacement
    fatal: The remote end hung up unexpectedly
    Cannot get the repository state from ssh://git.mojain.com/...

A quick [google search][2] quickly turned up the answer. The problem
was not with git, but with ssh. Spefically, ssh from [MacPorts][3].
It's worth noting that ssh in OS X 10.5 is *not* broken (which made my
intial trouble-shooting harder, as ssh-ing from the command line
worked just fine). But git in MacPorts is:

    $ which ssh
    /usr/bin/ssh
    $ ssh git.mojain.com
    Last login: Mon Oct 29 20:17:47 2007 from ...
    $ ^D
    
    $ /opt/local/bin/ssh git.mojain.com
    percent_expand: NULL replacement

You can follow the google results above for the details, but
essentially it seems that two things cause the git problem:

1. Leopard changed some environment variables that caused the MacPorts
version of git to get a NULL when it tried to determine what
"identity" to use.

2. git looks for ssh in the same directory as the git binary, causing
it to find the MacPorts version before the "native" OS X version.

There are a number of ways to work around this problem (all found in
the aforemetioned google results):

1. Set `GIT_SSH` to the OS X version (`/usr/bin/ssh`). This works for
git only of course.

1. Rename your ssh key files so MacPorts ssh can find them (I didn't
try this).

1. Tell ssh which key file to use by adding the following line to
`$HOME/.ssh/config` (creating that file if it doesn't exist):

    `IdentityFile ~/.ssh/id_dsa`

This last option is the one I chose, as it has the advantage of
working for all versions and invocations of ssh, and is probably a
good idea anyway. Presumably the MacPorts ssh package will be fixed at
some point, but this is working for me now.

[1]: http://twitter.com/mrowe
[2]: http://www.google.com/search?q=leopard+percent_expand%3A+NULL+replacement
[3]: http://www.macports.org/
