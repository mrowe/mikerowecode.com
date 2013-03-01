---
layout: post
title: "Do you have anything to declare, sir?"
---

One of the cornerstones of modern software engineering is dependancy management systems. Think [Bundler][], [Leiningen][], or (forgive me) Maven. We stand on the shoulders of giants when we write our apps, and we need a way of specifying which giants. Modern systems like RubyGems are pretty good at this. But not perfect.

[Bundler]: http://gembundler.com
[Leiningen]: http://leiningen.org

I have a dream
--

I have a simple dream. All I want is this: I want to be able to checkout your project on any computer, install the appropriate language runtime and dependency manager and type `make` (or `rake`, or `lein`, or `./build.sh`, or ...) and have a running system.

None of this is new. Joel [said it][1], Twelve Factor App [says it][2]. But surprisingly few people seem to actually do it.

[1]: http://www.joelonsoftware.com/articles/fog0000000043.html
[2]: http://www.12factor.net/dependencies


Undeclared dependencies are the root of all evil
--

It's very easy as a developer to introduce dependencies into your project without even realising. Our workstations get all sorts of cruft installed on them over time, and the chances are *something* lying around fulfills an undeclared transitive dependency for the library you just installed. But the next developer may not be so lucky.

I don't want to have to find out by trial and error what native libraries your code depends on. I don't want to belatedly discover some assumptions you made about what else would be running on my computer. I just want to type `make`.





So what's the point?
--

Just this: 

__Your project's build system has the responsibilty to install any library that is required to support your code__.

Hopefully most of these will be taken care of by your dependency management system. But for those that aren't (e.g. native libraries that are required by Ruby Gems) your build system needs to make sure they are installed.
