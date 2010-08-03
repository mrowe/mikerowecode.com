Spaces becomes usable in OS X 10.5.3

[Spaces][] was one of the most anticipated features in Leopard, at
least for Unix/X11 refugees like myself. X has had virtual desktops
for decades, but users of "mainstream" desktop operating systems (i.e.
Windows and Mac OS X) have had to rely on third-party utilities to get
the same functionality.

In the case of OS X, Leopard was set to change that with Spaces.
Unfortunately, the [implementation was broken][1] in such a way as to
make it incredibly frustrating to use the way I'm used to using X11. I
typically have Terminal and Safari (and often [Emacs][2]) windows open
on multiple desktops. But on a desktop dedicated to a particular task,
I want to be able to ⌘-⇥ (command-tab) between application windows on
that desktop. Prior to 10.5.3, this would invariably do precisely the
opposite of what I wanted, and flip to _another_ desktop that had a
window of that application open. This resulted in Spaces being about
5% as useful as X11 for serious keyboard-oriented work.

_(For what it's worth, this whole thing is mostly an issue because of
the distinction OS X makes between apps and windows of apps--in X11,
alt-tab usually cycles between all windows equally, regardless of what
application they belong to. On OS X however, command-tab cycles
between applications--⌘-` can be used to cycle between
windows of an application.)_

But good news! The recent 10.5.3 update to Mac OS X fixes it! Contrary
to what [Gruber says][3]:

> [Y]ou shouldn’t notice any changes, because the default behavior
> remains the same in 10.5.3

the default behaviour _has_ changed: command-tabbing between
applications now stays on the same desktop if the target application
has a window there, and jumps to another desktop otherwise.

This is just about perfect. I actually like the jump-to-desktop
behaviour for applications that aren't on multiple desktops (e.g.
iTunes), but now the default is to stay on-desktop for apps that are.
(I still think I'd be slighly more comfortable if OS X behaved the
same way as X11, and treated all windows as equal--but that could be
Just What I'm Used To.)

Thanks Apple!

[Spaces]: http://www.apple.com/macosx/features/spaces.html
[1]: http://blogs.sun.com/bblfish/entry/why_apple_spaces_is_broken
[2]: http://aquamacs.org/
[3]: http://daringfireball.net/2008/05/spaces
