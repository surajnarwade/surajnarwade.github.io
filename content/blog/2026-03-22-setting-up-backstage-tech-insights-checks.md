+++
title = "Backstage Tech Insights #4: Setting Up Checks"
slug = "setting-up-backstage-tech-insights-checks"
description = "How to define checks in Backstage Tech Insights using the JSON rules engine to evaluate facts from the built-in retrievers."
author = "Suraj Narwade"
date = "2026-03-22"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "checks"]
series = ["Backstage Tech Insights"]
+++

In the [previous post](/blog/setting-up-backstage-tech-insights-fact-retrievers/), we set up the built-in fact retrievers and verified that facts are flowing. We now have acts being collected for every entity in our catalog.

Facts on their own are useful, but they become much more valuable when you evaluate them. Checks do exactly that.

A check takes one or more facts and evaluates them against a condition. The result is a simple pass or fail.

---

## Installing the check engine

To evaluate facts, we need a check engine. The Tech Insights plugin provides a backend module called [plugin-tech-insights-backend-module-jsonfc](https://github.com/backstage/community-plugins/tree/main/workspaces/tech-insights/plugins/tech-insights-backend-module-jsonfc). The name **jsonfc** stands for **JSON Fact Checker**. Under the hood, it uses [`json-rules-engine`](https://github.com/CacheControl/json-rules-engine), a lightweight library for evaluating rules defined as JSON objects. This means you can define all your checks as JSON-based rules directly in `app-config.yaml` without writing any custom code.

Install it from your Backstage root directory:

```bash
yarn --cwd packages/backend add @backstage-community/plugin-tech-insights-backend-module-jsonfc
```

Then register it in your backend (`packages/backend/src/index.ts`):

```typescript
backend.add(import('@backstage-community/plugin-tech-insights-backend-module-jsonfc'));
```

You can confirm that it's set up via logs:

```
2026-03-24T17:43:34.455Z tech-insights info Fact checker configured. Enabling fact checking endpoints.
```

---

## Anatomy of a check

Checks are defined under the `techInsights.factChecker.checks` key in `app-config.yaml`. Here's a single check with every field explained:

```yaml
techInsights:
  factChecker:
    checks:
      groupOwnerCheck:
        type: json-rules-engine
        name: Group Owner Check
        description: Verifies that a group has been set as the spec.owner for this entity
        factIds:
          - entityOwnershipFactRetriever
        rule:
          conditions:
            all:
              - fact: hasGroupOwner
                operator: equal
                value: true
```

> Note: The `type` field in each check is set to `json-rules-engine` because as discussed earlier, we will be using `json-rules-engine`. This is the default engine that ships with the plugin. You can also bring your own check engine by implementing the `FactCheckerFactory` interface, but for most use cases the built-in engine is all you need.

Let's look at Check now:

| Field | Required | Description |
|-------|----------|-------------|
| **`groupOwnerCheck`** (key) | Yes | A unique identifier for this check. You'll reference this in scorecards later. |
| **`type`** | Yes | The check engine to use. Set to `json-rules-engine` when using the built-in jsonfc module. If you bring your own engine, this value will be different. |
| **`name`** | Yes | Human-readable name displayed in the UI. |
| **`description`** | Yes | Explains what the check verifies. Teams see this when a check fails, so make it actionable. |
| **`factIds`** | Yes | The list of `ids` of the fact retrievers that provide the facts for this check. Must match exactly. |
| **`rule.conditions`** | Yes | The evaluation logic. Contains `all` (AND) or `any` (OR) conditions. |

Each condition within `rule.conditions` has three fields:

| Field | Description |
|-------|-------------|
| **`fact`** | The fact name from the retriever's schema. Must match the schema key exactly (case-sensitive). |
| **`operator`** | The comparison operator (see table below). |
| **`value`** | The expected value to compare against. |

---

## Operators available in the JSON rules engine

| Operator | Description | Example |
|----------|-------------|---------|
| `equal` | Exact match | `value: true` |
| `notEqual` | Not equal | `value: false` |
| `greaterThan` | Greater than | `value: 0` |
| `greaterThanInclusive` | Greater than or equal | `value: 1` |
| `lessThan` | Less than | `value: 100` |
| `lessThanInclusive` | Less than or equal | `value: 50` |
| `in` | Value in a list | `value: ["a", "b"]` |
| `notIn` | Value not in a list | `value: ["x", "y"]` |
| `contains` | Array contains value | `value: "item"` |
| `doesNotContain` | Array does not contain | `value: "item"` |

All six built-in facts are booleans, so `equal` and `notEqual` are the operators you'll use most. The numeric and list operators become useful when you build custom fact retrievers with numeric facts later in [post #7](/blog/backstage-tech-insights-custom-fact-retrievers/).

You can find the full list of operators in the [json-rules-engine docs](https://github.com/CacheControl/json-rules-engine/blob/master/docs/rules.md).

### Custom operators

The built-in operators cover most cases, but you can also define your own. The `JsonRulesEngineFactCheckerFactory` accepts an `operators` array where you can register custom operators using `json-rules-engine`'s `Operator` class.

For example, if you want a `startsWith` operator:

```typescript
import { Operator } from 'json-rules-engine';

const myFactCheckerFactory = new JsonRulesEngineFactCheckerFactory({
  checks: [],
  logger: env.logger,
  operators: [new Operator('startsWith', (a, b) => a.startsWith(b))],
});
```

Then use it in a check like any other operator:

```yaml
rule:
  conditions:
    any:
      - fact: version
        operator: startsWith
        value: '12'
```

This is useful when the built-in operators don't cover your comparison logic. You can add as many custom operators as you need.

---

## Filtering checks by entity

Not every check makes sense for every entity. You might want ownership checks to only apply to production components, or documentation checks to only apply to APIs. Add a `filter` property to your check configuration. You can use dot notation to access nested entity fields.

> Note: If you have custom fact retriever, you can control the filtering in the fact retriever itself.

> Fun fact: I have personally faced filtering issue myself hence I contributed the check filtering functionality to this plugin. You can find the PR [here](https://github.com/backstage/community-plugins/pull/6697).

```yaml
groupOwnerCheck:
  type: json-rules-engine
  name: Group Owner Check
  description: Verifies that a group has been set as the spec.owner for this entity.
  factIds:
    - entityOwnershipFactRetriever
  filter:
    kind: component
    spec.lifecycle: production
  rule:
    conditions:
      all:
        - fact: hasGroupOwner
          operator: equal
          value: true
```

This check only runs on entities where `kind` is "component" **and** `spec.lifecycle` is "production". When you specify multiple fields in a single filter object, all conditions must match (AND logic).

You can also provide an array of filter objects for OR logic, or an array of values for a single field. Filters work with entity array properties too:

```yaml
# OR logic: match APIs or production components
filter:
  - kind: api
  - kind: component
    spec.lifecycle: production

# Multiple values for a single field
filter:
  kind: [component, api, system]

# Match by tag
filter:
  metadata.tags: backend
```

> **Note**: If the catalog service is unavailable or entity fetching fails, checks with filters will run against all entities as a fallback.

---

## Complete checks configuration

Here's the full `app-config.yaml` configuration bringing together the fact retrievers from the previous post and the checks. Notice how every check description tells the team **what to do** to fix it, not just what the check verifies. Teams should never see a failing check and wonder "now what?".

You can combine multiple conditions within a single check using `all` (AND) or `any` (OR). Just swap `all` for `any` in the conditions block. While `factIds` is a list, a check can reference multiple retrievers if its conditions use facts from different sources. If you need to evaluate facts from different retrievers independently, create separate checks and group them in a scorecard (covered in the [next post](/blog/setting-up-backstage-tech-insights-frontend-scorecards/)).

```yaml
techInsights:
  factRetrievers:
    entityMetadataFactRetriever:
      cadence: '0 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
    entityOwnershipFactRetriever:
      cadence: '20 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
    techdocsFactRetriever:
      cadence: '40 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }

  factChecker:
    checks:
      hasTitle:
        type: json-rules-engine
        name: Has Title
        description: Entity has a title in metadata. Add a title field to your catalog-info.yaml.
        factIds:
          - entityMetadataFactRetriever
        filter:
          - kind: component
            spec.lifecycle: production
        rule:
          conditions:
            all:
              - fact: hasTitle
                operator: equal
                value: true
      hasOwner:
        type: json-rules-engine
        name: Has Owner
        description: Entity has an owner defined in spec.owner. Every entity should have a clear owner.
        factIds:
          - entityOwnershipFactRetriever
        rule:
          conditions:
            all:
              - fact: hasOwner
                operator: equal
                value: true
      techDocsConfigured:
        type: json-rules-engine
        name: TechDocs Configured
        description: Entity has the backstage.io/techdocs-ref annotation. Add it to enable TechDocs.
        factIds:
          - techdocsFactRetriever
        rule:
          conditions:
            all:
              - fact: hasAnnotationBackstageIoTechdocsRef
                operator: equal
                value: true
```

This shows one check per retriever. You can follow the same pattern to add checks for `hasDescription`, `hasTags`, and `hasGroupOwner`.

---

## Checks don't run on their own

One thing that's easy to miss: defining checks in `app-config.yaml` only **registers** them. They don't evaluate automatically on a schedule like fact retrievers do. Checks are evaluated on demand, behind a `POST` API call.

If you look at the [router source code](https://github.com/backstage/community-plugins/blob/main/workspaces/tech-insights/plugins/tech-insights-backend/src/service/router.ts), you'll see that listing checks is a `GET`, but running them is a `POST`:

- `GET /checks` lists all registered checks
- `POST /checks/run/:namespace/:kind/:name` runs checks for a single entity
- `POST /checks/run` runs checks in bulk across multiple entities

So who triggers these POST calls? The frontend does. When you open an entity page with a scorecard or the service maturity plugin installed, it makes these POST calls behind the scenes to evaluate the checks and display results. We'll set that up in the [next post](/blog/setting-up-backstage-tech-insights-frontend-scorecards/).

But you don't need the frontend to test your checks. You can trigger them directly with `curl`. It's also worth understanding these API endpoints because they're the same ones you'd use if you want to build your own integrations, like a CI pipeline that evaluates checks before a deployment, or a Slack bot that reports check results on demand.

## Verifying checks with the API

### List all registered checks

First, confirm your checks are registered:

```bash
curl http://localhost:7007/api/tech-insights/checks
```

You should see a response listing all the checks you defined:

```json
[
  {
    "id": "hasTitle",
    "name": "Has Title",
    "description": "Entity has a title in metadata. Add a title field to your catalog-info.yaml.",
    "factIds": [
      "entityMetadataFactRetriever"
    ],
    "type": "json-rules-engine",
    "rule": {
      "conditions": {
        "all": [
          {
            "fact": "hasTitle",
            "operator": "equal",
            "priority": null,
            "value": true
          }
        ]
      },
      "name": "hasTitle"
    }
  },
  ...
]
```

This is just the list of registered checks. No evaluation has happened yet.

### Run checks for a specific entity

Now trigger the checks with a POST call:

```bash
curl -X POST http://localhost:7007/api/tech-insights/checks/run/default/component/my-service \
  -H "Content-Type: application/json" \
  -d '{}'
```

This evaluates all registered checks against `my-service` and returns the results:

```json
[
  {
    "facts": {
      "hasTitle": {
        "value": true,
        "type": "boolean",
        "description": "The entity has a title in metadata"
      }
    },
    "result": true,
    "check": {
      "id": "hasTitle",
      "name": "Has Title",
      ...
    }
  },
  ...
]
```

Each object in the response contains the `facts` that were evaluated, the `result` (true/false), and the full `check` definition. From the results, you can quickly see which checks passed and which failed for that entity.

### Run specific checks only

You can also pass a list of check IDs in the request body to run only specific checks:

```bash
curl -X POST http://localhost:7007/api/tech-insights/checks/run/default/component/my-service \
  -H "Content-Type: application/json" \
  -d '{"checks": ["hasOwner", "hasGroupOwner"]}'
```

### Troubleshooting

If you see an empty response or errors, check the following:

- The retriever IDs in `factIds` match the fact retriever's `id` exactly (case-sensitive)
- The fact names in your conditions match the schema keys in the retriever
- The fact retrievers have run at least once (check if facts exist using the facts API from [post #3](/blog/setting-up-backstage-tech-insights-fact-retrievers/))

---

## Tips for defining good checks

1. **Start with what you have.** The six built-in facts cover ownership, metadata, and documentation. That's enough to get started and demonstrate value.

2. **Make check descriptions actionable.** Every description should tell the team what to do, not just what failed. "Add a title field to your catalog-info.yaml" is better than "Entity is missing a title".

3. **Avoid checks that always fail.** If 90% of entities fail a check, it becomes noise. Either fix the underlying data first or start with checks where at least some entities pass.

---

## What's next?

We now have facts being collected and checks evaluating them. But right now these results are only visible through the API. In the [next post](/blog/setting-up-backstage-tech-insights-frontend-scorecards/), we'll set up scorecards to group checks and surface them in the Backstage UI.

## References

- [json-rules-engine documentation](https://github.com/CacheControl/json-rules-engine/blob/master/docs/rules.md)
- [tech-insights-backend-module-jsonfc on GitHub](https://github.com/backstage/community-plugins/tree/main/workspaces/tech-insights/plugins/tech-insights-backend-module-jsonfc)
