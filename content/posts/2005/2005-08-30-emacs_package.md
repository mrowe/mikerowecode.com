---
layout: post
title: Aqua Emacs Installer
date: "2005-08-30"
url: "/2005/08/emacs_package.html"
---

I have been occasionally building Emacs packages for the Mac from
CVS. Anyone can do this, of course. Just get the latest source code:

    cvs -z3 -d:ext:anoncvs@savannah.gnu.org:/cvsroot/emacs co emacs

change into the "mac" directory:

    cd emacs/mac

and run make-package. You need to pass it some options that it gives
to autoconf:

    ./make-package --self-contained -C,--enable-carbon-app -C,--without-x

This will result in a normal Mac installer package,
EmacsInstaller.dmg, in the current directory. You can double-click on
it in Finder, or use the magic "open" command from the terminal:

    open EmacsInstaller.dmg

and install a lovely aquafied carbon emacs. 

I've found CVS HEAD for emacs to be pretty stable, but it is under
active development, and is _not_ an official release, so could be
broken on any given day. On the up side, what will become Emacs v22
has some pretty neat new features.

You can download my package, built from HEAD when I get around to it
from time to time, [here](http://www.mojain.com/~mrowe/files/EmacsInstaller.dmg).
