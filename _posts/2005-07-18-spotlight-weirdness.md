---
layout: post
title: Spotlight Weirdness
---

I've had the uneasy feeling for some time that [spotlight][] was not
quite the full deal. I'd search for something, and not really be
satisfied with the results.

Anyway, today, I needed to find a phone number that I'd mentioned in
an [Adium][] chat last week. Something to do with Anthony, and it had
two 8s in it. But spotlight denied any knowledge of such a file. :-/
In the end, I browsed my Adium logs manually and found it, but it
annoyed me enough that spotlight couldn't even perform this simple
task, that I started looking into it.

The file (that I found manually) was definitely being indexed, since
`mdls` returned all its metadata. But neither the GUI spotlight nor
`mdfind` found it. A bit of googling around turned up no better
suggestion than "blow it away". That is, apparently the spotlight
index can become corrupt, and forcing it to rebuild is the only way to
fix it.

This is accomplished with the command:

    sudo mdutil -E /

(which erases the index for the startup volume).

Of course, OS X will then go and rebuild the index, which in my case
took less than a couple of hours.

And now I can find that Adium transcript!

[spotlight]: http://www.apple.com/macosx/features/spotlight/
[Adium]: http://www.adiumx.com/
