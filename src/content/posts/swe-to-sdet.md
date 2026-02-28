---
title: "Most Software Engineers Are Becoming SDETs"
description: "When the value of code goes down, the cost of quality goes up."
published: 2026-02-28
updated: 2026-02-28
tags: [Testing Strategy, Software Engineering, AI]
category: "Engineering"
draft: false
---

I’ve been thinking about a point from “Software engineering is dead now” by Theo
<iframe width="100%" height="468" src="https://www.youtube.com/embed/p2aea9dytpE?start=1393" title="YouTube video player" frameborder="0" allowfullscreen></iframe>

The argument isn’t really that engineers are obsolete. It’s that the bottleneck has moved.

Writing feature code is no longer the hardest or slowest part of shipping software. With modern tooling and AI, you can scaffold features, fix bugs, and refactor code much faster than before. Implementation time has compressed.

But the time it takes to safely ship hasn’t compressed at the same rate.

Defining user stories clearly still takes effort. Scoping work still matters. Deciding what “done” actually means still matters. Testing still takes time. Regression still takes time. Planning a release still takes coordination. Monitoring production still requires intent.

If code creation becomes cheap, the constraint shifts to everything around it.

That changes what’s valuable.

A team that can generate features quickly but lacks reliable regression testing will eventually slow down. The cost shows up as bugs in production, unstable deploys, or fear around refactoring. The more frequently you ship, the more this risk compounds.

So more engineering time ends up being spent on validation.

That validation can look like better unit test coverage. It can look like integration and end-to-end suites. It can look like improving CI reliability, reducing flakiness, tightening release gates, or improving observability.

None of that is new. What’s different is how central it’s becoming.

As implementation speed increases, verification becomes the limiting factor. Engineers who used to focus primarily on feature work now spend more time thinking about test structure, test data management, mocking strategies, pipeline performance, and production signals.

In practice, that looks closer to traditional SDET work.

Not necessarily as a separate role, but as a mindset. The engineer isn’t just responsible for delivering a feature. They’re responsible for proving that the feature works, continues to work, and doesn’t break adjacent functionality.

But you can see in Theo's video, he mentions not all SWE are good at QA. These are seperate mindsets, and that's why I feel the role is slowly shaping towards most engineers having to change into what was before considered the work of a SDET.

That doesn’t make engineering less important, it just changes the work.