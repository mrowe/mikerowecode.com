---
layout: post
title: Self-signed certificates in Mail.app
---

I run my own mail server, including an IMAP server. I use SSL, but I
was not about to pay Verisign (or anyone else) for a signed
certificate. I had been putting up with the "trust this certificate?"
messages every time I started the Apple Mail.app, until I came across
this [Apple][] [support
article](http://docs.info.apple.com/article.html?artnum=25593).

It describes how to import your self-signed certificate into your key
chain, so Mail doesn't bother you any more.

[Apple]: http://www.apple.com/
