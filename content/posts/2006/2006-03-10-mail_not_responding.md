---
layout: post
title: Apple Mail.app "not responding"
date: "2006-03-10"
url: "/2006/03/mail_not_responding.html"
---

This morning, my mail server was rebooted by my 
[ISP](http://rimuhosting.com/). This is not extraordinary in itself,
but somehow it caused my mail client (Apple's Mail.app) some
confusion. It thought the IMAP server was offline (which it was for
half an hour or so), and would not reconnect.

When I restarted Mail, it froze. "Application not responding". Force
quit worked to kill it, but it always froze on restart (even after a
reboot). Other IMAP clients (I also use the excellent [Mutt][] in
conjunction with [offlineimap][], and when really desperate,
[Thunderbird][]) were working just fine.

I found [this][1] article on [macosxhints][], but its suggestion:

    % cd ~/Library/Mail
    % find . -name '.index.ready' -delete
    % find . -name 'content_index' -delete
    % find . -name 'table_of_contents' -delete

did not work. (Well, it obviously worked, the files were deleted, but
it didn't help the symptoms.)

I noticed another indexy cachey looking file in my mailbox
directories: `.mboxCache.plist`. Deleting all of these:

    find . -name '.mboxCache.plist' -delete

seemed to fix Mail.app for me.

**NOTE** I have no idea what this file is for, or what the
implications of deleting it might be. It appears to have Worked For
Me(tm), but it could also cause all your email to fall out of the back
of your computer and make a nasty stain on your carpet.

[Mutt]: http://www.mutt.org/
[offlineimap]: http://quux.org/devel/offlineimap
[Thunderbird]: http://www.mozilla.org/products/thunderbird/
[1]: http://www.macosxhints.com/article.php?story=20040729192248562
[macosxhints]: http://www.macosxhints.com/
