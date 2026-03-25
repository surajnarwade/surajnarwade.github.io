+++
title = "Backstage Tech Insights #3: Setting Up Fact Retrievers"
slug = "setting-up-backstage-tech-insights-fact-retrievers"
description = "Configure the built-in fact retrievers in Backstage Tech Insights, understand the facts they produce, and verify data collection."
author = "Suraj Narwade"
date = "2026-03-19"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "fact-retrievers"]
series = ["Backstage Tech Insights"]
+++

In the [previous post](/blog/setting-up-backstage-tech-insights-backend/), we installed the Tech Insights backend plugin. Now it is time to make it do something useful.

Fact retrievers are the data collection layer of Tech Insights. They gather information about your catalog entities and store it as structured facts. Without fact retrievers, there is nothing for checks to evaluate.

---

## What is a fact retriever?

A fact retriever is a function that:

1. Runs on a defined schedule (e.g., every 15 minutes, every hour)
2. Iterates over entities in the catalog
3. Collects specific data points about each entity
4. Stores those data points as facts

Once facts are stored, checks can evaluate them.

---

## What does a fact retriever look like internally?

Before we configure anything, it helps to understand what a fact retriever looks like under the hood. Whether it is a built-in one or a custom one you write yourself, every fact retriever follows the same structure. Here is representation for `entityMetadataFactRetriever`:

```typescript
const entityMetadataFactRetriever: FactRetriever = {
  id: 'entityMetadataFactRetriever',       // unique identifier
  version: '0.0.1',                         // schema version
  title: 'Entity Metadata',                 // human-readable title
  description: 'Generates facts which indicate the completeness of entity metadata',
  entityFilter: [{ kind: 'component' }],    // which entities to process
  schema: {                                  // declares what facts this retriever produces
    hasTitle: {
      type: 'boolean',
      description: 'The entity has a title in metadata',
    },
    hasDescription: {
      type: 'boolean',
      description: 'The entity has a description in metadata',
    },
    hasTags: {
      type: 'boolean',
      description: 'The entity has tags in metadata',
    },
  },
  handler: async ({ discovery, entityFilter }) => {
    // fetch entities from the catalog and return facts
  },
};
```

You can see the full source of this `entityMetadataFactRetriever` in the [community plugins repository](https://github.com/backstage/community-plugins/blob/main/workspaces/tech-insights/plugins/tech-insights-backend/src/service/fact/factRetrievers/entityMetadataFactRetriever.ts).

The key parts:

| Field | What it does |
|-------|-------------|
| **`id`** | Unique identifier. You will reference this in check configurations later. |
| **`version`** | Version for the fact retriever, should be changed if there is schema change |
| **`entityFilter`** | Controls which catalog entities the retriever processes. |
| **`schema`** | Declares what facts the retriever produces. Each fact has a name, type (`boolean`, `number`, or `string`), and description. |
| **`handler`** | The function that actually collects the data. It receives entities from the catalog and returns facts for each one. |

This is the same pattern for all fact retrievers. The built-in ones are already written for you, but when we build custom retrievers in [post #7](/blog/backstage-tech-insights-custom-fact-retrievers/), you will write your own handler and schema following this exact structure.

For now, you don't need to write any code. The built-in retrievers are ready to use. Let's configure them.

---

## Registering fact retrievers in app-config.yaml

The Tech Insights backend plugin we installed in [post #2](/blog/setting-up-backstage-tech-insights-backend/) ships with three built-in fact retrievers. But they won't do anything until you register them in your `app-config.yaml`. Without registration, the backend has no idea which retrievers to run, how often to run them, or how long to keep the data. This applies to custom fact retrievers too. Whether built-in or custom, every fact retriever needs to be registered with a cadence and lifecycle before it starts collecting facts.

The built-in retrievers can be configured under the `techInsights.factRetrievers` key as shown below:

```yaml
techInsights:
  factRetrievers:
    entityMetadataFactRetriever:
      cadence: '*/15 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
    entityOwnershipFactRetriever:
      cadence: '*/15 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
    techdocsFactRetriever:
      cadence: '*/15 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
```

### Understanding the configuration fields

Each fact retriever accepts these fields:

| Field | Required | Description |
|-------|----------|-------------|
| **`cadence`** | Yes | A cron expression that controls how often the retriever runs. `*/15 * * * *` means every 15 minutes. `0 * * * *` means once per hour. |
| **`lifecycle`** | No (but recommended) | Controls how long collected facts are retained before being cleaned up. If omitted, facts are kept indefinitely which will grow your database over time. |

### Lifecycle options

The `lifecycle` field supports the following `timeToLive` units:

```yaml
# Keep facts for 2 weeks
lifecycle: { timeToLive: { weeks: 2 } }

# Keep facts for 30 days
lifecycle: { timeToLive: { days: 30 } }

# Keep facts for 24 hours
lifecycle: { timeToLive: { hours: 24 } }
```

Alternatively, you can limit by count instead of time:

```yaml
# Keep only the last 10 fact snapshots per entity
lifecycle: { maxItems: 10 }
```

> Note: Use a shorter TTL if you have many entities or frequent cadences.

---

## Built-in fact retrievers

Now let's look at what each retriever collects. Understanding the exact facts they produce is important because you will reference these fact names when defining checks in the next post.

### 1. entityMetadataFactRetriever

This retriever inspects the metadata of each entity in the catalog and checks for completeness.

**Facts produced:**

| Fact name | Type | Description |
|-----------|------|-------------|
| `hasTitle` | boolean | The entity has a `title` field in `metadata` |
| `hasDescription` | boolean | The entity has a non-empty `description` field in `metadata` |
| `hasTags` | boolean | The entity has at least one tag in `metadata.tags` |

These facts map directly to the fields in your `catalog-info.yaml`:

```yaml
# catalog-info.yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: my-service
  title: "My Service"              # hasTitle: true
  description: "Handles payments"  # hasDescription: true
  tags:                            # hasTags: true
    - python
    - payments
spec:
  type: service
  owner: team-payments
  lifecycle: production
```

### 2. entityOwnershipFactRetriever

This retriever checks whether entities have ownership defined.

**Facts produced:**

| Fact name | Type | Description |
|-----------|------|-------------|
| `hasOwner` | boolean | The entity has an `owner` field in `spec` |
| `hasGroupOwner` | boolean | The owner is a group (not an individual user) |

Ownership is a fundamental standard in most organisations. This retriever lets you check not just whether an owner exists, but whether the owner is a team rather than an individual. Team ownership is generally the recommended practice for production services.

```yaml
# catalog-info.yaml
spec:
  owner: team-payments        # hasOwner: true, hasGroupOwner: true
  # vs
  owner: user:john.doe        # hasOwner: true, hasGroupOwner: false
```

### 3. techdocsFactRetriever

This retriever checks whether entities are configured for TechDocs, Backstage's built-in documentation system.

**Facts produced:**

| Fact name | Type | Description |
|-----------|------|-------------|
| `hasAnnotationBackstageIoTechdocsRef` | boolean | The entity has the `backstage.io/techdocs-ref` annotation |
| `hasAnnotationBackstageIoTechdocsEntity` | boolean | The entity has the `backstage.io/techdocs-entity` annotation |

These facts tell you whether an entity is set up for TechDocs. The `techdocs-ref` annotation points to where the documentation source lives. The `techdocs-entity` annotation is used when documentation is defined in a separate entity. If both are missing, TechDocs cannot generate documentation for that entity.

```yaml
# catalog-info.yaml
metadata:
  annotations:
    backstage.io/techdocs-ref: dir:.       # hasAnnotationBackstageIoTechdocsRef: true
    backstage.io/techdocs-entity: component:default/my-docs  # hasAnnotationBackstageIoTechdocsEntity: true
```

---

## All built-in facts at a glance

Here is the complete reference of every fact available from the built-in retrievers:

| Fact retriever | Fact name | Type |
|---------------|-----------|------|
| `entityMetadataFactRetriever` | `hasTitle` | boolean |
| `entityMetadataFactRetriever` | `hasDescription` | boolean |
| `entityMetadataFactRetriever` | `hasTags` | boolean |
| `entityOwnershipFactRetriever` | `hasOwner` | boolean |
| `entityOwnershipFactRetriever` | `hasGroupOwner` | boolean |
| `techdocsFactRetriever` | `hasAnnotationBackstageIoTechdocsRef` | boolean |
| `techdocsFactRetriever` | `hasAnnotationBackstageIoTechdocsEntity` | boolean |

You will use these exact fact names in the [next post](/blog/setting-up-backstage-tech-insights-checks/) when defining checks.

---

## Looking at logs

Once you add the above block in your Backstage config and start the backend, you should see logs like these. They indicate that the fact retrievers are registered and scheduled:

```
2026-03-24T17:07:48.931Z tech-insights info Registered scheduled task: entityMetadataFactRetriever, {"version":2,"cadence":"*/15 * * * *","initialDelayDuration":"PT5S","timeoutAfterDuration":"PT5M"} task="entityMetadataFactRetriever"
2026-03-24T17:07:48.932Z tech-insights info Registered scheduled task: techdocsFactRetriever, {"version":2,"cadence":"*/15 * * * *","initialDelayDuration":"PT5S","timeoutAfterDuration":"PT5M"} task="techdocsFactRetriever"
2026-03-24T17:07:48.932Z tech-insights info Registered scheduled task: entityOwnershipFactRetriever, {"version":2,"cadence":"*/15 * * * *","initialDelayDuration":"PT5S","timeoutAfterDuration":"PT5M"} task="entityOwnershipFactRetriever"
2026-03-24T17:07:48.932Z tech-insights info Scheduled 3/3 fact retrievers into the tech-insights engine
```

The following logs show that retrievers are running and gathering facts:

```
2026-03-24T17:08:04.007Z tech-insights info Retrieving facts for fact retriever entityMetadataFactRetriever
2026-03-24T17:08:04.008Z tech-insights info Retrieving facts for fact retriever entityOwnershipFactRetriever
2026-03-24T17:08:04.010Z tech-insights info Retrieving facts for fact retriever techdocsFactRetriever
2026-03-24T17:08:04.044Z tech-insights info Stored 12 facts for fact retriever entityOwnershipFactRetriever in 0.0s
2026-03-24T17:08:04.046Z tech-insights info Stored 18 facts for fact retriever entityMetadataFactRetriever in 0.0s
2026-03-24T17:08:04.048Z tech-insights info Stored 18 facts for fact retriever techdocsFactRetriever in 0.0s
```

## Verifying API outputs

Backstage runs on port `7007` by default. All the API URLs below assume you are running the backend locally on this default port.

### Checking fact schemas

You can verify that all retrievers are properly registered by hitting the fact schemas endpoint as shown below:

```
curl http://localhost:7007/api/tech-insights/fact-schemas
```

This returns the schema for every registered retriever, including the fact names, types, and descriptions. If a retriever is missing from the response, it wasn't registered correctly in your config.

### Checking that facts are being collected

Once the retrievers are configured and the cadence has triggered for the first time, verify that facts are flowing by querying the Tech Insights API:

```bash
curl "http://localhost:7007/api/tech-insights/facts/latest?entity=component:default/my-service-1&ids[]=entityMetadataFactRetriever"
```

You should see a response containing the facts for that entity:

```json
{
  "entityMetadataFactRetriever": {
    "id": "entityMetadataFactRetriever",
    "entity": {
      "namespace": "default",
      "kind": "component",
      "name": "my-service-1"
    },
    "timestamp": "2026-03-24T17:13:05.000+00:00",
    "version": "0.0.1",
    "facts": {
      "hasTitle": false,
      "hasDescription": true,
      "hasTags": false
    }
  }
}
```

The `ids[]` parameter is an array, so you can query multiple retrievers at once by repeating it:

```bash
curl "http://localhost:7007/api/tech-insights/facts/latest?entity=component:default/my-service-1&ids[]=entityMetadataFactRetriever&ids[]=entityOwnershipFactRetriever"
```

output would be:

```json
{
  "entityOwnershipFactRetriever": {
    "id": "entityOwnershipFactRetriever",
    "entity": {
      "namespace": "default",
      "kind": "component",
      "name": "my-service-1"
    },
    "timestamp": "2026-03-24T17:14:05.000+00:00",
    "version": "0.0.1",
    "facts": {
      "hasOwner": true,
      "hasGroupOwner": false
    }
  },
  "entityMetadataFactRetriever": {
    "id": "entityMetadataFactRetriever",
    "entity": {
      "namespace": "default",
      "kind": "component",
      "name": "my-service-1"
    },
    "timestamp": "2026-03-24T17:14:05.000+00:00",
    "version": "0.0.1",
    "facts": {
      "hasTitle": false,
      "hasDescription": true,
      "hasTags": false
    }
  }
}
```

If these requests fail with an authentication error, you may need to disable the default auth policy for local development. Add the following to your `app-config.yaml`:

```yaml
backend:
  auth:
    dangerouslyDisableDefaultAuthPolicy: true
```

This disables the default auth policy so you can hit the API without a token. Only use this for local development. You can read more about this in the [Backstage auth service migration docs](https://backstage.io/docs/tutorials/auth-service-migration/#disabling-the-default-auth-policy).

If you see no data, check the backend logs. Common reasons:

- The cadence has not triggered yet. If you just started the backend, wait for the first cron window or set a short cadence like `*/2 * * * *` for testing
- The entity does not exist in the catalog. Verify with `curl http://localhost:7007/api/catalog/entities`

Later, once we set up the frontend ([post #5](/blog/setting-up-backstage-tech-insights-frontend-scorecards/)), you will be able to see these facts visually on each entity page.

---

## What is next?

Now that we have facts being collected from all built-in retrievers, the next step is to define checks that evaluate those facts. In the [next post](/blog/setting-up-backstage-tech-insights-checks/), we will install the check engine and write checks against the facts we just set up.

---

The built-in fact retrievers cover the basics that most organisations care about: metadata completeness, ownership, and documentation. Together they produce facts that you can start writing checks against immediately.

Get these running first. Verify the data is flowing via the API. Once you are comfortable with how facts work, we will define checks in the next post. Later in the series ([post #7](/blog/backstage-tech-insights-custom-fact-retrievers/)), we will go beyond catalog metadata and build custom fact retrievers that collect data from external systems like GitHub and CI tools.
