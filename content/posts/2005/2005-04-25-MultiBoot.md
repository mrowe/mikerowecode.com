---
layout: post
title: Booting many operating systems on one box
date: "2005-04-25"
url: "/2005/04/MultiBoot.html"
---

My main workstation PC has two physical IDE hard disks. Since one of
them is about 60G (and I don't have that many mp3s), I figured I may
as well install a few different operating systems on it.

At present, that consists of Debian r3.0 (woody), Debian unstable
(sid), FreeBSD 4.6, OpenBSD 3.2 and finally Debian GNU/Hurd. Phew.

### Partitions

Below is the layout of partitions on the disks.

    mira:~# fdisk -l /dev/hda /dev/hdb
    
    Disk /dev/hda: 255 heads, 63 sectors, 1028 cylinders
    Units = cylinders of 16065 * 512 bytes
    
       Device Boot    Start       End    Blocks   Id  System
    /dev/hda1             1         1      8001   83  Linux
    /dev/hda2           393      1028   5108670    5  Extended
    /dev/hda3             2       262   2096482+  a5  FreeBSD
    /dev/hda4           263       392   1044225   a6  OpenBSD
    /dev/hda5           393       963   4586526   83  Linux
    /dev/hda6           964      1028    522081   82  Linux swap
    
    Partition table entries are not in disk order
    
    Disk /dev/hdb: 255 heads, 63 sectors, 7476 cylinders
    Units = cylinders of 16065 * 512 bytes
    
       Device Boot    Start       End    Blocks   Id  System
    /dev/hdb1             1         4     32098+  83  Linux
    /dev/hdb2             5      7476  60018840    5  Extended
    /dev/hdb5             5       490   3903763+  83  Linux
    /dev/hdb6           491       612    979933+  83  Linux
    /dev/hdb7           613       734    979933+  83  Linux
    /dev/hdb8          2006      7476  43945776   83  Linux

### grub

This is the grub config I use to boot them all:

    title           Debian Woody GNU/Linux
    root            (hd1,4)
    kernel          /boot/vmlinuz-2.4.18 root=/dev/hdb5 ro
    savedefault
    
    title           Debian Sid GNU/Linux
    root            (hd0,4)
    kernel          /boot/vmlinuz-2.4.17 root=/dev/hda5 ro
    savedefault
    
    title           FreeBSD 4.6
    root            (hd0,a)
    kernel          /boot/loader
    savedefault
    
    title           OpenBSD 3.2
    root            (hd0,3)
    makeactive
    chainloader +1
    savedefault
    
    title           Debian GNU/Hurd
    root            (hd1,5)
    kernel          /boot/gnumach.gz root=hd1s6
    module          /boot/serverboot.gz
    savedefault

Although I wouldn't dream of doing it unless forced at knife-point (or
threatened with suspension of my beer allowance), you can also
convince grub to boot windows--against its better judgement.

### Installation

If I really had any clue, I would have made careful notes as this
sytem came together, so I could reproduce exactly what I
did. Sigh. Anyhow, it started off with just the smaller hard disk
(/dev/hda) containing a single install of Debian. When I got the
groovy new 60Ger and decided to go nuts, I installed Debian Woody on
that, and allocated a decent chunk to my /home filesystem. I then
proceeded to carve up the original disk for various BSDs and Hurds.

A reasonable amount of research in the web before I started convinced
me that it would all end in disaster, and my four year old debian sid
install that started life as potato would be toast... but what the eh,
it's all in the name of science. As it turned out, it was far less
traumatic than I thought--my biggest heart ache was discovering the
particular mysterious incantations that grub needed for each OS.

It's probably important that the *BSDs live in primary
partitions--this will somewhat limit your flexibility in having six
thousand OSes on a single disk. Linux is not so particular, and hurd
would probably happily live in that little mat of fluff that gathers
in the cooling vents of your PC.
