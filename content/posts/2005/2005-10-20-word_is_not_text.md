---
layout: post
title: Word is not text
date: "2005-10-20"
url: "/2005/10/word_is_not_text.html"
---

Note to self: next time you have occasion to commit a Word document
to CVS (_dog_ forbid), make sure you tell it that it's a __binary__
file, not text.

I foolishly let [Eclipse][] decide for me... and it picked
text. Sigh. So a week after I had left the project, I get a call from
my ex-manager complaining that certain documents could not be
opened. Since one of the last things I did before leaving the job was
to wipe my PC's hard drive, I was a little concerned.

But a quick inspection of the "corrupt" Word document confirmed my
suspicion. Every newline (0x0a) character was indeed preceed by a
carriage return (0x0d). DOS line breaks! Thanks for that one, Bill!

Anyway, [this] [1] small piece of Perl hackery later, and the document
opened fine (in [OpenOffice][] anyway).

[Eclipse]: http://www.eclipse.org/
[OpenOffice]: http://www.openoffice.org/
[1]: http://svn.mojain.com/svn/pub/scripts/dos2unix.pl
