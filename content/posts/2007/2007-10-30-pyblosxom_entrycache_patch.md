---
layout: post
title: Patch for pyblosxom entrycache plugin to make cache location configurable
date: "2007-10-30"
url: "/2007/10/pyblosxom_entrycache_patch.html"
---

The [entrycache][1] plugin for [pyblosxom][2] is really cool. I only
wish I could configure the location of the file it uses to store its
cached dates.

[1]: http://joe.terrarum.net/projects.html
[2]: http://pyblosxom.sourceforge.net/

So here's patch:

    --- a/entrycache.py
    +++ b/entrycache.py
    @@ -21,24 +21,31 @@ __url__ = "http://joe.terrarum.net"
     
     import os.path
     
    +def _get_cache_filename(args):
    +   request = args["request"]
    +   config = request.getConfiguration()
    +        if config.has_key('entrycache_cachefile'):
    +                return config['entrycache_cachefile']
    +        else:
    +                return os.path.join(config['datadir'],'.entrycache')
    +
     def cb_start(args):
        t = { }
        request = args["request"]
    -   config = request.getConfiguration()
        data = request.getData()
    -   if os.path.isfile(os.path.join(config['datadir'],'.entrycache')):
    -       data['cachefile'] = os.path.join(config['datadir'],'.entrycache')
    -       f = file(os.path.join(config['datadir'],'.entrycache'))
    +   if os.path.isfile(_get_cache_filename(args)):
    +       data['cachefile'] = _get_cache_filename(args)
    +       f = file(_get_cache_filename(args))
            t = eval(f.read())
            f.close()
             data['cache'] = t
        request.addData(data)
     
        if not data.has_key('cachefile'):
    -       f = file(os.path.join(config['datadir'],'.entrycache'),'w')
    +       f = file(_get_cache_filename(args),'w')
            f.write("{ }")
            f.close()
    -       data['cachefile'] = os.path.join(config['datadir'],'.entrycache')
    +       data['cachefile'] = _get_cache_filename(args)
            request.addData(data)
     
     def cb_filestat(args):

Then add a line like this to your pyblosxom config:

    py["entrycache_cachefile"] = \
        "/Users/mrowe/Sites/blog/data/.entrycache"
