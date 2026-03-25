+++
title = "Backstage Tech Insights #7: Custom Fact Retrievers"
slug = "backstage-tech-insights-custom-fact-retrievers"
description = "How to build custom fact retrievers in Backstage Tech Insights to collect data from external systems like GitHub, CI tools, and more."
author = "Suraj Narwade"
date = "2026-03-25"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "custom-fact-retrievers"]
series = ["Backstage Tech Insights"]
+++

Over the past six posts, we set up Tech Insights end to end using the three built-in fact retrievers. You have six facts covering metadata completeness, ownership, and TechDocs, and you've seen them in the UI through scorecards and service maturity.

That's a solid foundation. But those six facts only tell you what's in `catalog-info.yaml`. They can't tell you whether branch protection is enabled on a GitHub repository, whether the last CI build passed, or whether a service has a Dockerfile.

For that, you need custom fact retrievers.

---

## When do you need a custom fact retriever?

The built-in retrievers only inspect catalog metadata: `metadata.title`, `metadata.description`, `spec.owner`, and annotations. That's all they can see.

You need a custom fact retriever when the data comes from somewhere else:

- **GitHub/GitLab**: branch protection, open PR count, CODEOWNERS file presence
- **CI/CD systems**: last build status, deployment frequency
- **Monitoring tools**: alert count, SLO compliance, whether Snyk is enabled
- **Package registries**: dependency versions, vulnerability counts
- **Custom APIs**: anything specific to your organization

Let's build one. We'll create a GitHub fact retriever that checks whether repositories have branch protection enabled and a CODEOWNERS file.

---

## Step 1: Scaffold the backend module

A custom fact retriever lives in its own backend module. Backstage provides a scaffolding tool that sets up the boilerplate:

```bash
yarn new
```

Select `backend-plugin-module`, enter `tech-insights-backend` as the plugin ID, and give your module an ID (e.g., `github-retriever`). This creates the module structure under the `plugins/` directory.

This will also update `packages/backend/package.json` to add the new module as a dependency:

```json
"@internal/backstage-plugin-tech-insights-backend-module-github-retriever": "workspace:^",
```

---

## Step 2: Define the fact retriever

A fact retriever is a TypeScript object that implements the `FactRetriever` interface. It has four key parts: an ID, an entity filter that controls which catalog entities it runs against, a schema that declares what facts it produces, and a handler that collects the data.

Two fields to pay attention to:

- **`id`**: Once set, this can't be changed. Checks and the database reference this ID, so changing it would break existing data and check definitions.
- **`version`**: This tells Tech Insights when your schema has changed. Whenever you add, remove, or modify fields in the schema, bump the version. Tech Insights uses this to detect schema changes and update the underlying database columns accordingly. If you change the schema without updating the version, the new fields won't be picked up.

Create a file for the retriever in your new module:

```typescript
// plugins/tech-insights-backend-module-github/src/factRetrievers.ts
import {
  FactRetriever,
  FactRetrieverContext,
} from '@backstage/plugin-tech-insights-node';

export const githubFactRetriever: FactRetriever = {
  id: 'githubFactRetriever',
  version: '0.1.0',
  entityFilter: [{ kind: 'component' }],
  schema: {
    hasBranchProtection: {
      type: 'boolean',
      description: 'Whether the default branch has protection rules enabled',
    },
    hasCodeowners: {
      type: 'boolean',
      description: 'Whether the repository has a CODEOWNERS file',
    },
  },
  handler: async ({ auth, discovery, entityFilter }: FactRetrieverContext) => {
    // We'll fill this in next
  },
};
```

The `entityFilter` field controls which catalog entities this retriever processes. In this case, we only care about components. You can narrow it further:

```typescript
// Only process components of type service
entityFilter: [{ kind: 'component', 'spec.type': 'service' }],

// Process components and APIs
entityFilter: [{ kind: 'component' }, { kind: 'api' }],
```

The schema follows the same structure we covered in [post #3](/blog/setting-up-backstage-tech-insights-fact-retrievers/). Each fact gets a type (`boolean`, `number`, or `string`) and a description. The difference with custom retrievers is that these facts come from external systems rather than catalog metadata.

---

## Step 3: Write the handler

The handler is where data collection happens. It receives a context object with access to the catalog and auth services. The job is simple: fetch the entities, collect data for each one, and return the facts.

```typescript
handler: async ({ auth, discovery, entityFilter }) => {
  // Get the catalog client
  const catalogClient = new CatalogClient({ discoveryApi: discovery });
  const token = await auth.getPluginRequestToken({
    onBehalfOf: await auth.getOwnServiceCredentials(),
    targetPluginId: 'catalog',
  });

  // Fetch entities from the catalog
  const { items: entities } = await catalogClient.getEntities(
    { filter: entityFilter },
    { token: token.token },
  );

  const facts = [];

  for (const entity of entities) {
    const repoUrl = entity.metadata.annotations?.['backstage.io/source-location'];

    if (!repoUrl) {
      continue;
    }

    // Parse the repo URL to get owner and repo name
    const { owner, repo } = parseRepoUrl(repoUrl);

    // Check branch protection via GitHub API
    let hasBranchProtection = false;
    try {
      const response = await fetch(
        `https://api.github.com/repos/${owner}/${repo}/branches/main/protection`,
        { headers: { Authorization: `token ${githubToken}` } },
      );
      hasBranchProtection = response.ok;
    } catch {
      hasBranchProtection = false;
    }

    // Check for CODEOWNERS file
    let hasCodeowners = false;
    try {
      const response = await fetch(
        `https://api.github.com/repos/${owner}/${repo}/contents/CODEOWNERS`,
        { headers: { Authorization: `token ${githubToken}` } },
      );
      hasCodeowners = response.ok;
    } catch {
      hasCodeowners = false;
    }

    facts.push({
      entity: {
        namespace: entity.metadata.namespace || 'default',
        kind: entity.kind,
        name: entity.metadata.name,
      },
      facts: {
        hasBranchProtection,
        hasCodeowners,
      },
    });
  }

  return { facts };
},
```

A few things to note:

- The handler fetches entities from the catalog using the provided filter
- For each entity, it makes API calls to GitHub to collect the data
- It returns an array of facts, one per entity
- Error handling matters here since external APIs can fail or be rate-limited

---

## Step 4: Register the module

Wire up your fact retriever in the generated module file:

```typescript
// plugins/tech-insights-backend-module-github/src/module.ts
import { techInsightsFactRetrieversExtensionPoint } from '@backstage-community/plugin-tech-insights-node';
import { myFactRetriever } from './myFactRetriever';

export const techInsightsModuleMyFactRetriever = createBackendModule({
  pluginId: 'tech-insights',
  moduleId: 'my-fact-retriever',
  register(reg) {
    reg.registerInit({
      deps: {
        providers: techInsightsFactRetrieversExtensionPoint,
      },
      async init({ providers }) {
        providers.addFactRetrievers({
          myFactRetriever,
        });
      },
    });
  },
});
```

Then add the module to your backend:

```typescript
// packages/backend/src/index.ts
backend.add(import('./plugins/tech-insights-backend-module-github'));
```

---

## Step 5: Register the fact retriever in app-config.yaml

Just like the built-in retrievers we configured in [post #3](/blog/setting-up-backstage-tech-insights-fact-retrievers/), custom fact retrievers also need to be registered in `app-config.yaml` with a cadence and lifecycle. Without this, the backend won't know when to run your retriever or how long to keep the data.

Add your retriever under `techInsights.factRetrievers`:

```yaml
techInsights:
  factRetrievers:
    # existing built-in retrievers
    entityMetadataFactRetriever:
      cadence: '*/15 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
    entityOwnershipFactRetriever:
      cadence: '*/15 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
    techdocsFactRetriever:
      cadence: '*/15 * * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
    # your custom retriever
    githubFactRetriever:
      cadence: '0 */6 * * *'
      lifecycle: { timeToLive: { weeks: 2 } }
```

The key here must match the `id` field from your fact retriever definition. Since we're calling an external API, a cadence of every 6 hours is a reasonable starting point. You can always adjust this based on how frequently the data changes and your API rate limits.

---

## Step 6: Define checks for the new facts

Now that you have new facts flowing in, define checks against them just like we did in [post #4](/blog/setting-up-backstage-tech-insights-checks/). Here's a full example with `metadata` for service maturity (covered in [post #6](/blog/setting-up-backstage-tech-insights-frontend-service-maturity/)) and `links` pointing to relevant documentation:

```yaml
hasBranchProtection:
  type: json-rules-engine
  name: Branch Protection Enabled
  description: The default branch has protection rules enabled. Branch protection prevents direct pushes and enforces review requirements.
  factIds:
    - githubFactRetriever
  rule:
    conditions:
      all:
        - fact: hasBranchProtection
          operator: equal
          value: true
  metadata:
    category: Security
    rank: 2
    solution: Go to your GitHub repository settings, navigate to Branches, and add a branch protection rule for the default branch. At minimum, enable "Require a pull request before merging".
  links:
    - title: GitHub Branch Protection Docs
      url: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-a-branch-protection-rule
```

This check will show up in your scorecards ([post #5](/blog/setting-up-backstage-tech-insights-frontend-scorecards/)) and the maturity dashboard ([post #6](/blog/setting-up-backstage-tech-insights-frontend-service-maturity/)) automatically.

---

## Handling rate limits and performance

When your fact retriever calls external APIs, keep these in mind:

- **Batch requests where possible**: use GraphQL APIs or bulk endpoints instead of one call per entity
- **Add delays between requests**: a simple `await sleep(100)` can avoid hitting rate limits
- **Use conditional requests**: GitHub supports `If-None-Match` headers to skip unchanged data
- **Set appropriate timeouts**: large catalogs need longer timeouts
- **Use entity filters**: narrow the scope to only the entity types that matter

We cover performance tuning in more depth in [post #8](/blog/backstage-tech-insights-troubleshooting-production/), including batching patterns, staggering retriever schedules, and production monitoring.

---

## Testing your fact retriever

Before deploying, test it locally:

1. **Unit test the handler**: mock the catalog client and external APIs, verify the facts are structured correctly
2. **Run locally**: start Backstage locally and check the Tech Insights tab for your new facts
3. **Check the logs**: the Tech Insights backend logs when retrievers run and if they encounter errors
4. **Start with a small entity filter**: test with a specific entity before running against the entire catalog

---

## What's next?

In the [next post](/blog/backstage-tech-insights-troubleshooting-production/), we'll cover troubleshooting common issues and production best practices.

The pattern for building custom fact retrievers is always the same: scaffold a module, define the schema, write a handler, register it, and add checks. Start small and iterate. The visibility alone tends to drive improvement.

## References

- [Tech Insights documentation](https://backstage.io/docs/features/tech-insights/)
- [FactRetriever interface](https://github.com/backstage/backstage/blob/master/plugins/tech-insights-node/src/facts.ts)