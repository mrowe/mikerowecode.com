---
layout: post
title: Captions in Aperture
date: "2006-12-13"
url: "/2006/12/captions_in_aperture.html"
---

Here is a [small AppleScript][4] that provides a marginally friendlier
interface for entering captions in [Aperture][1] than the built in
method.

It presents a small dialogue box for each selected photo and allows
you to enter a caption. Pressing RETURN or clicking "Ok" saves the
caption and takes you to the next selected photo. Pressing ESC or
clicking "Skip" leaves the current caption as it is, and moves to the
next photo. You can click "Cancel" to stop the script.

I use it by adding a trigger to [QuickSilver][5] that binds a hot key
(I picked Control-Apple-C, which remarkably seems to be unused by
Aperture--it already binds most conceivable key combinations, and even
a few no-one has thought of yet) to the script, which can be saved
anywhere you like. QS can limit the scope of the binding to a single
app, which helps prevent conflicts in other applications.

I'm no AppleScript expert, so I'd be happy to receive any
[feedback](mailto:mrowe@mojain.com) on better techniques. It doesn't
seem like it's possible in script, but it would also be cool to be
able to display a thumbnail in the dialog--just having the name is not
necessarily all that helpful.

The source of the script is available in my [subversion repository][4].

[1]: http://www.apple.com/aperture/
[2]: http://gallery.therowes.id.au/
[3]: http://photo.mojain.com/
[4]: http://svn.mojain.com/svn/pub/projects/mac_hacks/ApertureCaptions/
[5]: http://quicksilver.blacktree.com/
