---
layout: post
title: Enabling git bash completion on OS X
date: "2009-06-01"
url: "/2009/06/git-bash-completion.html"
---

Bash completion, the magic that allows you to start typing the name of
a file, directory, etc. in bash then press TAB to complete it, can be
taught new tricks, including knowing about your git repository. But if
you're on a Mac, the magic is not installed by defaut.

If you are running git from [MacPorts][], you probably don't have the
bash_completion variant installed. You can install it with:

    sudo port install git-core +bash_completion

If you *do* already have git installed without this variant, you'll
probably need to deactivate it first:

    sudo port deactivate git-core

Then reinstall with the variants you need:

    sudo port install git-core +bash_completion +gitweb +svn +doc

You can then activate completion by adding the following to your
`~/.bash_profile`:

    if [ -f /opt/local/etc/bash_completion ]; then
        . /opt/local/etc/bash_completion
    fi

Thanks to [Denis Barushev][2] for this tip.

[MacPorts]: http://www.macports.org/
[2]: http://denis.tumblr.com/post/71390665/adding-bash-completion-for-git-on-mac-os-x-leopard
