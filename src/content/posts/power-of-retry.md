---
title: "Why expect.toPass() Is the civilized way to handle hydration issues"
description: "Hydration issues probably aren't getting fixed, so lets engineer around them"
published: 2026-02-16
updated: 2026-02-16
tags: [Testing Strategy, Playwright, Engineering Efficiency]
category: "Engineering"
draft: false
---

The page looks perfect.

High-resolution.  
The button is right there.  
Pixel-perfect UI.

You click it.

Nothing happens.

Not because it’s broken, but because the JavaScript hasn’t attached the event listeners yet. The app is *visible*, but it isn’t *awake*.

And somehow, this tiny hydration gap has caused more flakiness than entire backend systems.

---

## The Usual (Bad) Fixes

I keep seeing engineers solve this problem in the most expensive ways imaginable.

### The Timeout Gamble

```
javascript
// Don't do this please!!
await page.waitForTimeout(2000);
await page.click('#submit');
```

Two seconds of blind hope.

If hydration takes 50ms, you just wasted 1,950ms.  
If it takes 2,100ms, your test still fails.

---

### The Click Spam Strategy

```
javascript
await page.click('#submit');
await page.click('#submit');
await page.click('#submit');
```

This is kinda funny because it might work sometimes, but don't do this either! 

---

### The "Magic Boolean" Anti-Pattern

```
javascript
await page.waitForSelector('[data-hydrated="true"]');
await page.click('#submit');
```

Now your test depends on internal framework implementation details.
Quite an expensive way to fix hydration, even if it works

---

### The Visibility Trap

```
javascript
await expect(page.getByRole('button', { name: 'Submit' })).toBeVisible();
await page.click('#submit');
```

Visible ≠ interactive.  
Rendered ≠ hydrated.

Playwright is already checking this. Adding toBeVisible, or isVisible checks everywhere isn't helping.

---

## What Humans Actually Do

How does a human solve this?

If you're on an app and you click "Sign in" and nothing happens. What do you do.
Humans click the button again.

That’s it.

No timers.  
No DOM polling.  
No inspecting hidden attributes.

They perform the action until the side effect occurs.

That’s the system you should mimic.

---

## Engineering the Right Way

This is exactly what `expect().toPass()` is designed for.

Not blind retries.  
Not arbitrary sleep.  
Not hoping.

Structured, scoped retries around intent.

```
javascript
await expect(async () => {
  await page.getByRole('button', { name: 'Submit' }).click();
  await expect(page.getByText('Success')).toBeVisible();
}, { message: "Click the Submit button until it works!" }).toPass();
```

It also looks nice in the trace view.

If the click lands during the hydration “dead zone,” the block retries.
If the UI responds correctly, it moves on immediately.

No guesswork.  
No 2-second tax.  
No coupling to framework internals.

---

## Why This Is the Correct Abstraction

Hydration timing is a race condition.

You don’t eliminate race conditions by sleeping longer.  
You eliminate them by designing around eventual success.

### 1. Efficiency

The test runs as fast as the application allows.

20ms hydration?  
Test moves on in 20ms.

Not 2000ms.

---

### 2. Resilience

You acknowledge reality:  
Sometimes clicking the button will do {x}

Instead of predicting when sometimes will be, you just do the action until {x} occurs.
---

### 3. Decoupling

Your test no longer cares:

- Which framework is rendering.  
- What hydration flags exist.  
- How many microtasks are queued.  
- Whether React, Vue, or some custom system is bootstrapping.  

It only cares that:

> When a user clicks submit, they see {x}.

That’s the contract.

---

## The Bigger Problem

A lot of the other strategies do actually work.. most of the time. But the problem is most of the time isn't good enough for end to end test. That just means your result are flaky, which is arugably worse than passing. So much time is going to be wasted reviewing failures, and if you're not fixing these at the root it's going to end up costing so much to maintain these.

Imagine how many of your test are going to be retried because of these hydration issues, or if the container that is executing the test is slow and now all your test are showing false-negative results. It's a headache.

---

## The Verdict

If you're still using:

- `waitForTimeout`  
- Hydration flags  
- Click spamming  
- Manual polling loops  
- Any other weird strategies to handle hydration

You're building a brittle system around a predictable problem.

Let the framework retry intelligently, `expect().toPass()` is the correct abstraction.