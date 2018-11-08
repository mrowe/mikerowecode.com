---
layout: post
title: Got emacs working on tiger
date: "2005-05-01"
url: "/2005/05/tiger_emacs_working.html"
---

What seemed to make the difference was adding "--without-x" to the
configure:

    ./configure --with-carbon-app --without-x
    make bootstrap
    sudo make install

(I arrived at this by noticing in the compile error message:

    macterm.h:590: error: conflicting types for 'display_x_get_resource'

and on a hunch figured, well, I don't _need_ X so lets try without
it. I haven't looked in to what I lose by disabling it. \*shrug\* )

Oddly, the make install didn't. Or rather, it installed the non-carbon
version in /usr/local, but did not install the carbon app in
/Applications. Hopefully I will find out why, because I'm sure it did
the last time I built emacs on Panther (a week or two ago).

Anyway, I have aqua/carbon emacs working on tiger, and I'm a happy
little camper.

Oh, by the way, for those who came in late... the current CVS emacs
has native aqua/carbon support, which is how I'm building this. To get
it, do this:

    cvs -z3 -d:ext:anoncvs@savannah.gnu.org:/cvsroot/emacs co emacs

then the aforementioned configure/make. One of these days I'll get
around to uploading packages.
