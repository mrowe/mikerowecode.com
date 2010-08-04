---
layout: post
title: Adium script to insert NetNewsWire URL
---

I quite often find myself reading feeds in [NetNewsWire][] and find
something I want to share with a "buddy" in an [Adium][] chat. Adium
has a neat little "safari" button that inserts a link to the web page
you are currently viewing in Safari, but nothing equivalent for
NetNewsWire. And to do it manually is a pain, involving multiple mouse
movements and clicks (including right clicks, which are cumbersome
with the powerbook) and key presses to cut and paste.

AppleScript to the rescue!

The following bit of AppleScript (my first actual functional
applescript!) gets the necessary values from NetNewsWire:

    using terms from application "NetNewsWire"
		tell application "NetNewsWire"
			set linkHTML to "<HTML><A HREF=\"" & (URL of selectedHeadline) \
				& "\">" & (title of selectedHeadline) & "</A></HTML>"
			return linkHTML
		end tell
	end using terms from

This code needs to be wrapped in the appropriate Adium hook:

    on substitute()
	    ....
    end substitute

Finally, the whole thing needs to be packaged up in a bundle that can
be installed in Adium. The source code is available at my [svn
repository][1], and the installable bundle can be downloaded from
[NetNewsWire.AdiumScripts.zip][2].

[Adium]: http://www.adiumx.com/
[NetNewsWire]: http://ranchero.com/netnewswire/
[1]: http://svn.mojain.com/svn/pub/projects/mac_hacks/AdiumNetNewsWire/
[2]: http://www.mojain.com/~mrowe/files/NetNewsWire.AdiumScripts.zip
