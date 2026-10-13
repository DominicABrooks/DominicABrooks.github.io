---
title: "Getting the Most Value From Your E2E Tests"
description: "Building a test strategy where every layer does what it does best"
published: 2026-02-16
updated: 2026-02-16
tags: [Testing Strategy, Playwright, Engineering Leadership]
category: "Engineering"
draft: false
---

E2E tests are one of the most compelling tools in testing.

- They're readable.
- They're demonstrable.
- They simulate real user behavior.
- You see the cursor move.
- You see the button click.
- You see the success toast.

They give everyone confidence.

QA teams love them. Management loves them. The dashboard looks impressive.

That's exactly why it's worth being intentional about what you ask them to do. When an E2E suite is focused on the right things, it's fast, reliable, and gives the whole team confidence to ship.

---

## Testing Each Thing at the Right Level

The biggest win I've seen comes from validating *business logic* as early as possible, and saving E2E tests for what only they can verify.

Think about this scenario:

An endpoint starts returning `"100"` instead of `100`.  
A simple type change.

When a fast unit or integration test catches it, the fix happens in the pull request, in seconds, before anyone else is involved.

When it's only caught by an E2E test, the same bug travels a much longer path:

1. Breaks an E2E test.
2. Fails in a long CI run.
3. Requires QA review.
4. Generates a defect ticket.
5. Gets triaged.
6. Gets reprioritized.
7. Gets fixed.
8. Gets rerun through the full pipeline.

Catching it early saves everyone that time.

---

## Building on a Strong Foundation: The Test Pyramid

A strategy that scales well looks like this:

### Base Layer: Unit Tests
- Validate business logic
- Catch edge cases
- Run in milliseconds
- Execute on every commit

### Middle Layer: Integration & Contract Tests
- Validate service boundaries
- Catch schema/type regressions
- Ensure systems agree on expectations

### Top Layer: End-to-End Tests
- Validate critical user journeys
- Protect against major production failures
- Increase confidence across the whole system

With a strong foundation underneath, E2E tests can do what they do best: **increase confidence** that the product works for real users.

---

## What E2E Tests Do Best

A focused E2E suite answers the questions that matter most:

- Can a user sign up?
- Can they pay?
- Can they complete the core revenue flow?
- Can they perform the primary value-generating actions?

E2E tests protect the business.

Unit and integration tests protect the engineers.

When each layer handles the category of bugs it's best at catching, your most valuable testing layer stays fast, stable, and focused.

---

## How to Move Faster

If you're aiming for:

- Faster time to market  
- Lower CI costs  
- Quicker triage  
- A happier, more productive QA team  
- Stronger developer ownership  

Then start building quality in at every layer:

- Add unit and integration test gates to pull requests.
- Add contract validation at service boundaries.
- Ask during RCA: *Where's the earliest place we could catch this?*
- Make logic coverage part of the PR before QA ever sees it.

Every bug caught lower in the pyramid is dramatically cheaper.

E2E suites still play an important role. Sometimes the business needs broad regression coverage at the UI level, and when it does, good design makes all the difference. I wrote about the patterns I use in [Design Patterns for 100% Critical Coverage](/posts/automating-repetitive-apps/).
