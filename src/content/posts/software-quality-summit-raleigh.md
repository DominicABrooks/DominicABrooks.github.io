---
title: "Software Quality Summit Raleigh"
description: "Accessibility testing in CI, AI evals, and spec-driven development at TestingMind's Software Quality Summit"
published: 2026-10-12
updated: 2026-10-12
tags: [Conference, Accessibility, AI, Playwright, QA Engineering]
category: 'Quality Assurance'
draft: false
---

On September 30th, I attended TestingMind's [Software Quality Summit](https://www.testingmind.com/event/software-quality-summit-raleigh/) in Cary, right here in the Research Triangle. Two of my coworkers and I went together, thanks to Aspida for sending us. It was a great day, and it was nice to meet so many people working on quality engineering in the area.

## AI Was the Main Theme

Most of the day was about AI, from testing AI-powered products to using AI in the testing process itself. The panel on AI agents in testing (co-pilot, colleague, or replacement?) was a fun discussion.

The topic I found most interesting was **evals**. When a feature is powered by an LLM, a traditional pass/fail assertion doesn't fit very well, because the same input can produce many different valid outputs. Evals give you a structured way to measure whether those outputs are good enough, and they feel like a natural next step for QA engineers. Building eval datasets, defining scoring criteria, and tracking quality over time are all things testers are already good at.

## Accessibility Testing in CI

My favorite talk was Vladimir Volkov's session on accessibility testing and WCAG compliance automation.

The setup was:

- **Storybook** for testing individual components in isolation
- **Playwright with axe** for scanning full pages and user flows
- Both running in **CI**, so accessibility issues show up in the pull request

This reminded me a lot of the accessibility pipeline we built on a previous project. Theirs was more mature, and it gave me a lot of ideas for how to take that approach further. Testing at the component level is especially appealing. If your button, modal, and form components are accessible in Storybook, every page built from them starts from a strong foundation.

The part that impressed me most was his walkthrough of the **WCAG conformance levels**:

- **Level A**: The baseline requirements, like text alternatives for images and full keyboard access.
- **Level AA**: The level most organizations target, and the one most often referenced in regulations and legal standards. It adds things like color contrast requirements and consistent navigation.
- **Level AAA**: The highest level. It's great to aim for where possible, but it isn't usually expected across an entire site.

He also briefly covered where legal liability comes in when products aren't accessible. That's a really useful way to frame accessibility for stakeholders. It's the right thing to do for users, and it's also a real business risk to manage, which makes it much easier to prioritize.

Automated tools like axe won't catch everything (things like logical reading order and meaningful alt text still need a human), but running them in CI catches a large share of common issues early and makes accessibility a normal part of the development process.

## Agent Orchestration and Spec-Driven Development

The other talk I really enjoyed was by Andrew Knight, also known as [Automation Panda](https://automationpanda.com/). I've been reading his blog for years, and I had no idea he was based in the Triangle, so it was great to see him speak in person.

His talk was about agent orchestration and the future of spec-driven development in AI product workflows. The idea is that as AI agents take on more of the implementation work, the specification becomes the most important artifact. Clear, well-structured specs drive the agents, and they also define what "correct" looks like for testing.

I thought it was a really interesting thought experiment. It connects to something I wrote about earlier this year in [Most Software Engineers Are Becoming SDETs](/posts/swe-to-sdet/): as writing code gets faster, defining and verifying behavior becomes the center of the work. Spec-driven development takes that idea one step further.

## Wrapping Up

I came home with a lot of new ideas, especially around maturing accessibility testing and getting more hands-on with evals. Thanks again to Aspida for supporting our professional development, and to TestingMind for putting on a great event. I'm looking forward to the next one.
