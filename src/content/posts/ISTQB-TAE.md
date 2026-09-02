---

title: "I'm now a Certified Test Automation Engineer"
description: "My experience getting ISTQB Advanced Test Automation Engineer certified and how it compares to CTFL"
published: 2026-09-02
updated: 2026-09-02
tags: [ISTQB, Certification, Test Automation, TAE, QA Engineering]
category: 'Quality Assurance'
image: '../../assets/images/astqb-TAE.png'
draft: false
------------

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

## The Good: Architecture Over Scripts

This is where I think TAE is much more useful than CTFL.

The certification treats test automation as software. You have architecture, technical debt, maintenance costs, deployment risks, reporting, and a lifecycle. That sounds obvious if you've spent years building automation, but it's surprisingly easy for automation teams to focus almost entirely on writing tests.

The gTAA was probably the most useful part for me. Having a common way to talk about things like the Test Adaptation Layer and the different responsibilities within an automation architecture makes design discussions easier.

It also made me look at some of the frameworks I've worked with differently. You can have hundreds of tests and still have a pretty poor automation solution if the underlying architecture makes those tests difficult to maintain.

I built the [istqb-tae-quiz](https://github.com/DominicABrooks/istqb-tae-quiz) specifically to drill these concepts. Each question maps back to a learning objective from the syllabus, so instead of repeatedly reading the PDF, I could actually test myself on the material. The source material is also included in [`/ref`](https://github.com/DominicABrooks/istqb-tae-quiz/tree/main/ref).

## The Critical: It's Still Pretty Prescriptive

My biggest complaint with CTFL was how prescriptive it could be. There was a lot of material where the real answer is "it depends," but the certification still wants you to choose the textbook answer.

TAE has some of the same problem.

For example, the syllabus describes a world where you can properly evaluate automation tools, run a pilot, get stakeholder buy-in, define responsibilities, and then gradually roll out the solution.

That's great if you're starting a new automation project with plenty of time and organizational support.

It's less useful when you join a team and someone hands you a repository containing 400 flaky Playwright tests and a CI pipeline that times out.

In practice, teams often don't get to design their automation architecture from scratch. You inherit whatever is already there and try to make it better while the product continues changing.

The same thing applies to tool selection. In the real world, you might choose Playwright because another team already uses it, because your developers know TypeScript, or because you're replacing an old Selenium suite. You probably aren't going to spend weeks evaluating five different tools against a weighted decision matrix.

TAE also presents layered and keyword-driven approaches as part of the more mature end of automation architecture. Those approaches can make sense for large organizations, but I wouldn't automatically consider them better.

Sometimes a relatively simple Playwright framework with good test data separation, Page Objects, and sensible abstractions is all you need. Adding another architectural layer doesn't automatically make the system better.

That's probably the biggest thing I'd keep in mind when studying for the certification: **the exam answer and the answer I'd choose for a real project aren't always the same thing.**

The difference is that TAE's recommendations are generally useful things to adapt from. I just wouldn't treat the diagrams as mandatory architecture.

## Still Worth It

I think TAE is worth getting, especially if you're already doing test automation professionally.

I wouldn't get it because I expect a recruiter to see "CTAL-TAE" on my resume and suddenly care. Hands-on experience building automation is still much more important.

The value for me was having a framework for thinking about things I already deal with.

The gTAA gives you a vendor-neutral way to talk about automation architecture. The sections on tool selection and deployment give you a checklist for problems that are easy to overlook. The reporting and TAS verification sections also cover areas that automation teams don't always think about until something goes wrong.

I especially liked the emphasis on verifying the automation itself. A test suite isn't automatically trustworthy just because it has thousands of passing tests.

There is a lot in TAE that I probably wouldn't implement exactly as written. That's fine. I don't think the certification needs to give you a perfect blueprint. It's more useful as a reference when you're making design decisions.

## What's Next

In my last post, I said I was curious whether TAE would be more practical than CTFL.

For me, it was.

It lines up pretty closely with the SDET work I'm already doing: framework design, CI integration, reducing flakiness, and figuring out how to make automation results trustworthy.

I don't have another ISTQB certification planned right now. I've actually been spending some of my study time on CTF preparation and working through pwn.college instead.

If you're deciding between CTFL and TAE, I'd do CTFL first if you haven't already. The vocabulary is useful, and it's the prerequisite anyway. After that, I'd wait until you've actually spent some time building or maintaining automation before doing TAE.

A lot of the material makes more sense once you've experienced the problems it's talking about.

Real-world automation is still messy. You inherit technical debt, deadlines don't move, tools get replaced, tests become flaky, and nobody has time to implement the perfect architecture.

TAE doesn't change any of that.

If you're on the fence about taking it, I'd recommend it once you've got a year or two of automation experience. Not because anyone cares, but because continued education is fun