---
title: "I'm now a Certified Test Automation Engineer"
description: "Passing ISTQB's Advanced Test Automation Engineer exam, the quiz app I built to study, and how it compares to CTFL"
published: 2026-09-02
updated: 2026-09-02
tags: [ISTQB, Certification, Test Automation, TAE, QA Engineering]
category: 'Quality Assurance'
image: '../../assets/images/astqb-TAE.png'
draft: false
---

I passed the ISTQB Advanced Level Test Automation Engineer (CTAL-TAE) exam. Like [CTFL](/posts/istqb/) back in November, I studied for about a week, and I enjoyed this one too. Shoutout again to Aspida for paying for the exam and giving me time to study.

To study, I built a quiz app and open-sourced it: [DominicABrooks/istqb-tae-quiz](https://github.com/DominicABrooks/istqb-tae-quiz). Every question maps to a learning objective in the syllabus, so I could test myself on the material and skip rereading the PDF. The syllabus and reference material are in [`/ref`](https://github.com/DominicABrooks/istqb-tae-quiz/tree/main/ref).

## What I got out of it

TAE is a pretty narrow certification. Almost all of it is about building and maintaining a Test Automation Solution (TAS), and the biggest chapter covers the Generic Test Automation Architecture (gTAA), which splits a TAS into layers for generating, defining, executing and adapting tests.

A lot of it was familiar from work. I've used Page Objects, abstraction layers, separate test data, CI pipelines and a few different ways of handling flaky tests, and TAE basically gave me a formal model for those decisions. Where CTFL gave me testing vocabulary, TAE gave me a way to think about the architecture behind automation. Shared names for things like the Test Adaptation Layer make design discussions a lot easier.

The syllabus walks through an ideal rollout: evaluate tools, run a pilot, get buy-in, then roll out gradually. The gTAA is just as useful on an existing project. If you join a team with hundreds of Playwright tests already, it gives you a map for improving the suite one layer at a time.

It also covers layered and keyword-driven frameworks, which suit large organizations with lots of contributors. For a smaller team, a clean Playwright framework with Page Objects and separate test data often covers everything, and the gTAA helps you notice when it's time to add more structure. When studying, I'd learn the exam answer and then think about how you'd tailor it to your own project.

I especially liked the section on verifying the TAS, which is about testing the automation itself before you trust its results.

## Should you take it?

In my CTFL post I wondered whether TAE would feel more practical, and it did. It lines up closely with my SDET work: framework design, CI integration, reducing flakiness and making automation results trustworthy.

If you're choosing between the two, do CTFL first. The vocabulary is useful, and it's the prerequisite anyway. I'd wait on TAE until you've spent a year or two building or maintaining automation, because the material clicks once you've seen the situations it describes.

I don't have another ISTQB certification planned. Lately I've been spending my study time on CTF prep and pwn.college.
