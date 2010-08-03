Linux on a Compaq EVO N600c

There are [several good resources][1] for getting Linux running on a
Compaq EVO laptop. A tip I haven't found elsewhere: unless you disable
glx (in `/etc/X11/XF86Config`), putting the machine in standby (with X
running) will cause the screen to make very weird patterns.

An unsolved problem: standby mode still consumes significant battery
power. Enough, in fact, to totally flatten the batteries after about
15 hours from a full charge. If anybody has seen this, and found a
solution, I'd love to know.

[1]: http://www.google.com/search?q=linux+compaq+evo+n600c
