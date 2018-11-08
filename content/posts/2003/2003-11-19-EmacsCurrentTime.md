---
layout: post
title: Insert current time in an emacs buffer
date: "2003-11-19"
url: "/2003/11/EmacsCurrentTime.html"
---

A lot of text editors (way back to the days of Borland's
SideKick--wow, what a blast!) provided an easy way to insert the
current date and time in the file you are editing. This is
particularly useful for a notes/journal type of thing.

[Emacs][1] (the One True Text Editor) does not provide an "out of the
box" way to do this, but a very simple piece of elisp can do it.

I put this (adapted from a [suggestion][2] found on the help-gnu-emacs
list) in my .emacs:

    (defun mr-insert-current-time ()
      (interactive)
      (insert (format-time-string "%F %T")))
    (global-set-key (kbd "C-c d") 'mr-insert-current-time)
    
    (defun mr-insert-current-time-block ()
      (interactive)
      (insert "n-------------------n")
      (insert (format-time-string "%F %T"))
      (insert "n-------------------n")
      )
    (global-set-key (kbd "C-c C-d") 'mr-insert-current-time-block)

which allows me to press `C-c d` at any time to get the current date and
time in my buffer (formatted the way I like). The second function puts
it in a little block, good for seperating entries in a notes log.

[1]: http://www.gnu.org/software/emacs/emacs.html
[2]: http://mail.gnu.org/archive/html/help-gnu-emacs/2002-10/msg00092.html
