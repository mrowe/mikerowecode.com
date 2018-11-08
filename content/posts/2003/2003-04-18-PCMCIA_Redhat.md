---
layout: post
title: PCMCIA and your custom RedHat 9 kernel
date: "2003-04-18"
url: "/2003/04/PCMCIA_Redhat.html"
---

I recently wanted to compile a custom kernel for my RedHat 9 laptop.
(For some reason, RedHat decided to omit NTFS support from their stock
kernel, even though it includes just about every other module under
the sun.) Much to my consternation, on reboot my PCMCIA cards no
longer worked. :( The only change I made to the default kernel config
was to add NTFS support, so I was rather perplexed.

Booting back to the stock kernel (ya gotta love grub!) revived my
PCMCIA CardBus, but left me NTFS-less. (Please keep the "that's a
feature, not a bug" comments to yourself...) The release of a security
patch for the 2.4.20 kernel just increased my desire to get a custom
kernel going.

Further investigation determined that the problem was a missing socket
driver (`yenta_socket`) module. When the `ds` module tried to load, it
failed because it couldn't find the socket driver. Manually loading
`yenta_socket` then `ds` got everything working properly. But it
didn't work at boot time, only if I manually loaded the yenta module.

I still can't figure out _why_ this is the case, since my
`modules.conf` and `/etc/sysconfig/pcmcia` files are the same with
either kernel. But for what it's worth, adding this line:

<pre>
    pre-install ds modprobe yenta_socket
</pre>

to modules.conf solved the problem.

If anyone can shed any light on this, I'd be glad to hear it.
