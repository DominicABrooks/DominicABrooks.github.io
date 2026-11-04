---
title: "Handling Hydration in Playwright Reliably"
description: "Retrying an action until the page responds, with expect().toPass(), instead of guessing when a server-rendered app is ready"
published: 2026-02-16
updated: 2026-02-16
tags: [Testing Strategy, Playwright, Engineering Efficiency]
category: "Engineering"
draft: false
---

Server-rendered apps send HTML that looks ready before the JavaScript has attached its event handlers. For a few hundred milliseconds, a button can be visible and enabled and still not respond when you click it. If a test clicks during that window, nothing happens, and the test fails for a reason that has nothing to do with the feature.

## What people usually try

The first fix most people reach for is a fixed wait:

```javascript
await page.waitForTimeout(2000);
await page.click('#submit');
```

It's easy to read, but it's a guess. If hydration takes 50ms, the test still waits the full two seconds, and if it ever takes longer than two seconds, the wait isn't enough.

Waiting for a `data-hydrated` flag works well when the app has one, but then the test depends on framework internals, and someone has to add and maintain the flag. Checking that the button is visible first doesn't add much either, because Playwright's actionability checks already wait for that before every click.

Clicking a few times in a row looks silly, but it's the closest of the lot, because it's what a person does when a button doesn't respond:

```javascript
await page.click('#submit');
await page.click('#submit');
await page.click('#submit');
```

It just needs a way to know when to stop.

## Retrying with toPass

`expect().toPass()` does exactly that. It retries a block of actions and assertions until the assertions pass:

```javascript
await expect(async () => {
  await page.getByRole('button', { name: 'Submit' }).click();
  await expect(page.getByText('Success')).toBeVisible({ timeout: 1000 });
}, { message: 'Submit the application' }).toPass({ timeout: 10000 });
```

If the click lands before hydration finishes, the block tries again, and as soon as the page responds, the test moves on. If hydration takes 20ms, the test waits 20ms. It also doesn't need to know which framework rendered the page or whether a hydration flag exists.

Give the inner assertion a short timeout (one second here) so each attempt fails fast, and give `toPass` its own timeout so the whole block has an upper limit. In the trace viewer, the retries show up grouped under the step name you pass in `message`, which makes failures easy to read.

## Only retry what's safe to repeat

This works best for actions you can safely do more than once, like clicking Next, opening a menu or switching tabs.

For anything with side effects, like submitting a payment or creating a record, use the same idea one step earlier. Retry something harmless on the page until it responds, which proves the page is hydrated, and then do the important action once.
