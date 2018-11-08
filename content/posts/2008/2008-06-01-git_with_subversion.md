---
layout: post
title: Using git on subversion projects
date: "2008-06-01"
url: "/2008/06/git_with_subversion.html"
---

Despite all the noise lately about distributed version control
systems, the chances are any given project you want to work on today
will be using Subversion. But that's OK, you can still get the benefit
of all the advanced features of [git][] by using it as a "front end" to
subversion. 

Before I get into the "how", why would you want to do this?

The most obvious benefits are having a full local history, and cheap
local branching. It's trivial in git to create branches for features
you're working on, and then easily switch between them. Say you're
working on a feature for the next release, and an urgent bug for 1.0
comes in. Simply:

    $ git commit -m "work in progress"
    $ git checkout --track -b fix-urgent-bug-1234 release-1.0

    ...hack hack...

    $ git commit -m "fixed bug #1234"
    $ git checkout cool-feature-foo

and continue where you left off.

There's also a bunch of other neat stuff in git that I miss whenever I
have to use something else (keep in mind that I'm no svn guru, so
there may be similar things in svn if you look hard enough. But I very
much doubt they're as fast). `git grep` for rapidly searching source
trees. `gitk` for visualising branches and interactively searching for
commit messages and changes. Local commits. Oh, and everything is
*much* faster.

OK, on to the __how__.

We start by checking out the svn repo:

    $ git svn clone -s http://svn.example.com/svn/cool-project

The `-s` switch means "standard layout", i.e. the recommended
subversion usage of trunk/branches/tags. If your project doesn't
follow this convention, you can specify the names of the
subdirectories used:

    $ git svn clone --trunk=MAIN --branches=branches --tags=releases \
        http://svn.example.com/svn/cool-project

There are lots of other options to clone that can help if you have a
really non-standard repo to work with. Check the `init` command in
[man git-svn(1)][2].

You should now have the HEAD of trunk in a directory called
"cool-protect". (You can specify a different target directory name by
appending it to the `git svn clone` command.)

The full power of git is now at your command! You can grep the source
tree:

    $ git grep '^class Model('
    django/db/models/base.py:class Model(object):
    tests/modeltests/invalid_models/models.py:class Model(models.Model):

Find the git commit corresponding to a subversion revision:

    $ git svn find-rev r1234
    c5dfec042453672a27fd19ff81131edd01145584

    $ git show c5dfec0
    commit c5dfec042453672a27fd19ff81131edd01145584
    Author: Michael Rowe <mrowe@mojain.com>
    Date:   Sat Feb 16 10:14:57 2008 +1100
    ...

And interrogate the full history of the repo:

    $ cd ~/src/django
    $ git log '@{3 weeks ago}' -1
    commit 696a3322d6709ebffcc436eb6188ea4d769ebfc5
    Author: mtredinnick <mtredinnick@bcc190cf-cafb-0310-a4f2-bffc1f526a37>
    Date:   Mon Feb 4 04:57:56 2008 +0000
    
        Fixed a simple TODO item in one error path of the "extends" tag.

In the time we've been playing with this, maybe some changes have been
committed upstream. To make sure our local repository is up to date, we
rebase:

    $ git svn rebase

You could also just use `git svn fetch` to fetch the upstream changes
into the repo without rebasing your working tree. In general, I would
avoid this unless you know what you are doing, since it can make
things complicated when you go to merge and push your changes
upstream. If you are working on the main trunk of the svn repo,
`rebase` is almost always what you want.

So now we have an up to date checkout, lets get to work! As you work,
add files to git's "index" and commit to the repo. Commits in git are
fast, and should be used almost as frequently as saving a file in your
editor. You can always consolidate these "micro-commits" into larger
feature or bug fix commits later.

    ...hack hack...

    $ git add src/module.py src/other.py
    $ git commit -m "I did stuff"

and repeat.

*Note for subversion users*: you have to tell git about every file you
change, even if it's not a new file. Details of git usage are beyond
the scope of this article (there are some [excellent starting points][5]),
but be aware that you have to `git add` each file you
want included in a commit.

(*Note for lazy git users*: This can be combined into a single
command for existing files:

    $ git commit -m "I did stuff" src/module.py src/other.py

but I tend to prefer the two-step approach for anything but the most
trivial changes.)

As you work, you can periodically sync with the upstream subversion
repo to get other people's changes:

    $ git svn rebase

This won't work if you have any local uncommitted changes. However,
you can "stash" them away temporarily (in git 1.5.3 and later):

    $ git stash
    $ git svn rebase
    $ git stash apply

In any case, as mentioned above, you want to commit locally as often
as possible.

When you have finished work on a feature or bug fix that you want to
push back to the subversion repository, make sure all your changes are
committed locally to git (`git status`), then review what you've done:

    $ git log origin/trunk (by default, or whatever svn branch you're on)
    $ git diff origin/trunk

Finally, when you are happy with the work you've done and are ready to
push it up to subversion:

    $ git svn dcommit

This will create individual svn check ins for each git commit since the
last upstream revision. If you want to combine local commits into one
large svn check in (e.g. because you followed my advice above and made
frequent local commits), the interactive rebase command will help:

    $ git rebase --interactive origin/trunk

Interactive rebase opens an editor with a list of all the commits
since the revision you specify (`remotes/trunk` in our example).

    pick d79a908 A small change to a file
    pick c5dfec0 An unrelated change
    pick db0346b Fix typo in hello

To combine the typo fix into the first commit, move its line directly
below the line for the first commit and change "pick" to "squash":

    pick d79a908 A small change to a file
    squash db0346b Fix typo in hello
    pick c5dfec0 An unrelated change

The result will be two commits (`d79a908` and `c5dfec0`), with
`d79a908` incorporating the changes from `db0346b`. You can do this
for multiple consecutive lines if you want to combine many commits
into one. See [man git-rebase(1)][3] for full details.

Now use `git svn dcommit` as above to push the revised commits
upstream.

We've been working on a single branch so far, but one of the big
benefits of using git is the cheap branching. Lets start work on a new
experimental feature:

    $ git checkout -b my-wacky-feature

The `-b` switch means create a new branch. Without that, `git
checkout` switches to an existing branch. 

    ...hack hack...

    $ git add ...
    $ git commit ...

At any time, we can commit locally and switch to another branch:

    $ git checkout other-thing-to-work-on

    ...hack hack...

    $ git add ...
    $ git commit ...

then switch back and continue where we were:

    $ git checkout my-wacky-feature

All of the commands we've discussed operate on the current branch
(unless you specify otherwise). So you can grep for strings, get
change logs and diffs and view visual history all in the context of
the branch. You can also diff the current branch with another. To get
a diff from `release-1.0` to the current working tree (on branch
`fix-urgent-bug-1234`):

    $ git checkout fix-urgent-bug-1234
    $ git diff release-1.0

Or to get diffs between arbitrary branches and revisions (without
having to checkout either branch):

    $ git diff release-1.0..my-wacky-feature 

See [man git-diff(1)][4] for all the options to diff.

`git svn dcommit` will only push changes on the current branch up to
the subversion repository, so you can clean up and consolidate your
commits using rebase, then push them back to subversion when they're
ready.

----

I hope this quick introduction has whet your appetite for combining
the power of git with the ubiquity of subversion. There is much more
to git (we haven't touched on merging at all), and once you've dipped
your feet in, I recommend reading the intros and man pages at the [git site][].

Please [let me know][1] if you have any suggestions or notice any
errors.

[1]: mailto:mikerowe@mikerowecode.com
[2]: http://www.kernel.org/pub/software/scm/git/docs/git-svn.html
[3]: http://www.kernel.org/pub/software/scm/git/docs/git-rebase.html
[4]: http://www.kernel.org/pub/software/scm/git/docs/git-diff.html
[5]: http://git.or.cz/gitwiki/QuickStart
[git]: http://git.or.cz/
[git site]: http://git.or.cz/gitwiki/GitDocumentation
