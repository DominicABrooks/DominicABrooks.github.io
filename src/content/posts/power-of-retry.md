---
title: "Handling Hydration in Playwright Reliably"
description: "A simple, fast pattern for testing server-rendered apps with confidence"
published: 2026-02-16
updated: 2026-02-16
tags: [Testing Strategy, Playwright, Engineering Efficiency]
category: "Engineering"
draft: false
---

Handling hydration well is one of the biggest reliability wins you can get in a Playwright suite.

If you haven't run into it: server-rendered apps send HTML that *looks* ready before the JavaScript has attached event handlers. For a few hundred milliseconds, a button can be visible and enabled but not respond yet. A test that clicks during that window needs a way to handle it.

---

## Common Approaches

Teams usually reach for one of a few approaches first. Each one is a reasonable starting point, and each points toward a better solution.

### Fixed Waits

```javascript
await page.waitForTimeout(2000);
await page.click('#submit');
```

Simple and easy to read. The trade-off is that it's tied to a guess: if hydration takes 50ms, the test waits the full 2,000ms anyway, and if it ever takes longer than 2,000ms, the wait isn't enough.

---

### Clicking Multiple Times

```javascript
await page.click('#submit');
await page.click('#submit');
await page.click('#submit');
```

This is on the right track, because it mirrors what a user does. It just needs structure: a way to know when to stop, so the action only happens as many times as it needs to.

---

### Hydration Flags

```javascript
await page.waitForSelector('[data-hydrated="true"]');
await page.click('#submit');
```

This works well when the app exposes a flag. The trade-off is that the test now depends on framework internals, and the flag has to be added and maintained in the app.

---

### Visibility Checks

```javascript
await expect(page.getByRole('button', { name: 'Submit' })).toBeVisible();
await page.click('#submit');
```

A good instinct, and Playwright's actionability checks already do this for you automatically before every click. Hydration is about *interactivity*, so the most reliable signal is the result of the interaction itself.

---

## What Humans Do

How does a human handle this?

If you click "Sign in" and nothing happens, you click it again.

No timers.  
No DOM polling.  
No inspecting hidden attributes.

They perform the action until the expected result happens. That's the behavior worth mimicking.

---

## Retrying Around Intent

This is exactly what `expect().toPass()` is designed for: structured, scoped retries around an action and its expected outcome.

```javascript
await expect(async () => {
  await page.getByRole('button', { name: 'Submit' }).click();
  await expect(page.getByText('Success')).toBeVisible({ timeout: 1000 });
}, { message: 'Submit the application' }).toPass({ timeout: 10000 });
```

If the click lands before hydration finishes, the block retries.  
As soon as the UI responds, it moves on immediately.

It also reads nicely in the trace view, since the retries are grouped under one named step.

A couple of details make this work best:

- **Give the inner assertion a short timeout**, so each attempt retries quickly instead of waiting the full default expect timeout (5s).
- **Give `toPass` an explicit timeout**, so it has a clear upper bound.

### Best Fit: Repeatable Actions

This pattern is a perfect fit for actions that are safe to repeat, like clicking "Next," opening a menu, or switching tabs.

For actions with side effects, like submitting a payment or creating a record, use the same idea one step earlier: retry a harmless interaction on the page until it responds (proving it's hydrated), then perform the important action once.

---

## Why This Works

Hydration timing is a race condition, and the most reliable way to handle a race condition is to design around eventual success.

### 1. Efficiency

The test runs as fast as the application allows. If hydration takes 20ms, the test moves on in 20ms.

### 2. Resilience

Instead of predicting *when* the button will be ready, you perform the action until the expected outcome occurs.

### 3. Decoupling

Your test is free to focus on behavior, independent of:

- Which framework is rendering
- What hydration flags exist
- Whether React, Vue, or some custom system is bootstrapping

It only cares that:

> When a user clicks submit, they see the success message.

That's the contract.

---

## The Payoff

Consistent results are what make an end-to-end suite valuable. When every pass and every failure is meaningful, the team spends its time on real issues and trusts the pipeline completely.

Across a whole suite, especially on CI containers of varying speed, handling hydration this way adds up to a noticeably faster and more dependable test run.

---

## Summary

`expect().toPass()` lets you wait for the outcome instead of predicting when the app is ready. Use it for repeatable actions, give it sensible timeouts, and hydration becomes one less thing to think about.
