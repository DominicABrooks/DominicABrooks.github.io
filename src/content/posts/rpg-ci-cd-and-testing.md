---
title: "How I Test My Browser RPG"
description: "Playwright on GitHub runners with no GPU, and a monkey that plays every area of the game overnight"
published: 2026-11-03
updated: 2026-11-03
tags: [CI/CD, GitHub Actions, Playwright, Vitest, Game Development, Testing Strategy]
category: "Engineering"
draft: false
---

In October I gave myself 72 hours to make an RPG that runs in the browser. It's called [Simplicity](https://dominicab.itch.io/simplicity), it's written in TypeScript and three.js, and I've kept working on it since the jam ended.

It has cutscenes and boss fights and a whole city of people going about their day, which is a lot of stuff to break by accident. So every push runs 1,160 unit tests and 51 Playwright tests on GitHub Actions, and nothing goes up to itch.io unless they all pass. Most of that is the usual lint, type check, test and build. The parts that took some figuring out were running WebGL on machines with no GPU, and a "monkey" that plays every area looking for bugs I'd never have thought to test for.

## Playwright without a GPU

The e2e tests play the real game in Chromium. One spec walks from the woods to the throne room and back. Another sits through the King's whole speech. GitHub's runners don't have a GPU, and Chrome won't fall back to software WebGL on its own anymore, so the config asks for SwiftShader:

```typescript
launchOptions: {
  args: ["--use-angle=swiftshader", "--enable-unsafe-swiftshader"],
},
```

That works, but scenes that run at 60 fps on my machine run at a few frames a second on a runner.

## Waiting on the game's clock

I added the e2e job on the second night of the jam, and its first run failed. The very first spec walks the hero each way for half a second (`waitForTimeout(500)`) and checks that he gets more than 5 units from where he started. On the runner he made it 4.897.

The game moves forward at most 50 ms per frame, so a long frame doesn't teleport everything. That's fine at 60 fps. At six frames a second, which is what one full-screen spell effect gets in CI, a real second is only 0.3 seconds of game time.

So the helpers wait on the game's own clock instead:

```typescript
async function play(page: Page, seconds: number) {
  const from = await page.evaluate(() => window.game.clock);
  await page.waitForFunction(
    ([from, s]) => window.game.clock >= from + s,
    [from, seconds] as const,
  );
}

async function hold(page: Page, key: string, seconds: number) {
  await page.keyboard.down(key);
  await play(page, seconds);
  await page.keyboard.up(key);
}
```

Now half a second of `hold` is half a second of walking, at 60 fps or at 6.

The second run failed too. One spec clicks on the King to talk to him, and on the runner the click landed while the camera was still moving, so it missed. Now it waits for the camera and clicks again if nothing happened, which is pretty much the trick from my [hydration post](/posts/power-of-retry/). The third run passed.

## Starting in the middle

Playing the whole story to test the last boss would take forever at a few frames a second. So the e2e build has a debug handle, and URL parameters load the game straight into a scene:

```typescript
await startAt(page, "area=capital&hour=19"); // the city at 7pm
await startAt(page, "boss=calder&god");      // a boss fight, can't die
```

The URL only sets the scene up. Whatever the spec is testing goes through the keyboard and mouse, like a player.

## The monkey

The unit tests check what I think the game should do, and the e2e tests check the paths I thought to write. For everything else there's a soak test, which is basically a monkey. It gets dropped into every area from every entrance, with every weapon (fishing rod included), by day and by night. It wanders around mashing attack, talks to people, knocks on doors, pets the dogs, and walks through whatever exit it bumps into. It runs the real game loop, just without drawing anything.

All it knows is a few things that should never happen. After every frame it checks that:

- nothing's position is `NaN`
- the hero's health is in range
- the hero isn't stuck inside anything solid
- entities aren't piling up

Every push plays each area once, and a nightly workflow plays each one 50 times.

A day after I added it, the monkey found a real bug. At first it looked like a flaky test, failing about one run in three in the same boss fight (the PR that fixed it still has "soak flake" in its title). That boss has an attack that pulls you toward a crack in the ground, and the pull ignored collision. If you were backed up against a tree at the edge of the arena, you got dragged into it a little more every frame. None of my tests ever stood the hero against a tree during that fight, but the monkey did, about a third of the time. Now the pull goes through `moveBy`, the same function walking uses, so it stops at whatever's in the way.

It also couldn't be replayed at first, because the test rolled a new random seed every run and didn't print it. Now every failure prints its seed, and you can pin it:

```bash
SOAK_GAME_SEED=14 npx vitest run tests/soak.test.ts
```

Seed 14 (the Rift, with a bow, at 7am) is its own test case now.

None of this tells me whether a fight is any fun, though. I still have to play it for that.
