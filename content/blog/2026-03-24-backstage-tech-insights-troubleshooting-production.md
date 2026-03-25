+++
title = "Backstage Tech Insights #8: Troubleshooting & Production Best Practices"
slug = "backstage-tech-insights-troubleshooting-production"
description = "Debug common Tech Insights issues, monitor fact retriever health, manage database growth, and harden your setup for production."
author = "Suraj Narwade"
date = "2026-03-24"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "troubleshooting", "production"]
series = ["Backstage Tech Insights"]
+++

In the [previous post](/blog/backstage-tech-insights-custom-fact-retrievers/), we built a custom fact retriever to check Kubernetes annotations on running deployments. By now you have a working Tech Insights setup: fact retrievers collecting data, checks evaluating it, and a frontend displaying results.

But running Tech Insights in a development environment is one thing. Running it reliably in production is another.

This post covers the issues you're most likely to hit, and how to run Tech Insights reliably in production.

---

## Common issues and how to debug them

### Facts are not being collected

This is the most frequent issue. You have registered a fact retriever, but the Tech Insights tab shows no data.

**Check the backend logs first.** The Tech Insights backend logs when retrievers start and finish. Look for errors or warnings related to your retriever ID.

```bash
# Search for tech-insights related logs
grep -i "tech-insights\|techInsights\|fact-retriever" backstage.log
```

Common causes:

- **The retriever is not registered.** Verify that the backend module is added in `packages/backend/src/index.ts` and that the fact retriever is registered via the extension point.
- **The retriever is not enabled in config.** Defining and registering a retriever in code is not enough. You must also add it under `techInsights.factRetrievers` in `app-config.yaml` with `cadence` and `lifecycle`. If it is missing there, Tech Insights will not schedule it, and no facts will be collected.
- **The cadence has not triggered yet.** Tech Insights does trigger an initial run on backend startup, but if the backend was already running when you added the retriever, the first run won't happen until the next cron window. Restart the backend or use a short cadence like `*/2 * * * *` (every 2 minutes) for testing.
- **The entity filter matches nothing.** If your `entityFilter` is `[{ kind: 'component', 'spec.type': 'service' }]` but your catalog has no entities matching that filter, no facts will be collected.

### Checks show "no data" even though facts exist

This usually means the check's `factIds` does not match the fact retriever's `id`.

```yaml
# In app-config.yaml under techInsights.factChecker.checks
myCheck:
  type: json-rules-engine
  name: My Check
  description: ...
  factIds:
    - entityMetadataFactRetriever  # Must match the retriever's id exactly
  rule:
    conditions:
      all:
        - fact: hasOwner
          operator: equal
          value: true
```

Double-check the `id` field in your fact retriever definition against the `factIds` in your check configuration.

### Facts are collected but checks always fail

Verify that the fact names in your check conditions match the schema keys in your fact retriever. These are case-sensitive.

```yaml
# This will fail silently if the fact name is wrong
rule:
  conditions:
    all:
      - fact: hasOwner        # Must match the schema key exactly
        operator: equal
        value: true
```

You can verify the actual fact values by querying the Tech Insights API directly:

```bash
curl "http://localhost:7007/api/tech-insights/facts/latest?entity=component:default/my-service&ids[]=entityMetadataFactRetriever"
```

---

## Retention and scheduling

The `lifecycle` configuration controls how long facts are retained, and cadence controls how often new facts are collected:

```yaml
techInsights:
  factRetrievers:
    entityMetadataFactRetriever:
      cadence: '*/15 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
```

**If you do not set a `lifecycle`, facts accumulate indefinitely.** This is the single biggest cause of database bloat in production Tech Insights setups. Set a reasonable TTL for every retriever.

**Sizing guidance:**

| Catalog size | Retriever cadence | Suggested TTL |
|-------------|-------------------|---------------|
| < 100 entities | Every 15 min | 4 weeks |
| 100–500 entities | Every hour | 2 weeks |
| 500+ entities | Every 6 hours | 1 week |

---

## Monitoring retriever health

In production, you need to know when a fact retriever stops working. Silent failures mean stale data, which means checks pass incorrectly.

### Log-based monitoring

At minimum, set up alerts on Tech Insights error logs. Look for:

- `Failed to run fact retriever`: the handler threw an error
- `Fact retriever timed out`: the handler exceeded the configured timeout
- `No entities matched filter`: the entity filter returned zero results (might be expected or might indicate a catalog issue)

### Freshness checks

A useful pattern is to alert on stale retriever activity by monitoring logs and expected run intervals. For example, if a retriever runs every 15 minutes, alert if no successful run is seen for more than 1 hour.

### Backstage health endpoint

Backstage exposes health endpoints. Ensure your monitoring includes the backend health check at `/healthcheck` and that the Tech Insights backend module is included in the health status.

---

## Performance tuning

### Slow fact retrieval for large catalogs

If you have hundreds or thousands of entities, a single retriever run can take minutes. Strategies:

1. **Narrow the entity filter.** Only process entities you actually need. If a check only applies to services, do not process APIs and libraries.

2. **Batch external API calls.** Instead of one API call per entity, batch where possible:

```typescript
// Instead of this:
for (const entity of entities) {
  const result = await fetchFromApi(entity);
}

// Do this: process in batches to avoid overwhelming the target API.
// Tune batchSize based on your API's rate limits.
const batchSize = 50;
for (let i = 0; i < entities.length; i += batchSize) {
  const batch = entities.slice(i, i + batchSize);
  const results = await Promise.all(batch.map(e => fetchFromApi(e)));
}
```

3. **Increase the timeout.** The default scheduled task timeout may be too short for large catalogs. Set it explicitly in the cadence configuration.

4. **Stagger retriever schedules.** If you have multiple retrievers, do not run them all at the same cron time. Stagger them to spread the load:

```yaml
entityMetadataFactRetriever:
  cadence: '0 * * * *'     # top of every hour
entityOwnershipFactRetriever:
  cadence: '15 * * * *'    # 15 minutes past every hour
techdocsFactRetriever:
  cadence: '30 * * * *'    # 30 minutes past every hour
```

### Frontend performance

The Tech Insights frontend fetches check results per entity. If you have many checks, the entity page load time can increase.

- Keep the number of checks reasonable, 10 to 20 is a good range
- Use scorecards to group checks so the UI does not render a long flat list
- Consider whether every check needs to be on every entity type

---

## Schema changes and database migrations

When you change a fact retriever's schema (add, remove, or modify fields), bump the `version` field in the retriever definition. Tech Insights uses this version to detect schema changes and update the underlying database columns.

What to expect when you bump the version:

- Tech Insights will run a migration on the next retriever execution to add new columns
- Existing fact rows keep their old values. New columns will be `null` until the next retriever run populates them
- Removing a field from the schema does not drop the column. The column stays but stops getting new values

If you change the schema without bumping the version, the new fields won't be picked up and your checks will see stale or missing data. This is a common gotcha in production.

---

## Multiple backend replicas

If you run multiple Backstage backend instances (which you should in production), you might wonder whether the same retriever runs on every replica simultaneously.

It doesn't. Tech Insights uses the Backstage `TaskScheduler`, which relies on PostgreSQL advisory locks for distributed coordination. Only one replica picks up a given retriever run at a time. The others skip it. This works out of the box with PostgreSQL. If you're still on SQLite (don't do this in production), there's no distributed locking and you'll get duplicate runs.

---

## Production checklist

Before going live with Tech Insights, verify the following:

- [ ] **PostgreSQL** is the database backend (not SQLite)
- [ ] **Lifecycle/TTL** is configured for every fact retriever
- [ ] **Cadence** is set appropriately for your catalog size
- [ ] **Logs** are set up for Tech Insights backend logs
- [ ] **Entity filters** are scoped correctly, no unnecessary processing
- [ ] **External API tokens** (GitHub, CI, etc.) are stored securely in Backstage config or a secrets manager
- [ ] **Rate limits** are accounted for in custom retrievers
- [ ] **Timeouts** are set high enough for your catalog size
- [ ] **Check descriptions** include actionable guidance so teams know how to fix failures

---

## What's next?

This is the last technical post in the series. In the [final post](/blog/backstage-tech-insights-series-wrap-up/), we will wrap up with a summary of everything we covered and key takeaways.

Most of these problems become visible quickly if you're watching the logs. Set up monitoring early and you'll catch issues before your users do.
