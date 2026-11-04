---
title: "All Things Open 2026"
description: "The XZ Utils backdoor, OpenSSF's Stranger Things tour and the other security and DevOps talks I went to at All Things Open in Raleigh"
published: 2026-11-04
updated: 2026-11-04
tags: [Conference, Security, DevOps, Supply Chain, Observability]
category: "Engineering"
draft: false
---

On October 20th, I went to [All Things Open](https://2026.allthingsopen.org/) at the Raleigh Convention Center. Aspida sent me for the Tuesday schedule, and since there are way more talks than one person can get to, I mostly stuck to the Security and DevOps tracks.

## The XZ Utils backdoor

The first talk I went to in the Security room was Kedar Kulkarni's [breakdown of the XZ Utils attack](https://2026.allthingsopen.org/sessions/two-years-to-a-backdoor-lessons-from-the-xz-utils-supply-chain-attack-an-attempt-to-compromise-openssh). If you don't remember it, back in 2024 someone spent over two years earning the trust of the XZ Utils maintainers so they could slip in a backdoor aimed at SSH. XZ is a compression library that ships with pretty much every Linux distro, so this could've been really bad. It only got caught because Andres Freund noticed his SSH logins were a little slower than they should be and decided to dig into it.

The part that got me was where the payload was hidden. It was inside binary test files, and it only turned on under specific build conditions. As a tester that's kinda wild, test fixtures are exactly the kind of file nobody looks at closely in a code review.

## Stranger Things, but for open source

Stacey Potter's [OpenSSF talk](https://2026.allthingsopen.org/sessions/security-things-how-openssfs-technical-initiatives-keep-you-safe-from-the-upside-down) was Stranger Things themed the whole way through. The Upside Down is the malicious package three layers deep in your dependency tree, the Open Source Security Foundation is Eleven and the gang, and most of the tools got a character too. Scorecard is Eleven's psychic compass for checking a package before you trust it, Sigstore gets "friends don't lie" for making code signing actually usable, and Alpha-Omega is Hopper, backing the critical projects that don't have enough people.

Scorecard and Sigstore both fit right into a CI pipeline, which is where I spend most of my time anyway.

## Defense with agents everywhere

Adam Kaplan's [Zero Slack Security](https://2026.allthingsopen.org/sessions/zero-slack-security-playing-defense-in-an-age-of-agents) started with chess. Defense used to be like playing black, you're half a step behind but the board is still fair. Now agents are baked into editors and CI/CD, and attacks like Shai-Hulud are worming through package ecosystems. Most of the talk was about what to do about it, like treating dev machines and pipelines as the front line, protecting yourself from bad packages on npm, PyPI and Maven, and using AI to find your own weak spots first. Plus zero trust and key handling, all the stuff that should've been done back in '23.

## Coding agents and observability

Over in the DevOps room, Jason Ostroski's [Move Fast, Break Nothing](https://2026.allthingsopen.org/sessions/move-fast-break-nothing-closing-the-loop-between-coding-agents-and-observability) started with a pretty simple problem. Coding agents can write and merge code faster than anyone can review it, and they have no idea what happens once it ships. His idea is to give them production context, so they check the blast radius before shipping and hand you an evidence-backed fix plan when something regresses, while you stay in control. It's a lot like what I wrote about in [Most Software Engineers Are Becoming SDETs](/posts/swe-to-sdet/), just with the agents doing some of the checking too.

Peter Zaitsev's [eBPF talk](https://2026.allthingsopen.org/sessions/ebpf-for-ai-powered-root-cause-analysis) right before it was about where that context can come from. eBPF gives you really deep visibility into a running system without much overhead, so it's a great fit for AI-powered root cause analysis.

## AI pull requests

Angie Jones's [talk](https://2026.allthingsopen.org/sessions/the-maintainers-dilemma-ban-ai-or-build-for-it) looked at AI from the maintainer's side. Projects are getting way more pull requests than they can keep up with, and some have started banning AI contributions completely. Her take was that you can't stop the wave, so you might as well learn to surf. Make your repo easy for people using AI to find their way around, and use AI yourself to help triage. She maintains a project with over 400 contributors, so she's definitely seen it firsthand.

Shoutout to Aspida for sending me. I'll be looking at test fixtures a little differently for a while.
