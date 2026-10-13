---
title: "I'm now a Certified Test Automation Engineer"
description: "My experience getting ISTQB Advanced Test Automation Engineer certified and how it compares to CTFL"
published: 2026-09-02
updated: 2026-09-02
tags: [ISTQB, Certification, Test Automation, TAE, QA Engineering]
category: 'Quality Assurance'
image: '../../assets/images/astqb-TAE.png'
draft: false
---

I recently passed the ISTQB Advanced Level Test Automation Engineer (CTAL-TAE) exam and earned my ASTQB TAE certification. Like CTFL back in November, I spent about a week studying for it. I also enjoyed this one quite a bit.

Shoutout again to my employer, Aspida, for paying for the exam and giving me time to study. If you want to see how I prepared, I open-sourced the quiz app I made for myself: [DominicABrooks/istqb-tae-quiz](https://github.com/DominicABrooks/istqb-tae-quiz). I also included the syllabus and reference material in [`/ref`](https://github.com/DominicABrooks/istqb-tae-quiz/tree/main/ref).

## What I Learned

TAE is a pretty narrow certification. Almost everything revolves around designing, building, and maintaining a sustainable Test Automation Solution (TAS).

The syllabus covers eight chapters:

* **Introduction and Objectives for Test Automation**: Why to automate, when not to automate, success factors, and how automation fits into the SDLC.
* **Preparing for Test Automation**: Evaluating whether a system is suitable for automation, selecting tools, and running a pilot.
* **The Generic Test Automation Architecture (gTAA)**: The main part of the certification. It covers the different TAS layers, including generation, definition, execution, and adaptation, along with different approaches to framework design.
* **Deployment Risks and Contingencies**: Things like unrealistic expectations, brittle testware, tool lock-in, and ways to reduce those risks.
* **Test Automation Reporting and Metrics**: Measuring things like defect detection, execution progress, TAS health, and coverage.
* **Transitioning Manual Testing to an Automated Environment**: Deciding what should be automated, what shouldn't, and how to keep automation maintainable.
* **Verifying the TAS**: Testing the automation itself before relying on its results.
* **Continuous Improvement**: Keeping the TAS useful as the application, tools, and team change.

A lot of this was familiar from work, but TAE gave names and structure to things I was already doing.

For example, I've worked with Page Objects, abstraction layers, separated test data, API testing, UI testing, CI pipelines, and different approaches to dealing with flaky tests. TAE basically gave me a formal model for thinking about those decisions.

That's probably the biggest difference I noticed compared to CTFL. CTFL gives you a lot of testing terminology. TAE gives you a way to think about the architecture behind your automation.

## Highlight: Architecture Over Scripts

This is where TAE builds on CTFL the most.

The certification treats test automation as software. You have architecture, technical debt, maintenance costs, deployment risks, reporting, and a lifecycle. That sounds obvious if you've spent years building automation, but it's surprisingly easy for automation teams to focus almost entirely on writing tests.

The gTAA was probably the most useful part for me. Having a common way to talk about things like the Test Adaptation Layer and the different responsibilities within an automation architecture makes design discussions easier.

It also made me look at some of the frameworks I've worked with differently. A great automation solution isn't measured by test count. It's measured by how easy those tests are to maintain, and that comes from the underlying architecture.

I built the [istqb-tae-quiz](https://github.com/DominicABrooks/istqb-tae-quiz) specifically to drill these concepts. Each question maps back to a learning objective from the syllabus, so instead of repeatedly reading the PDF, I could actually test myself on the material. The source material is also included in [`/ref`](https://github.com/DominicABrooks/istqb-tae-quiz/tree/main/ref).

## Applying It to Real Projects

Like CTFL, TAE describes an ideal path. The syllabus walks through evaluating automation tools, running a pilot, getting stakeholder buy-in, defining responsibilities, and then gradually rolling out the solution.

That's a great playbook for a new automation project, and the same ideas carry over to existing ones too.

Often you join a team that already has a suite in place, maybe hundreds of Playwright tests and a busy CI pipeline. That's where TAE really shines for me: the gTAA gives you a map for improving what's already there, one layer at a time, while the product keeps moving.

Tool selection works the same way. A weighted decision matrix is a useful way to make the trade-offs explicit, even when the right answer is the tool another team already uses, the language your developers already know, or the natural next step from an existing Selenium suite.

TAE also covers layered and keyword-driven approaches, which are a great fit for large organizations with many contributors. For smaller teams, a clean Playwright framework with good test data separation, Page Objects, and sensible abstractions often covers everything you need, and the gTAA helps you recognize when it's time to add more structure.

That's the mindset I'd recommend when studying: **learn the exam answer, then think about how you'd tailor it to your own project.** TAE's recommendations are a strong foundation to adapt from.

## Still Worth It

I think TAE is worth getting, especially if you're already doing test automation professionally.

Hands-on experience building automation still matters more than any certification. The value for me was having a vocabulary and framework for the architecture decisions I already make every day.

The gTAA gives you a vendor-neutral way to talk about automation architecture. The sections on tool selection and deployment give you a checklist for problems that are easy to overlook. The reporting and TAS verification sections cover areas that are easy to overlook and pay off quickly.

I especially liked the emphasis on verifying the automation itself. That's what turns thousands of passing tests into results the whole team can trust.

I think of TAE as a reference for design decisions, not a blueprint, and that flexibility is what makes it useful across so many different teams.

## What's Next

In my last post, I said I was curious whether TAE would be more practical than CTFL.

For me, it was.

It lines up pretty closely with the SDET work I'm already doing: framework design, CI integration, reducing flakiness, and figuring out how to make automation results trustworthy.

I don't have another ISTQB certification planned right now. I've actually been spending some of my study time on CTF preparation and working through pwn.college instead.

If you're deciding between CTFL and TAE, I'd do CTFL first if you haven't already. The vocabulary is useful, and it's the prerequisite anyway. After that, I'd wait until you've actually spent some time building or maintaining automation before doing TAE.

The material really clicks once you've seen the situations it describes firsthand.

Real-world automation keeps evolving: codebases grow, deadlines shift, tools get replaced, and priorities change. TAE gives you a solid framework for making good decisions through all of it.

If you're on the fence about taking it, I'd recommend it once you've got a year or two of automation experience. It won't replace hands-on experience, but it will sharpen how you think about and explain the automation you build.