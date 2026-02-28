---
title: "Stop Writing So Many E2E Tests (The Hard Way)"
description: "Lets be honest, you have way too many"
published: 2026-02-16
updated: 2026-02-16
tags: [Testing Strategy, Playwright, Engineering Leadership]
category: "Engineering"
draft: false
---

You've got 2,000 end-to-end tests.  
You've got an expensive, parallelized CI fleet.  
And yet… you're still afraid to push to production.

Why?

Because your testing strategy is missing the point.

---

## The Illusion of Confidence

E2E tests *feel* like the best approach.

- They're readable.
- They're demonstrable.
- They simulate real user behavior.
- You see the cursor move.
- You see the button click.
- You see the success toast.

It *feels* definitive.

QA teams love them. Management loves them. The dashboard looks impressive.

But then the suite keeps growing.

You add more parallelization.  
You add more containers.  
You hire more QA engineers to maintain the suite.  

And defect rates are still high.

Stories reach QA completely broken.  
Hotfixes require an overnight 3-hour run across hundreds of containers.  
Everyone holds their breath before pressing deploy.

Something feels wrong.

Because something **is** wrong.

---

## You're Testing the Wrong Things at the Wrong Level

E2E tests are being used to validate *business logic* that should never have made it that far.

Yes — technically you *can* test everything through QA-level end-to-end tests.

It’s just wildly expensive.

Think about this scenario:

An endpoint starts returning `"100"` instead of `100`.  
A type change. Nothing catastrophic.

Instead of being caught in a pull request by a fast unit or integration test, it:

1. Breaks an E2E test.
2. Fails in a 3-hour CI run.
3. Requires QA review.
4. Generates a defect ticket.
5. Gets triaged.
6. Gets reprioritized.
7. Gets fixed.
8. Gets rerun through the same expensive pipeline.

All of this over something that could’ve been caught in seconds in a staging PR.

That’s not just slow.  
That’s organizational drag.

---

## Engineering Is a Pyramid, Not a Monolith

Your test strategy should look like this:

### Base Layer — Unit Tests
- Validate business logic
- Catch edge cases
- Run in milliseconds
- Execute on every commit

### Middle Layer — Integration & Contract Tests
- Validate service boundaries
- Catch schema/type regressions
- Ensure systems agree on expectations

### Top Layer — End-to-End Tests
- Validate critical user journeys
- Ensure no catastrophic production failures
- Increase confidence — not prove correctness

E2E tests are supposed to **increase confidence**, not serve as your primary regression mechanism.

If you're testing all business rules through Playwright-level flows, you're building a skyscraper with no foundation.

---

## What E2E Tests Are Actually For

You don't need 2,000.

You need enough to answer:

- Can a user sign up?
- Can they pay?
- Can they complete the core revenue flow?
- Can they perform the primary value-generating actions?

E2E tests protect the business.

Unit and integration tests protect the engineers.

If you're relying on QA to validate business logic through UI automation, you're using your most expensive testing layer to solve the cheapest category of bugs.

---

## If You Want to Move Faster

If you're serious about:

- Faster time to market  
- Lower CI costs  
- Fewer triage loops  
- Reduced QA burnout  
- Higher developer ownership  

Then stop relying on outdated testing habits.

Start:

- Enforcing unit and integration test gates.
- Adding contract validation at service boundaries.
- Asking during RCA: *Could this have been caught lower?*
- Holding PRs accountable for logic coverage before QA ever sees it.

Every bug caught lower in the pyramid is exponentially cheaper.

Every bug caught at E2E is organizational friction.

---

## Final Thought

End-to-end tests are powerful.

But when they become your primary safety net, you're not building confidence.

You're building fear.

Engineering requires a pyramid