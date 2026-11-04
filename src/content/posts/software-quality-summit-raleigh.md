---
title: "Software Quality Summit Raleigh"
description: "Accessibility testing in CI, AI evals, and spec-driven development at TestingMind's Software Quality Summit"
published: 2026-10-12
updated: 2026-10-12
tags: [Conference, Accessibility, AI, Playwright, QA Engineering]
category: 'Quality Assurance'
draft: false
---

On September 30th, two of my coworkers and I went to TestingMind's [Software Quality Summit](https://www.testingmind.com/event/software-quality-summit-raleigh/) in Cary, right here in the Research Triangle. Thanks to Aspida for sending us. It was nice to meet so many people working on quality engineering so close to home.

## AI was the main theme

Most of the day was about AI, from testing AI-powered products to using AI in testing (one panel asked whether AI agents are a tester's co-pilot, colleague or replacement).

The topic I found most interesting was evals. When an LLM powers a feature, a pass/fail assertion doesn't fit well, because the same input can produce lots of valid outputs. Evals measure whether those outputs are good enough, and they feel like a natural next step for QA engineers. Building datasets, defining scoring criteria and tracking quality over time are things testers already do well.

## Accessibility testing in CI

My favorite talk was Vladimir Volkov's on automating accessibility testing and WCAG compliance. His setup used Storybook to test components in isolation and Playwright with axe to scan full pages and user flows, with both running in CI so accessibility issues show up in the pull request.

It reminded me a lot of the accessibility pipeline we built on a previous project. His was more mature, and I came away with plenty of ideas for taking ours further. I especially like testing at the component level. If your buttons, modals and forms pass in Storybook, every page built from them starts with accessible pieces.

He also covered the WCAG conformance levels (AA is the one most regulations point to) and where legal liability comes in, which is a useful way to pitch accessibility to stakeholders. It's the right thing for users, and it's also a business risk, so it's much easier to prioritize.

Axe can't check everything, like reading order or whether alt text makes sense, but in CI it catches the common issues on every pull request.

## Spec-driven development

The other talk I really enjoyed was by Andrew Knight, also known as [Automation Panda](https://automationpanda.com/). I've been reading his blog for years and had no idea he was based in the Triangle, so it was great to see him speak in person.

He talked about agent orchestration and spec-driven development. As AI agents take on more of the implementation, the spec becomes the most important artifact. It drives the agents, and it also defines what "correct" looks like for testing.

I wrote something similar earlier this year in [Most Software Engineers Are Becoming SDETs](/posts/swe-to-sdet/): as writing code gets faster, defining and checking behavior becomes the center of the work. Spec-driven development takes that idea a step further.

I came home with a lot of ideas, mostly about our accessibility testing and getting hands-on with evals. Thanks to TestingMind for putting it on.
