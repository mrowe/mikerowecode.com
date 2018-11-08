---
layout: post
title: Setting iTunes metadata for downloaded TV shows
date: "2007-09-05"
url: "/2007/09/setting_itunes_metadata.html"
---

Below is a small piece of thoroughly untested AppleScript (well, it
worked for me at least once) that will process files you might have
downloaded from a torrent and attempt to parse out show, season and
episode information from the file name.<!--break-->Below is a small
piece of thoroughly untested AppleScript (well, it worked for me at
least once) that will process files you might have downloaded from a
torrent and attempt to parse out show, season and episode information
from the file name. This relies on files being named in a regular way,
but that's been the case for everything I've downloaded recently.

Not that you'd illegally download torrents of course. Not if the major
content producers weren't such jackasses anyway.

My workflow for this is something like: 

1. find and download shows with [Xtorrent][1]

1. convert to iTunes with [Movie2iTunes][2] (I'd love to know if there is a better way)

1. go to the "Recently Added" smart playlist in iTunes and select all the newly-downloaded shows

1. run this script

1. grab your FrontRow remote and enjoy!

Opps, I left out "profit"! Feel free to [send me stuff][3].

[1]: http://www.xtorrentp2p.com/
[2]: http://dettmer.maclab.org/movie2itunes.html
[3]: http://www.amazon.com/gp/registry/1YCQAYFMSBW35

Here's the script:

    set AppleScript's text item delimiters to "."
    
    tell application "iTunes"
        repeat with sel in selection
            
            -- try work out show, season and episode from file name (e.g. Californication.S01E04.HDTV.XViD-Caph.avi)
            
            set tokens to text items of (name of sel as text)
            
            set myShow to item 1 of tokens
            set myEpisodeId to item 2 of tokens
            set mySeason to text 2 through 3 of myEpisodeId as number
            set myEpisode to text 5 through 6 of myEpisodeId as number
            --display dialog myShow & " season " & mySeason & " episode " & myEpisode
            
            tell sel
                set video kind to TV show
                set album to myShow
                set year to "2007"
                set genre to "Drama"
                set show to myShow
                set season number to mySeason
                set episode number to myEpisode
                set episode ID to myEpisodeId
            end tell
            
        end repeat
    end tell
