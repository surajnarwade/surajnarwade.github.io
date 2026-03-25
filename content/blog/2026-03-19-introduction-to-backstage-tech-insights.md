+++
title = "Backstage Tech Insights #1: Introduction to Tech Insights"
slug = "introduction-to-backstage-tech-insights"
description = "An introduction to the Tech Insights plugin in Backstage, what it does, why it matters, and how it fits into your developer portal."
author = "Suraj Narwade"
date = "2026-03-19"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "developer-portal"]
series = ["Backstage Tech Insights"]
+++

If you are running Backstage as your internal developer portal, you have probably dealt with this question at some point:

> How do we know if our services are following the standards we agreed on?

Maybe you want to check whether every service has a README. Maybe you want to ensure all services have CI configured. Or perhaps you want to verify that a particular annotation is present on the components or their Kubernetes resources.

Doing this manually does not scale. For fewer services, you could do spreadsheets but beyond that, building custom dashboards for every check is a lot of effort.

This is exactly what **Tech Insights** solves.

---

## What is Tech Insights?

[Tech Insights](https://github.com/backstage/community-plugins/tree/main/workspaces/tech-insights) is a Backstage plugin that lets you collect facts (data) about your software catalog entities and run checks against those facts.

Think of it as a lightweight compliance and quality framework built directly into your developer portal.

The core idea is simple:

1. **Collect facts** about entities (services, libraries, websites, anything in your catalog)
2. **Define checks** that evaluate those facts against expected values
3. **Show results** so teams can see where they stand (either as a scorecard or service maturity)

It gives you a structured way to measure and track standards across your organisation without building everything from scratch.

---

## Why does this matter?

In most organisations, standards exist but enforcement is inconsistent.

Someone writes a wiki page that says "every service must have a Dockerfile". But there is no automated way to verify it. Over time, compliance drifts and nobody notices until something breaks.

Tech Insights addresses this by:

- **Making standards visible**: instead of hidden wiki pages, checks are surfaced in the Backstage UI next to each entity
- **Automating verification**: facts are collected automatically on a schedule
- **Tracking over time**: you can observe whether compliance improves or regresses
- **Reducing manual audits**: no more spreadsheets or quarterly reviews

---

## How does it work at a high level?

Tech Insights has three main concepts:

### Fact Retrievers

A fact retriever is responsible for collecting facts (data) about entities. It runs on a schedule and stores the results as facts.

For example, a fact retriever might check whether an entity has a specific annotation, or whether a GitHub repository has branch protection enabled.

### Checks

A check evaluates one or more facts against a condition. For example:

- "Does the entity have a `backstage.io/techdocs-ref` annotation?"
- "Is the CI pipeline status passing?"
- "Does the service have more than zero test cases?"

Checks return a simple pass or fail result.

### Scorecards

Scorecards group related checks together. For example, you might have a "Production Readiness" scorecard that includes checks for documentation, CI, monitoring, and ownership. also as name indicates, it shows score (for example, 3/5 meaning 3 checks are passing out of total 5 checks)

### Service Maturity

Tech Insights Maturity helps teams see where their services stand and what they need to do next. It introduces maturity rankings and categories into your checks.

---

## What can you check with Tech Insights?

Here are some common use cases:

- **Documentation**: does the entity have a README? Is TechDocs configured?
- **Ownership**: is there a defined owner in `catalog-info.yaml`?
- **CI/CD**: does the repository have a CI pipeline configured?
- **Security**: are dependency scanning tools enabled?
- **Annotations**: are required Backstage annotations present?
- **Custom standards**: anything specific to your organisation

The plugin is flexible enough that you can define checks for almost any standard you care about.

---

## What does it look like in practice?

Once set up, Tech Insights adds a section to each entity page in Backstage. Teams can see:

- Which checks pass and which fail
- What facts were collected
- How their entity compares to the expected standards

This visibility is surprisingly effective. Teams often fix issues simply because they can see them, without anyone having to file a ticket or send a reminder.

---

## Where does Tech Insights fit in the platform?

Tech Insights is not a governance tool that blocks deployments. It is more of a visibility tool that helps teams understand where they stand.

You can think of it as a layer on top of your software catalog:

- The **catalog** tells you what exists
- **Tech Insights** tells you how well those things meet your standards

It works well alongside other Backstage features like TechDocs, scaffolder templates, and the catalog itself.

---

## What is coming next in this series?

This is the first post in a series on Backstage Tech Insights. Over the next few posts, we will cover:

1. **Setting up the backend**: installing and configuring the backend plugin
2. **Built-in Fact Retrievers**: how to collect data about your entities using built in fact retrievers
3. **Defining Checks**: how to write checks that evaluate facts
4. **Scorecards and Visualization**: grouping checks into scorecards and displaying them in the UI
5. **Frontend, Service Maturity**: tracking maturity levels for your services
6. **Custom Fact Retrievers**: building your own retrievers for organisation-specific needs
7. **Troubleshooting & Production Best Practices**: debugging, monitoring, and hardening for production
8. **Series Wrap Up**: key takeaways and next steps

By the end of this series, you will have a working Tech Insights setup that tracks the standards that matter to your team.

---

Tech Insights takes the guesswork out of "are our services following the agreed standards?" and replaces it with something measurable and visible.

If you are already running Backstage, this is one of the most practical plugins you can add to your developer portal.

I hope this gives you a clear idea of what the Tech Insights plugin offers and how it builds on top of the existing software catalog. Let's get into the setup in the next post.
