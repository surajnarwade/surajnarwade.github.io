+++
title = "Backstage Tech Insights #8: Troubleshooting & Production Best Practices"
slug = "backstage-tech-insights-troubleshooting-production"
description = "Debug common Tech Insights issues, monitor fact retriever health, manage database growth, and harden your setup for production."
author = "Suraj Narwade"
date = "2026-03-26"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "troubleshooting", "production"]
series = ["Backstage Tech Insights"]
+++

In the [previous post](/blog/backstage-tech-insights-custom-fact-retrievers/), we built a custom fact retriever to check Kubernetes annotations on running deployments. By now you have a working Tech Insights setup: fact retrievers collecting data, checks evaluating it, and a frontend displaying results.

But running Tech Insights in a development environment is one thing. Running it reliably in production is another.

This post covers the issues you are most likely to hit and how to solve them.

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
- **The cadence has not triggered yet.** If you set a 6-hour cron, the first run will not happen until the next cron window. For testing, use a short cadence like `*/2 * * * *` (every 2 minutes).
- **The entity filter matches nothing.** If your `entityFilter` is `[{ kind: 'component', 'spec.type': 'service' }]` but your catalog has no entities matching that filter, no facts will be collected.

### Checks show "no data" even though facts exist

This usually means the check's `factRef` does not match the fact retriever's `id`.

```yaml
# In app-config.yaml
techInsights:
  factChecker:
    checks:
      myCheck:
        factRef: entityMetadataFactRetriever  # Must match the retriever's id exactly
```

Double-check the `id` field in your fact retriever definition against the `factRef` in your check configuration.

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
curl http://localhost:7007/api/tech-insights/v1/facts/latest?entity=component:default/my-service
```

---

## Database management

### Understanding the Tech Insights tables

Tech Insights creates its own database (`backstage_plugin_tech-insights`) with tables for:

- **Fact schemas**: the registered retriever schemas
- **Facts**: the actual collected data, one row per entity per retriever per run
- **Check results**: cached check evaluation results

Over time, the facts table grows with every retriever run.

### Managing data growth

The `lifecycle` configuration controls how long facts are retained:

```yaml
techInsights:
  factRetrievers:
    entityMetadataFactRetriever:
      cadence: '*/15 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
```

If you do not set a `lifecycle`, facts are kept indefinitely. For production, always set a TTL.

**Sizing guidance:**

| Catalog size | Retriever cadence | Suggested TTL |
|-------------|-------------------|---------------|
| < 100 entities | Every 15 min | 4 weeks |
| 100–500 entities | Every hour | 2 weeks |
| 500+ entities | Every 6 hours | 1 week |

If the database is already large, you can clean up old facts manually:

```sql
DELETE FROM facts WHERE timestamp < NOW() - INTERVAL '14 days';
```

Run this during low-traffic hours and vacuum the table afterwards if using PostgreSQL.

### Database migrations

When upgrading the Tech Insights plugin, migrations run automatically on startup. If a migration fails:

1. Check the backend logs for the specific migration error
2. Verify database permissions. The Backstage database user needs `CREATE TABLE` and `ALTER TABLE` privileges
3. If the migration is stuck, check if a lock exists in the `knex_migrations_lock` table and release it

---

## Monitoring retriever health

In production, you need to know when a fact retriever stops working. Silent failures mean stale data, which means checks pass incorrectly.

### Log-based monitoring

At minimum, set up alerts on Tech Insights error logs. Look for:

- `Failed to run fact retriever`: the handler threw an error
- `Fact retriever timed out`: the handler exceeded the configured timeout
- `No entities matched filter`: the entity filter returned zero results (might be expected or might indicate a catalog issue)

### Freshness checks

A useful pattern is to create a "meta" fact retriever that checks the freshness of other retrievers' data:

```typescript
// Query the facts table directly to check when facts were last updated
const lastRun = await db.query(
  `SELECT MAX(timestamp) as last_run FROM facts WHERE retriever_id = $1`,
  ['entityMetadataFactRetriever']
);

const hoursSinceLastRun = (Date.now() - lastRun.last_run) / (1000 * 60 * 60);
if (hoursSinceLastRun > 2) {
  logger.warn(`entityMetadataFactRetriever has not run in ${hoursSinceLastRun} hours`);
}
```

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

// Do this:
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

## Production checklist

Before going live with Tech Insights, verify the following:

- [ ] **PostgreSQL** is the database backend (not SQLite)
- [ ] **Lifecycle/TTL** is configured for every fact retriever
- [ ] **Cadence** is set appropriately for your catalog size
- [ ] **Error monitoring** is set up for Tech Insights backend logs
- [ ] **Database backups** include the `backstage_plugin_tech-insights` database
- [ ] **Entity filters** are scoped correctly, no unnecessary processing
- [ ] **External API tokens** (GitHub, CI, etc.) are stored securely in Backstage config or a secrets manager
- [ ] **Rate limits** are accounted for in custom retrievers
- [ ] **Timeouts** are set high enough for your catalog size
- [ ] **Check descriptions** include actionable guidance so teams know how to fix failures

---

## What is next?

This is the last technical post in the series. In the [final post](/blog/backstage-tech-insights-series-wrap-up/), we will wrap up with a summary of everything we covered and key takeaways.

---

Tech Insights is easy to set up, but running it well in production requires attention to database growth, retriever health, and performance. The issues covered here are the ones that come up most often in real deployments.

Set up monitoring early, keep your lifecycle configuration tight, and stagger your retrievers. Most problems become visible quickly if you are watching the logs.
