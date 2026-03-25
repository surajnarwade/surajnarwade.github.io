+++
title = "Backstage Tech Insights #9: Series Wrap-Up"
slug = "backstage-tech-insights-series-wrap-up"
description = "A wrap-up of the Backstage Tech Insights series with links to all eight posts and key takeaways."
author = "Suraj Narwade"
date = "2026-03-25"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "developer-portal"]
series = ["Backstage Tech Insights"]
+++

This is the final post in the Backstage Tech Insights series. Over the past eight posts, we went from "what is Tech Insights?" to setting up plugins, retrievers, checks, scorecards, custom retrievers, and production troubleshooting.

Here is everything we covered, with links to each post.

---

## The series

1. **[Introduction to Backstage Tech Insights](/blog/introduction-to-backstage-tech-insights/)**: What Tech Insights is, why it matters, and how it fits into your developer portal. Covers the core concepts: fact retrievers, checks, and scorecards.

2. **[Setting Up Tech Insights Backend](/blog/setting-up-backstage-tech-insights-backend/)**: Installing and configuring the backend plugin, database setup, and the module system.

3. **[Setting Up Fact Retrievers](/blog/setting-up-backstage-tech-insights-fact-retrievers/)**: How fact retrievers work, the built-in entity metadata fact retriever, fact schemas, handler functions, and scheduling.

4. **[Setting Up Checks](/blog/setting-up-backstage-tech-insights-checks/)**: Writing checks with the JSON rules engine, available operators, combining conditions with AND/OR logic, and verifying check results via the API.

5. **[Setting Up Scorecards](/blog/setting-up-backstage-tech-insights-frontend-scorecards/)**: Defining scorecards to group checks, installing the frontend plugin, and surfacing check results on entity pages.

6. **[Setting Up Service Maturity](/blog/setting-up-backstage-tech-insights-frontend-service-maturity/)**: Configuring the Service Maturity component for maturity rankings on entity pages.

7. **[Building Custom Fact Retrievers](/blog/backstage-tech-insights-custom-fact-retrievers/)**: Building your own fact retrievers to collect data from GitHub, CI systems, and other external sources. Covers registration, entity filtering, rate limits, and testing.

8. **[Troubleshooting & Production Best Practices](/blog/backstage-tech-insights-troubleshooting-production/)**: Debugging common issues, monitoring retriever health, database maintenance, and hardening Tech Insights for production.

---

## Key takeaways

If you take away a few things from this series, let it be these:

- **Start small.** Pick two or three standards that your organization already cares about. Build retrievers and checks for those first.

- **Visibility drives improvement.** In most cases, teams fix issues simply because they can see them on the entity page. You do not need enforcement to get results.

- **Facts first, checks second.** Get the data flowing before worrying about checks. Seeing raw facts often reveals things you did not expect.

- **Custom retrievers unlock the real value.** The built-in metadata retriever is a solid starting point, but the biggest wins come from connecting Tech Insights to your specific tools and systems.

- **Iterate.** Your standards will evolve. Add new retrievers and checks as your platform matures. Remove checks that no longer matter.

---

Tech Insights turns vague standards into something measurable, visible, and trackable.

If you have been following along, you now have everything you need to set it up in your own Backstage instance. Start simple, let the data tell you what to focus on, and build from there.

If you are not sure where to start, pick one standard this week and implement your first check.

Thanks for reading the series.
