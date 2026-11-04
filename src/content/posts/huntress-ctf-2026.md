---
title: "My First CTF: Huntress CTF 2026"
description: "A month of daily challenges in Huntress's AI Arena, the agent I built for my team, and a perfect score"
published: 2026-11-04
updated: 2026-11-04
tags: [CTF, Security, AI, Reverse Engineering, Web Security]
category: "Security"
draft: false
---

Huntress CTF 2026 was my first CTF, and it ran for the whole month of October. I played on a team with some of my coworkers from Aspida, and we finished with a perfect score, which put us in the top 6%. We peaked at 6th place during week 2 and spent most of the month somewhere around 20th to 40th.

In my [TAE post](/posts/istqb-tae/) I mentioned I'd been spending my study time on CTF prep, mostly [pwn.college](https://pwn.college/) and PortSwigger's [Web Security Academy](https://portswigger.net/web-security). This was the first one I actually got to play.

## The AI Arena

Huntress runs a CTF every October for Cybersecurity Awareness Month. Past years leaned into malware and forensics, with a lot of challenges based on real incidents, but this year's was called the AI Arena and it was pretty different. There were six tracks (warmups, binary reverse engineering, script analysis, Android, metadata forensics and web), and new challenges unlocked every morning at 9, for 189 in total.

Every challenge had three flags. Solving it once with no clock was worth 2 points. The Time Trial gave you a fresh version on a timer for 3, and Mastery threw a whole batch of them at you for 5. Some of those batches were huge, the Final Boss in the binary RE track had over 6,500 answers. Flags were unique to every team, and AI use was strongly encouraged (Huntress even gave out AI tokens), so the whole thing was pretty much built for bots.

## Our agent

I built most of our team's agent, which took care of the Time Trial and Mastery flags so none of us had to race the clock by hand.

On the solving side I bounced around a lot. Web was what I'd practiced the most for on PortSwigger, but that track didn't open until day 19, and then it caught up with two or three challenges a day. The last one, Framework Roulette, was a Cloudflare Worker sitting in front of an ASP.NET Blazor app with two layers of access control to get through. I also worked on binary RE, script analysis, Android and metadata forensics. The script analysis track used a different scripting language every day, 31 in total.

## SHA1-Hulud

The challenge with the fewest solves was SHA1-Hulud on day 30. It's named after the Shai-Hulud worm that spread through npm last year, which also came up in one of the [All Things Open](/posts/all-things-open-2026/) talks I went to. It was a Rust crackme for Windows with obfuscated strings, code that unpacked itself behind an exception handler, fake timing checks and a check that hashed its own code.

Only 56 teams and solo players solved it, and we were one of them. Everyone from 57th to 96th finished exactly one SHA1-Hulud short of a perfect score.

## Tiebreakers

Points didn't drop as more people solved something, so the top of the board was a big tie. Ties went to whoever submitted their last flag first, and the last challenges unlocked at 9 AM on October 31st. The top 10 all had perfect scores and finished between 9:03 and 9:08 that morning, so first place came down to a few minutes.

Shoutout to my teammates for a really fun month. If you've been thinking about trying a CTF, I'd say go for it. Huntress runs one every October.
