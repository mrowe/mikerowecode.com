Using VMWare Fusion shared folders with a Linux guest

[VMWare Fusion][] has a "shared folders" feature which allows you to
seamlessly share folders on the host Mac system with the virtualised
guest OS. With a Linux guest, `vmware-tools` will install the
"Host-Guest File System" (`hgfs`) driver and add an entry to
`/etc/fstab` to automagically mount all shared folders under
`/mnt/hgfs`. 

This is great, but unless your user id in the Linux guest happens to
match your user id OS X, you will not be able to access the mounted
directories as a regular user. Luckily, you can get the hgfs driver to
mount the shared folders as your user. Edit `/etc/fstab` as root:

    $ sudo vi /etc/fstab

and look for a section like:

    # Beginning of the block added by the VMware software
    .host:/ /mnt/hgfs vmhgfs defaults,ttl=5 0 0
    # End of the block added by the VMware software

Add options for `uid` and `gid`:

    # Beginning of the block added by the VMware software
    .host:/ /mnt/hgfs vmhgfs defaults,ttl=5,uid=1000,gid=1000 0 0
    # End of the block added by the VMware software

The values I've used, 1000 for uid and gid, are the defaults for the
first user created on an Ubuntu desktop install. To find the correct
values for your user, run the `id` command in the guest OS:

    $ id
    uid=1000(mrowe) gid=1000(mrowe) groups=...

[VMWare Fusion]: http://www.vmware.com/products/fusion/
