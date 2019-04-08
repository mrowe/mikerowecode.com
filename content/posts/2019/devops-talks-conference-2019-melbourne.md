---
title: "DevOps Talks Conference 2019 Melbourne"
date: 2019-04-08T09:51:56+10:00
---

I recently started in a new role in a large financial services
organisation, with a mission to establish a [Site Reliability
Engineering](https://landing.google.com/sre/) practice. Due to some
fortunate timing, one of the first I did was attend the [DevOps Talks
Conference](https://devopstalks.com/2019_Melbourne/), a two-day
conference that "brings together leaders, engineers and architects who
are implementing DevOps in start ups and in enterprise companies".
While this conference was obviously primarily about the principles and
practices of DevOps, there is a good deal of overlap with the ideas of
Site Reliability Engineering.

Below are a few notable highlights from the two days, as seen through
the lens of someone who is trying to implement SRE in a traditional IT
organisation.


## Jennifer Petoff - Google SRE Program Manager

Jennifer described SRE at Google and its key motivators and
principles, then talked about what to focus on to implement SRE in
_your_ organisation. Key messages:

 * Start with SLOs **and consequences** (aka error budgets)
 * Push back on the dev teams when necessary to drive improvements in reliability


## Matty Stratton - PagerDuty

**A good post mortem raises more questions than it answers**. With
today's complex distributed systems, there is unlikely to be a
singular "root cause" for any given incident. A post mortem/PIR should
tell a story, and lead you to learn more about your systems and how
they interact.


## Nathan Harvey - Google

Some metrics to think about when trying to measure application
stability:

 * Mean Time to Recovery (MTTR)
 * **Change failure rate**
 
Change failure rate is the percentage of "changes" (i.e.
releases/deployments) that "fail"—lead to production incidents or have
to be rolled back. The idea is that by working to keep this low you
can be more confident in your releases and move faster.

> "You have to be safe to move fast... and you have to move fast to be safe."

Meaning you can move faster when you're confident in your testing,
etc., but by the same token being able to quickly move a change
through your pipeline to production is "safer"—it makes it far easier
to roll out fixes.


## John Willis

Organisational change is doomed to fail if you impose it from above.
The people who have to implement and are most affected by the change
need to be involved in designing the New Way.

There is a yawning chasm between legacy IT management (CABs, work
queues, service tickets, etc.) and agile/cloud/devops teams. Breaking
down tribal knowledge and creating institutional knowledge is critical
to succeeding with a devops/SRE culture.

**You _can_ get rid of your CAB!** — Change management processes
implement "subjective attestation", but we can achieve the same ends
(confidence in the integrity of our production systems) by using
"objective attestation": automated pipelines, cryptographically
authenticated control of changes, etc. See also "DevSecOps" and Mark
Angrish's [talk on
governance](https://kccna18.sched.com/event/GrSw/automating-enterprise-governance-using-the-cicd-pipeline-satyam-agarwala-thoughtworks-mark-angrish-anz)
at Kubecon last year.


## Mark Angrish - ANZ

Ground swell of support in the technology community is important, but
change (to devops, etc.) starts with senior leadership, including the
CEO.

**Funding models are also critical.** Project-based funding where
teams are disbanded when they are "finished" makes it really hard to
adopt a true devops/SRE culture.


## Lindsay Holmwood - Envato

This was a really interesting talk, and contained basically no
technical content. Lindsay talked about organisational design—how to
understand it, and how it relates to technology innovation and
architecture—value chain mapping, and complexity theory. A few
take-aways:

 * There's lots of existing research about "organisation culture"
(sociology, anthropology). We should pay attention to it.
 * DevOps is great at introspection, but we need to look outside our
own domain. There's lots to learn from other disciplines. 
 * **You need to rearchitect your org structure to adopt new technology.**
Your existing org structure mirrors your product/tech architecture. If
you want to innovate/adopt new technologies and architectures, you
need to change your organisation to enable this. (See also: [Conway's
Law](https://en.wikipedia.org/wiki/Conway%27s_law).)

And if you want to learn more, some interesting pointers for further
reading/research:

 * *Edgar Schein* — [Organizational Culture and Leadership](https://www.amazon.com/Organizational-Culture-Leadership-Edgar-Schein/dp/0470190604)
 * *Henderson & Clark* — [Architectural Innovation](http://www-management.wharton.upenn.edu/pennings/documents/Hensderson_and_Clark_ASQ_1990.pdf) 
 * *Simon Wardley* — [value chain mapping](https://hiredthought.com/2018/09/01/intro-to-wardley-mapping/, https://github.com/andrewharmellaw/wardley-maps-book/releases/)
 * *The Great Courses* — [Understanding Complexity](https://www.thegreatcourses.com.au/courses/understanding-complexity.html)
 * *Douglas Hubbard* — [How to Measure Anything](https://www.amazon.com.au/How-Measure-Anything-Intangibles-Business-ebook/dp/B00INUYS2U)

Phew! 

# Summary

Overall it was a worthwhile couple of days, and gave me a few things
to think about and leads to follow... and a bit of reassurance that
SRE is possible in a large "enterprise" organisation. People have done
this before!

The organisers promised to post videos of the talks in the coming
days. Check back at https://devopstalks.com/2019_Melbourne/index.html.

 
