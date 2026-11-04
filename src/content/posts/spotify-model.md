---
title: "Thoughts on the Spotify Model for QA"
description: "What I learned about where QA fits when a company I work with moved to squads, chapters and tribes"
published: 2025-11-29
updated: 2025-11-29
tags: [Team Structure, Organizational Design, QA Engineering, Agile]
category: 'Quality Assurance'
draft: false
---

This year I got to watch an insurtech company I work with move to the Spotify model, going from a traditional department structure to cross-functional squads. It worked well, and it taught me a lot about where QA fits best in that kind of setup.

If you haven't run into it, the Spotify model came out of Spotify's engineering culture around 2012. Teams are organized around a feature or service: small squads with their own developers, designers and QA, each owning its services end to end. Specialists from different squads meet as "chapters," and squads working on related areas form "tribes."

## What got better

Meetings got smaller. Standups went from 30 people to 6 or 8, and people actually talked during them. If a meeting didn't apply to your squad, you could skip it.

Developers also felt invested in their services, and the ones who focused on one area made a much bigger impact than generalists did. People who know their system that well are less likely to break it.

## Where QA fits

The model works best when there's enough QA to embed someone in each squad, or a whole-team quality culture where developers own testing. Most companies, especially smaller ones, have more developers than QA engineers, so they have to be thoughtful about where QA goes. With a handful of QA engineers across several squads, rotating QA assignments or cross-squad support can work well alongside dedicated squad members.

The companies that handle it best put QA engineers where they add the most, on exploratory testing, edge cases and business logic, and have developers write more of the integration and API tests.

## What made it work

The company I watched built shared testing frameworks and utilities that every squad could use, so no squad depended on a single QA engineer. They trained developers to write thorough unit and integration tests. QA engineers were embedded in squads but also met regularly as a chapter to share knowledge, set standards and coordinate testing strategy.

They also planned around the people they had, and adapted the model to fit the team.
