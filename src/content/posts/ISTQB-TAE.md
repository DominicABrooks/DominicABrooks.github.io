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

I recently passed the ISTQB Advanced Level Test Automation Engineer (CTAL-TAE) exam and earned my ASTQB TAE certification. Like with CTFL back in November, it took about a week of focused studying, and also like last time I had fun with it.

Shoutout again to my employer, Aspida, for paying for the exam and supporting the study time. And if you want to see how I studied, I open-sourced my prep: a full quiz app covering the entire syllabus at [DominicABrooks/istqb-tae-quiz](https://github.com/DominicABrooks/istqb-tae-quiz) with the syllabus and reference docs mirrored in [`/ref`](https://github.com/DominicABrooks/istqb-tae-quiz/tree/main/ref).

## What I Learned

Where CTFL is broad and vocabulary-heavy, TAE is narrow and architectural. It is entirely about building and maintaining a sustainable Test Automation Solution (TAS) not just writing scripts that click buttons.

The syllabus is organized around eight chapters:

- **Introduction and Objectives for Test Automation**: Why you automate (and when you should not), success factors, and how automation fits into the SDLC
- **Preparing for Test Automation**: Evaluating the SUT for automatability, tool evaluation and selection, and running a pilot project
- **The Generic Test Automation Architecture (gTAA)**: The core of the cert layers (generation, definition, execution, adaptation), components, and design approaches from linear scripts to structured, data-driven, and keyword-driven frameworks
- **Deployment Risks and Contingencies**: The risks specific to automation unrealistic expectations, testware brittleness, tool lock-in and mitigations like piloting and staged rollout
- **Test Automation Reporting and Metrics**: Selecting the right metrics (defect detection, execution progress, TAS health, SUT coverage), collecting them from the TAS, and reporting for different stakeholders
- **Transitioning Manual Testing to an Automated Environment**: What to automate (and what not to), maintainability and the cost of the automation code itself
- **Verifying the TAS**: Testing the test automation verifying the TAS itself is correct, reliable, and fit for purpose before you trust its results
- **Continuous Improvement**: Tuning the TAS over time as the SUT, tools, and team evolve

CTFL gave me the vocabulary. TAE gave me a reference architecture. Having names for things like the "Test Adaptation Layer" or the difference between a pilot and a proof-of-concept makes design discussions much faster.

## The Good: Architecture Over Scripts

This is where TAE is miles ahead of CTFL.

CTFL teaches techniques. TAE teaches system design. It forces you to think about automation as a software product in its own right, with its own architecture, technical debt, and lifecycle. Concepts I already used daily Page Objects, abstraction layers, test data separation, API vs. UI tradeoffs finally had formal structure around them.

If CTFL is "how to talk about testing," TAE is "how to reason about automation design" and that is far more applicable to my day-to-day as an SDET.

I built the [istqb-tae-quiz](https://github.com/DominicABrooks/istqb-tae-quiz) to drill this. Every question maps to a learning objective from the syllabus, so I could test gTAA concepts, deployment risks, and reporting without just re-reading PDFs. It is in [`/ref`](https://github.com/DominicABrooks/istqb-tae-quiz/tree/main/ref) if you want the source material.

## The Critical: Still Prescriptive, Still Idealized

My main gripe with CTFL was rigidity presenting highly idealized roles and processes as if "testing is context dependent" did not apply to them. TAE is better, but it has not fully escaped that.

TAE still assumes a world where:

- You have time to run a proper pilot, evaluate 5+ tools against a weighted matrix, and get stakeholder sign-off before writing a line of code
- Automation has clear ownership separation between Test Automation Engineer, Test Automation Architect, and manual testers transitioning in
- Someone is actively monitoring TAS metrics and funding continuous improvement as a line item

In reality, most teams inherit a flaky Cypress or Playwright suite on day one, pick a tool because "the other team uses it," and improve it between feature sprints. You do not always get a greenfield gTAA to design you get a repo with 400 tests, no abstraction layer, and a CI job that times out.

The syllabus also leans heavily toward layered, keyword-driven architectures as the "mature" end state. Those are great for longevity at enterprise scale, but for many teams a well-structured single-layer with good data separation and Page Objects is the right pragmatic call. The cert does not always make that nuance explicit exam-correct vs. context-correct can diverge.

That said, the prescriptiveness is less grating here than in CTFL, because at least the architecture guidance is genuinely useful to adapt from. Take it as a reference design, not a mandate.

## Still Worth It

Just like CTFL, I think TAE is worth it but for a different reason. CTFL is worth it for the shared language. TAE is worth it for the design framework.

It provides:

- A vendor-neutral architecture (gTAA) to evaluate and discuss any framework, whether it is Playwright, Selenium, or something custom
- A checklist for tool selection, piloting, and deployment that prevents the classic "we automated 200 tests in month one and spent year two maintaining them" trap
- Clear guidance on reporting and verifying the TAS the two areas most teams skip and then regret
- Industry recognition that complements hands-on automation experience

Take the process diagrams with a grain of salt, but take the architecture seriously. Use gTAA as a lens, not a blueprint.

## What's Next

In my last post I said I was curious whether TAE would be more practical than CTFL it is. It aligns directly with the SDET work I am already doing: framework design, flakiness reduction, CI integration, and making automation results trustworthy.

I do not have another ISTQB level queued up immediately. I've actually been practicing for a CTF event I'm working on studying for in pwn.college recently.

If you are deciding between CTFL and TAE: do CTFL first if you have not. It is the prerequisite and the vocabulary matters. Then do TAE when you are actually building or owning automation that is when it clicks.

Real-world automation is still messy, context-dependent, and constrained by time and tech debt no syllabus fixes that. But having a reference architecture like gTAA makes it easier to make intentional tradeoffs instead of accidental ones.

If you are on the fence about TAE, I would say go for it once you have a year or two of hands-on automation under your belt. Not because anyone cares about it, but because continued education is fun.

