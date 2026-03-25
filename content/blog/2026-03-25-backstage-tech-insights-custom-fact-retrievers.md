+++
title = "Backstage Tech Insights #7: Custom Fact Retrievers"
slug = "backstage-tech-insights-custom-fact-retrievers"
description = "How to build a custom fact retriever in Backstage Tech Insights that queries the Kubernetes API to check for org-specific annotations on running deployments."
author = "Suraj Narwade"
date = "2026-03-25"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "custom-fact-retrievers", "kubernetes"]
series = ["Backstage Tech Insights"]
+++

Over the past six posts, we set up Tech Insights end to end using the built-in fact retrievers. These retrievers gave us facts covering metadata completeness, ownership, and TechDocs, and you have seen them in the UI through scorecards and service maturity.

That's a solid foundation. But those facts only tell you about Backstage entity metadata and TechDocs. They inspect catalog metadata such as `metadata.title`, `metadata.description`, `spec.owner`, and annotations. That's all they can see.

They can't tell you whether your Kubernetes deployments have the required annotations, whether the last CI build passed, or whether a service has branch protection enabled.

For that, you need custom fact retrievers.

You need a custom fact retriever when the data comes from somewhere else:

- **Kubernetes**: required annotations on running deployments, resource limits, replica counts
- **GitHub/GitLab**: branch protection, open PR count, CODEOWNERS file presence
- **CI/CD systems**: last build status, deployment frequency
- **Monitoring tools**: alert count, SLO compliance
- **Custom APIs**: anything specific to your organization

Let's build one. We'll create a Kubernetes fact retriever that checks whether running deployments have required org-specific annotations like `myorg.io/cost-center` and `myorg.io/team`.

This is a practical problem. Teams might set everything correctly in `catalog-info.yaml`, but the actual Kubernetes deployment could be missing annotations that your org requires for cost tracking, ownership, or compliance. A custom fact retriever lets you catch that drift.

---

## Step 1: Scaffold the backend module

A custom fact retriever lives as a Tech Insights backend module in the Backstage plugin directory. Backstage provides a scaffolding tool that sets up the boilerplate:

```bash
yarn new
```

Select `backend-plugin-module`, enter `tech-insights` as the plugin ID, and give your module an ID (for example, `kubernetes-annotation-retriever`). Lastly, provide `@backstage-community/plugin-tech-insights-backend` as the package name.

```
➜ yarn new
? What do you want to create? backend-plugin-module - A new backend module that extends an existing backend
 plugin
? Enter the ID of the plugin [required] tech-insights
? Enter the ID of the module [required] kubernetes-annotation-retriever
? Enter the package name of the plugin this module extends (e.g. @backstage/plugin-catalog-backend)
[required] @backstage-community/plugin-tech-insights-backend
```

This creates the module structure under the `plugins/` directory. You should have a directory structure like this:

```
❯ tree plugins
plugins
├── README.md
└── tech-insights-backend-module-kubernetes-annotation-retriever
    ├── package.json
    ├── README.md
    └── src
        ├── index.ts
        └── module.ts
```

This will also update `packages/backend/package.json` to add the new module as a dependency:

```json
"@internal/backstage-plugin-tech-insights-backend-module-kubernetes-annotation-retriever": "workspace:^",
```

---

## Step 2: Define the fact retriever

A fact retriever is a TypeScript object that implements the `FactRetriever` interface. We saw this in an earlier post when we looked at the built-in fact retriever code.

It has four key parts:

- an ID,
- an entity filter that controls which catalog entities it runs against,
- a schema that declares what facts it produces,
- a handler that collects the data.

Two fields to pay attention to:

- **`id`**: Once set, this can't be changed. Checks and the database reference this ID, so changing it would break existing data and check definitions.
- **`version`**: This tells Tech Insights when your schema has changed. Whenever you add, remove, or modify fields in the schema, bump the version. Tech Insights uses this to detect schema changes and update the underlying database columns accordingly. If you change the schema without updating the version, the new fields won't be picked up.

Create a file for the retriever in your new module:

```typescript
// plugins/tech-insights-backend-module-kubernetes-annotation-retriever/src/factRetriever.ts
import {
  FactRetriever,
  FactRetrieverContext,
  TechInsightFact,
} from "@backstage-community/plugin-tech-insights-node";
import { CatalogClient } from "@backstage/catalog-client";

export const kubernetesAnnotationFactRetriever: FactRetriever = {
  id: "kubernetesAnnotationFactRetriever",
  version: "0.1.0",
  entityFilter: [{ kind: "component", "spec.type": "service" }],
  schema: {
    hasCostCenterAnnotation: {
      type: "boolean",
      description:
        "The Kubernetes deployment has the myorg.io/cost-center annotation",
    },
    hasTeamAnnotation: {
      type: "boolean",
      description: "The Kubernetes deployment has the myorg.io/team annotation",
    },
  },
  handler: async ({ auth, discovery, entityFilter }: FactRetrieverContext) => {
    // We'll fill this in next
  },
};
```

The `entityFilter` field controls which catalog entities this retriever processes. Since we're checking Kubernetes deployments, we filter to components of type `service`. You can adjust this to match your catalog structure:

```typescript
// Only process components of type service
entityFilter: [{ kind: 'component', 'spec.type': 'service' }],

// Process all components
entityFilter: [{ kind: 'component' }],
```

The schema follows the same structure we covered in [post #3](/blog/setting-up-backstage-tech-insights-fact-retrievers/). Each fact gets a type (`boolean`, `number`, or `string`) and a description. The difference with custom retrievers is that these facts come from external systems rather than catalog metadata.

---

## Step 3: Write the handler

The handler is where data collection happens. It receives a context object with access to the catalog and auth services. The job is simple: fetch the entities, query the Kubernetes API for each one, and return the facts.

This handler runs periodically based on the cadence you configure for the retriever in Backstage config.

We can use the Backstage Kubernetes backend plugin's proxy to talk to clusters. This means you do not need to manage kubeconfig or credentials in your retriever. The Kubernetes plugin handles cluster discovery and authentication, as long as you have already configured Kubernetes integration in Backstage.

```typescript
  handler: async ({
    discovery,
    entityFilter,
    auth,
  }: FactRetrieverContext) => {
    const { token } = await auth.getPluginRequestToken({
      onBehalfOf: await auth.getOwnServiceCredentials(),
      targetPluginId: 'catalog',
    });
    const catalogClient = new CatalogClient({
      discoveryApi: discovery,
    });
    const { items: entities } = await catalogClient.getEntities(
      {
        filter: entityFilter,
      },
      { token },
    );

    // Get a token for the Kubernetes plugin
    const { token: k8sToken } = await auth.getPluginRequestToken({
      onBehalfOf: await auth.getOwnServiceCredentials(),
      targetPluginId: 'kubernetes',
    });

    const k8sBaseUrl = await discovery.getBaseUrl('kubernetes');

    const facts: TechInsightFact[] = [];


    for (const entity of entities) {
      const namespace = entity.metadata.namespace || 'default';
      const name = entity.metadata.name;
      const kind = entity.kind;

      let hasCostCenterAnnotation = false;
      let hasTeamAnnotation = false;

      try {
        // Use the Backstage Kubernetes proxy to fetch resources for this entity
        const response = await fetch(
          `${k8sBaseUrl}/resources/workloads/query`,
          {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
              Authorization: `Bearer ${k8sToken}`,
            },
            body: JSON.stringify({
              entity: {
                metadata: { namespace, name, annotations: entity.metadata.annotations ?? {} },
                kind,
              },
            }),
          },
        );

        if (response.ok) {
          const data = await response.json();

          // Walk through the cluster responses to find deployments
          for (const cluster of data.items || []) {
            for (const resource of cluster.resources || []) {
              if (resource.type !== 'deployments') continue;

              for (const deployment of resource.resources || []) {
                const annotations = deployment.metadata?.annotations || {};
                if (annotations['myorg.io/cost-center']) {
                  hasCostCenterAnnotation = true;
                }
                if (annotations['myorg.io/team']) {
                  hasTeamAnnotation = true;
                }
              }
            }
          }
        }
      } catch (error) {
        // Network/auth/parsing errors should not crash the whole retriever run.
        logger.warn(
          `Kubernetes lookup failed for ${kind}:${namespace}/${name}`,
          error instanceof Error ? error : undefined,
        );
      }

      facts.push({
        entity: {
          namespace,
          kind,
          name,
        },
        facts: {
          hasCostCenterAnnotation,
          hasTeamAnnotation,
        },
      });
    }

    return facts;
  },
```

A few things to note:

- The handler fetches entities from the catalog using the provided filter
- For each entity, it queries the Backstage Kubernetes plugin's proxy to get the workload resources
- It checks the actual deployment annotations, not the catalog metadata
- If the Kubernetes API is unreachable or the entity has no deployments, facts default to `false`

The Backstage Kubernetes plugin figures out which cluster to query based on the entity's annotations (like `backstage.io/kubernetes-id` or `backstage.io/kubernetes-namespace`). Make sure your entities have these annotations set. You can read more about this in the [Backstage Kubernetes plugin docs](https://backstage.io/docs/features/kubernetes/).

---

## Step 4: Register the module

Wire up your fact retriever in the generated module file:

```typescript
// plugins/tech-insights-backend-module-kubernetes-annotation-retriever/src/module.ts
import { createBackendModule } from "@backstage/backend-plugin-api";
import { techInsightsFactRetrieversExtensionPoint } from "@backstage-community/plugin-tech-insights-node";
import { kubernetesAnnotationFactRetriever } from "./factRetriever";

export const techInsightsModuleKubernetesAnnotationRetriever =
  createBackendModule({
    pluginId: "tech-insights",
    moduleId: "kubernetes-annotation-retriever",
    register(reg) {
      reg.registerInit({
        deps: {
          techInsightsFactRetrievers: techInsightsFactRetrieversExtensionPoint,
        },
        async init({ techInsightsFactRetrievers }) {
          techInsightsFactRetrievers.addFactRetrievers({
            kubernetesAnnotationFactRetriever,
          });
        },
      });
    },
  });
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
      cadence: "*/15 * * * *"
      lifecycle: { timeToLive: { weeks: 2 } }
    entityOwnershipFactRetriever:
      cadence: "*/15 * * * *"
      lifecycle: { timeToLive: { weeks: 2 } }
    techdocsFactRetriever:
      cadence: "*/15 * * * *"
      lifecycle: { timeToLive: { weeks: 2 } }
    # your custom retriever
    kubernetesAnnotationFactRetriever:
      cadence: "0 */6 * * *"
      lifecycle: { timeToLive: { weeks: 2 } }
```

The key here must match the `id` field from your fact retriever definition. Since we're querying the Kubernetes API, a cadence of every 6 hours is a reasonable starting point. Kubernetes annotations don't change that often, and you want to avoid putting unnecessary load on your clusters. Adjust based on how frequently your deployments change.

---

## Step 6: Define checks for the new facts

Now that you have new facts flowing in, define checks against them just like we did in [post #4](/blog/setting-up-backstage-tech-insights-checks/). Here's a full example with `metadata` for service maturity (covered in [post #6](/blog/setting-up-backstage-tech-insights-frontend-service-maturity/)):

```yaml
hasCostCenterAnnotation:
  type: json-rules-engine
  name: Kubernetes Cost Center Annotation
  description: The Kubernetes deployment has the myorg.io/cost-center annotation. This annotation is required for cost tracking and chargeback.
  factIds:
    - kubernetesAnnotationFactRetriever
  rule:
    conditions:
      all:
        - fact: hasCostCenterAnnotation
          operator: equal
          value: true
  metadata:
    category: Compliance
    rank: 2
    solution: Add the myorg.io/cost-center annotation to your Kubernetes deployment manifest. Check the platform team's wiki for the list of valid cost center codes.

hasTeamAnnotation:
  type: json-rules-engine
  name: Kubernetes Team Annotation
  description: The Kubernetes deployment has the myorg.io/team annotation. This annotation is required for ownership tracking at the infrastructure level.
  factIds:
    - kubernetesAnnotationFactRetriever
  rule:
    conditions:
      all:
        - fact: hasTeamAnnotation
          operator: equal
          value: true
  metadata:
    category: Ownership
    rank: 1
    solution: Add the myorg.io/team annotation to your Kubernetes deployment manifest. The value should match your team name in the catalog.
```

These checks will show up in your scorecards ([post #5](/blog/setting-up-backstage-tech-insights-frontend-scorecards/)) and the maturity dashboard ([post #6](/blog/setting-up-backstage-tech-insights-frontend-service-maturity/)) automatically.

---

## What you should see after setup

Once the retriever runs and checks are evaluated:

- Services with the required annotations will pass these checks in scorecards
- Services missing one or both annotations will fail with actionable guidance from the `solution` field
- Teams can quickly spot deployment-level metadata drift between `catalog-info.yaml` and actual cluster resources

---

## Handling API performance

When your fact retriever calls the Kubernetes API, keep these in mind:

- **Set appropriate timeouts**: large catalogs need longer timeouts, and some clusters are slower than others
- **Use entity filters**: narrow the scope to only the entity types that matter. Not every component in your catalog has a Kubernetes deployment
- **Handle missing deployments gracefully**: some entities might not have any workloads deployed yet. That's fine, just return `false` for the facts

---

## Testing your fact retriever

Before deploying, test it locally:

1. **Unit test the handler**: mock the catalog client and Kubernetes API responses, verify the facts are structured correctly
2. **Run locally**: start Backstage locally and check the Tech Insights tab for your new facts
3. **Check the logs**: the Tech Insights backend logs when retrievers run and if they encounter errors
4. **Start with a small entity filter**: test with a specific entity before running against the entire catalog

You can also verify facts via the API before setting up checks:

```bash
curl "http://localhost:7007/api/tech-insights/facts/latest?entity=component:default/my-service&ids[]=kubernetesAnnotationFactRetriever"
```

---

## What's next?

In the [next post](/blog/backstage-tech-insights-troubleshooting-production/), we'll cover troubleshooting common issues and production best practices.

The pattern for building custom fact retrievers is always the same: scaffold a module, define the schema, write a handler, register it, and add checks. Start small and iterate. We picked Kubernetes annotations here, but the same pattern works for any external data source. Once teams see which deployments are missing required annotations, they tend to fix them pretty quickly. The visibility alone drives improvement.

## References

- [Tech Insights documentation](https://backstage.io/docs/features/tech-insights/)
- [FactRetriever interface](https://github.com/backstage/backstage/blob/master/plugins/tech-insights-node/src/facts.ts)
- [Backstage Kubernetes plugin](https://backstage.io/docs/features/kubernetes/)
