---
layout: post
title: Adding captions in Aperture
date: "2006-12-13"
url: "/2006/12/adding_aperture_captions.html"
---

[1]: http://www.apple.com/aperture/
[2]: http://gallery.therowes.id.au/
[3]: http://photo.mojain.com/
[4]: http://svn.mojain.com/svn/pub/projects/mac_hacks/ApertureCaptions/
[5]: http://quicksilver.blacktree.com/
[6]: http://www.mojain.com/node/46

I've just started using Apple's [Aperture][1] to manage my photos
([family][2] and [other][3]), and I'm pretty pleased with it so far.
One thing, however, that is harder than it needs to be is entering
captions. As far as I can tell, the only way to do this in Aperture is
to click on the photo in the browser, mouse over to the Information
panel and click in the caption field (making sure you've selected IPTC
fields to be displayed), type the caption and mouse back and click in
the browser window so it has focus (otherwise any further keys you
press will still be typing in the caption field). Then click on the
next photo. Eeew.

This is bearable for one or two photos, but when you have a largish
batch to do, it is very tedious. It's also prone to error, such as
when you start typing the caption without the field having focus, and
Aperture gleefully trying to act on all your keypresses.

So I [threw together a small AppleScript][6] that provides a
marginally friendlier interface. This is one of my first attempts at
AppleScript, so I'd be happy to receive any feedback on better
techniques.

