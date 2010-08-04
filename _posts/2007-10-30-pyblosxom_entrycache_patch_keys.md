---
layout: post
title: Another patch for pyblosxom entrycache - normalised keys
---

The entrycache plugin uses the absolute path of a file as the key for
caching its date. This is problematic if the file is moved (e.g. your
data dir is different locally to on your web server).

This patch normalises the key to remove the "datadir" component. It
also cleans up how the cache is written to disk:

    diff --git a/entrycache.py b/entrycache.py
    index 0cc3196..b46f89d 100644
    --- a/entrycache.py
    +++ b/entrycache.py
    @@ -52,19 +52,18 @@ def cb_filestat(args):
        request = args["request"]
        data = request.getData()
        cache = data["cache"]
    -   if cache.has_key(args['filename']):
    +   config = request.getConfiguration()
    +   key = args['filename'].replace(config['datadir'], '')
    +   if cache.has_key(key):
            mtime = []
            for i in args['mtime']:
                mtime.append(i)
    -       mtime[8] = cache[args['filename']]
    +       mtime[8] = cache[key]
            args['mtime'] = tuple(mtime)
        else:
    -       cache[args['filename']] = args['mtime'][8]
    +       cache[key] = args['mtime'][8]
            f = open(data['cachefile'],'w')
    -       f.write("{\n")
    -       f.write("\t'%s' : %i,\n" % (args['filename'], \
        args['mtime'][8]))
    -       for i in cache:
    -           f.write("\t'%s' : %i,\n" % (i, cache[i]))
    -       f.write("}")
    +       import pprint
    +       pprint.pprint(cache, f)
            f.close()
        return args

I'll get around to publishing my git repo of this soon.

