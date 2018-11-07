---
layout: post
title: Reviewboard git mirror
date: "2008-01-17"
url: "/2008/01/reviewboard_with_djblets.html"
---

For some months now, I've been maintaining a [git mirror][1] of the [Reviewboard][2] project's [svn repository][3]. The [git-svn][4] tool works really well for this, except for one small wrinkle: the reviewboard projects uses svn:external to include an external module, djblets, and git-svn provides no transparent way to support this.

For now, I manage this manually. When ever I notice an update to djblets (which are thankfully rather rare), I use the following process to merge the changes into a branch ([with-djblets][5]) in the git repo:

    $ cd ~/src/djblets
    $ git svn rebase
    $ git log -1 | grep -v '^commit' > /tmp/djblets.log

Note: change "1" to whatever number of commits have happened in djblets since the last time I did this. The grep command removes the git-specific "commit" lines from the log, which won't be interesting enough to include in the commit message below.

    $ cd ~/src/reviewboard-with-djblets
    $ git status # make sure working dir is clean
    $ cp -rp ~/src/djblets/* reviewboards/djblets/

At this point, I do a `git status` and manual sanity check to make sure the changes I'm about to commit here match the incoming change to djblets.

    $ git add <files that are changed/new>
    $ git commit -F /tmp/djblets.log
    $ git push public-repo with-djblets

Done! Simple, no? Well, no... This process has a number of problems, the main one of which is it's manual, and *I* have to do it. I'm hoping that I'll be able to bend [git-submodule][6] to my will enough to take care of this.

[1]: http://git.mojain.com/w/mirrors/reviewboard.git
[2]: http://code.google.com/p/reviewboard/
[3]: http://reviewboard.googlecode.com/svn/
[4]: http://www.kernel.org/pub/software/scm/git/docs/git-svn.html
[5]: http://git.mojain.com/w/mirrors/reviewboard.git?a=shortlog;h=with-djblets
[6]: http://www.kernel.org/pub/software/scm/git/docs/git-submodule.html
