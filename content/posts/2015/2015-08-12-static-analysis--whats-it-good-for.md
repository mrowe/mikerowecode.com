---
layout: post
title: Static Analysis&mdash;what's it good for?
date: "2015-08-12"
slug: "static-analysis-whats-it-good-for"
url: "/2015/08/static-analysis--whats-it-good-for.html"
---

Let's face it, writing software is hard. And frankly we humans suck at
it. We need all the help we can get. Our industry has developed many
tools and techniques over the years to provide "safety rails", from
the invention of the [macro assembler][assembler] through to
sophisticated integration and automated testing frameworks. But
somewhere along the way the idea of static analysis went out of
favour.

[assembler]: http://en.wikipedia.org/wiki/Assembly_language

I'm here to convince you that static analysis tools still have a place in modern software engineering.

Note that I am avoiding the word "metrics" here. That is how a lot of
people think of these tools, and the whole idea of "measuring" a
developer's work rightly has a terrible reputation ("what does that
number even mean?"). Static analysis is simply about providing
*information* that a human can use to learn more about a code base.

## What is static analysis?

Simply, it's a tool that analyses your code "at rest". That is, it
inspects the source code (or in some cases object code) _statically_,
not in a running environment. (Dynamic analysis of running systems,
such as with memory profilers like [yourkit][] and [valgrind][], is a
whole other topic.)

[yourkit]: http://www.yourkit.com
[valgrind]: http://valgrind.org

## Why is it so unpopular?

One of our developers recently made the following comment in an
internal chat channel:

> _After seeing some refactoring that people have done to satisfy
> static code quality analysis tools, I question their value._

This is a common response to the use of such tools, and perfectly
reasonable. But it misses the point. Of course static analysis can be
misused, but that doesn't mean it _has_ to be.

Another common complaint is "but these tools can't replace the eye of
an experienced developer!". No, they can't. But they can help focus
that experienced eye where it is most needed.

## So what is it good for?

 * Early warning of problems

    By scanning the daily report for issues in recently introduced
    code, tech leads and senior developers can talk with the
    developers involved. Together they can work out better approaches
    before the problematic code becomes ossified in the code base and
    inevitably replicated by copy/paste.

 * Identifying "hot spots" in the code that warrant further attention

 * Overall sense of the "health" of a codebase

 * See trends over time

    Time series graphs can give you a good overview of how your code
    base is growing and changing. Steadily increasing LoC in a mature
    system might be a sign that it's time to factor out a submodule.
    Or maybe increasing complexity indicates too much pressure to rush
    out features without enough consideration for design.

The static analysis tool itself is not going to tell you any of these
things, but it might suggest places to look for potential trouble.


## What is it NOT good for?

 * Gating check-ins/failing builds

    This usually just leads to "gaming the system" or poor refactoring
    to "get around" the rules, which helps no one. Static analyses are
    information that needs to be intrepeted by people, not an
    automatic way to prevent bad code being committed.

 * Measuring developers' performance

    Hopefully I don't need to explain why this is a terrible idea. The
    output of these tools is the start of a conversation, and should
    certainly never be used against people or teams.

## How should I use my analysis tools?

 * Daily report to tech lead - new issues

    Tech leads can review a daily report as a starting point for
    conversations with developers. E.g. "I see you commited this
    method with a lot of nested `if`s.. have you considered doing it
    this way instead?"

 * High level graphs over time - complexity, etc.

    Dashboard of health. Are we getting worse? Should we be putting
    more effort into refactoring and clean up?

 * Predicting "cost of change"

    If a code base has a high complexity--relative to others in your
    org, or its own past state--the cost of change is likely to be
    higher. This can be useful information when estimating/predicting
    future effort.

 * Enforcing style guides

    This is a bit more controversial, and not really the kind of
    analysis I am talking about... but there is an argument to be made
    for using tools like [checkstyle][] and [rubocop][] to enforce your local
    style conventions. If nothing else, it makes arguments about brace
    position, white space, etc. moot.

[checkstyle]: http://checkstyle.sourceforge.net
[rubocop]: https://github.com/bbatsov/rubocop

## Quality is a people problem

> "It is impossible to do a true control test in software development,
> but I feel the success that we have had with code analysis has been
> clear enough that I will say plainly it is irresponsible to not use
> it." -- John Carmack, [In-Depth: Static Code Analysis][carmack]

[carmack]: http://www.gamasutra.com/view/news/128836/InDepth_Static_Code_Analysis.php

No tool is going to be a silver bullet. Software quality is and always has been primarily a "people problem". Tools can help, but they cannot automatically fix all your problems and enforce all your "rules". They simply provide information that can help people focus on the areas most needing attention, and highlight potential problems that might otherwised have been missed.

Static analysis tools (aka "quality metrics") can be a useful way to gain more insight into your code and identify areas that need more attention.

(This article was originally published on the [REA Tech Blog][].)

[REA Tech Blog]: http://techblog.realestate.com.au/static-analysis-whats-it-good-for/
