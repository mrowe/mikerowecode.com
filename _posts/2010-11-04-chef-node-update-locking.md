---
layout: post
title: Chef doesn't lock node data when updating
---

We came across an exciting [Chef][chef] bug today.

Chef tracks metadata about nodes in its database. This includes
operational facts about the node (uptime, memory, etc.), and
chef-related things like when the node last checked in. It also
includes intentional data such as what run list should be applied to
the node. 

Periodically, a node polls its server for updates. What happens is:

 * node checks in with server

 * node gets current metadata from server, including its run list of
   recipes and roles

 * node performs actions as per the run list

 * node saves its metadata back to the server, including the run list
   it just applied

All well and good, except that step three can potentially be long
running. There's plenty of time for an administrator to change the
node's desired run list (or other intentional metadata) using the
`knife` tool or the web interface. But now, when the node's run
completes, it saves its *old* state back to the server, over-writing
whatever updates an administrator applied while it was running. And
you won't know unless you look.

This is unfortunate.

There's a [bug][1] that more or less describes this in the project's
tracker. It was raised quite recently, so hopefully someone from the
Chef team will take a look at it soon. There's also [a thread][2] on
the Chef mailing list. 

[Chef]: http://wiki.opscode.com/display/chef/Home
[1]: http://tickets.opscode.com/browse/CHEF-1812
[2]: http://lists.opscode.com/sympa/arc/chef/2010-11/msg00019.html
