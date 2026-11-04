---
title: "Most Software Engineers Are Becoming SDETs"
description: "AI made writing code faster, so more of an engineer's job is now showing that the code works"
published: 2026-02-28
updated: 2026-02-28
tags: [Testing Strategy, Software Engineering, AI]
category: "Engineering"
draft: false
---

I’ve been thinking about a point from “Software engineering is dead now” by Theo:

<iframe width="100%" height="468" src="https://www.youtube.com/embed/p2aea9dytpE?start=1393" title="YouTube video player" frameborder="0" allowfullscreen></iframe>

His point is that the bottleneck has moved. With modern tooling and AI, you can scaffold features, fix bugs and refactor code much faster than before, so writing the code is no longer the slowest part of shipping software. Everything around it still takes about as long as it did. Someone still has to write clear user stories, scope the work, decide what “done” means, test it, run regression, plan the release and watch production. If writing code gets cheap, those become the constraint.

So more engineering time goes to validation: better test coverage, more reliable CI, fewer flaky tests, tighter release gates and better observability. None of that is new, but it’s becoming the center of the job. A team that pairs fast feature work with reliable regression testing gets to keep its speed, and the more often it ships, the more that testing pays off.

That looks a lot like traditional SDET work, done by every engineer as part of the job. Whoever ships a feature is also responsible for showing that it works, keeps working, and doesn’t break anything next to it.

Theo also points out that building features and trying to break them are different mindsets, and both are valuable. That’s why I think most engineering roles are slowly shifting toward what used to be SDET work, and I think it makes the job more interesting.
