---
title: "Getting the Most Value From Your E2E Tests"
description: "Moving business logic checks down to unit and integration tests, so the E2E suite can focus on what only it can check"
published: 2026-02-16
updated: 2026-02-16
tags: [Testing Strategy, Playwright, Engineering Leadership]
category: "Engineering"
draft: false
---

E2E tests are the most satisfying tests to watch. You see the cursor move, the button click and the success toast pop up, and anyone from QA to management can follow along. That's exactly why it's worth being choosy about what you ask them to do.

## Catch logic bugs early

The biggest win I've seen comes from checking business logic as early as possible, and saving E2E tests for what only they can verify.

Say an endpoint starts returning `"100"` instead of `100`. If a unit or integration test catches it, the fix happens in the pull request, in seconds, before anyone else is involved.

If only an E2E test catches it, the same bug takes a much longer trip. It fails a long CI run, someone in QA reviews the failure and files a defect, the defect gets triaged and prioritized, a developer fixes it, and the whole pipeline runs again. That's a lot of people and hours for a type change.

## What's left for E2E

With unit tests covering logic and edge cases, and integration and contract tests making sure services agree on their data, the E2E suite can focus on the journeys that matter most. Can a user sign up? Can they pay? Can they get through the core flows the business depends on? A suite that sticks to those questions stays fast and stable.

## A few habits that help

- Add unit and integration test gates to pull requests.
- Add contract checks at service boundaries.
- Make logic coverage part of the PR before QA ever sees it.
- In every root cause analysis, ask where the earliest place to catch the bug would have been.

Sometimes the business does need broad regression coverage at the UI level, and then good design makes all the difference. I wrote about the patterns I use for that in [Design Patterns for 100% Critical Coverage](/posts/automating-repetitive-apps/).
