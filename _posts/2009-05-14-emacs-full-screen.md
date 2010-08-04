---
layout: post
title: Emacs full-screen shortcut
---

When you're writing or coding, you want to remove as many distractions
as possible. In addition to obvious things like shutting down you
email, IM and twitter clients, it can be helpful to put your editor in
full-screen mode. This way, the editor is the only thing visible, so
your attention isn't drawn to menu bars, flashing notifications or
bouncing dock icons.

To create a shortcut for fullscreen mode in emacs, put this in your
`~/.emacs` file:

    (defun toggle-fullscreen ()
      (interactive)
      (set-frame-parameter nil 'fullscreen (if (frame-parameter nil 'fullscreen)
                                               nil
                                               'fullboth)))
    (global-set-key [(meta return)] 'toggle-fullscreen)

Now pressing `M-return` (usually `alt + return` on Windows/Linux or `⌘
+ return` on a Mac) will toggle Emacs between normal and full screen
mode.

Thanks to Vebjorn Ljosa in [this thread][1] for this code snippet.

[1]: http://groups.google.com/group/carbon-emacs/browse_thread/thread/1945355952b13c5d
