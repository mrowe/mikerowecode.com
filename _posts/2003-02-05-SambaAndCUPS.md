Printing from windows with samba and cups

The default debian (woody) config of cups didn't quite allow this to
work... make the following changes (to enable raw printing via the
windows driver):

Uncomment line 83 in `/etc/cups/mime.convs`:

    #application/octet-stream        application/vnd.cups-raw        0       -

and also uncomment line 152 in `/etc/cups/mime.types`:

    #application/octet-stream

Also, we need to add the following (rarely mentioned) line to smb.conf
(in the [Printers] section):

    use client driver = yes

This will cause windows to successfully get the printers' status.
