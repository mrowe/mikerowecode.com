Solving weirdness in an svn updated django install

I am playing with the "trunk" version of [Django][1], a nifty
python-based web framework. This evening, I spent a few hours getting
back up to speed with the current state (trunk is changing fairly
rapidly at the moment, all for the good though). But I kept getting
TemplateSyntaxError in the admin module, with a message something
like:

[1]: http://www.djangoproject.com/

    django.templatetags.adminmedia does not define a variable named "register"

(Sorry that's not verbatim, I stupidly didn't copy the error before I fixed it.)

I double checked all my settings and removed references to any of my
local code, but kept getting the error. Finally, someone on the IRC
channel (#django at irc.freenode.net) suggested cleaning out the
django code to remove any *.pyc (compiled python) files.

Sure enough, doing this:

    cd ~/src/django.svn/trunk # or where ever you checked out django 
    find . -name "*pyc" -delete

fixed it.
