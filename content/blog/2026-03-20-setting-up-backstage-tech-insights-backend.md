+++
title = "Backstage Tech Insights #2: Setting Up Tech Insights - Backend"
slug = "setting-up-backstage-tech-insights-backend"
description = "Install and configure the Tech Insights backend plugin in your Backstage instance, including database setup and the module system."
author = "Suraj Narwade"
date = "2026-03-20"
category = "Blog"
tags = ["backstage", "tech-insights", "platform-engineering", "developer-portal"]
series = ["Backstage Tech Insights"]
+++

In the [previous post](/blog/introduction-to-backstage-tech-insights/), we looked at what Tech Insights is and why it is useful. Now let's get it running.

This post walks through the backend installation and configuration of the Tech Insights plugin.

---

## Prerequisites

Before you start, make sure you have:

- [Node.js](https://nodejs.org/en/download) and [Yarn](https://yarnpkg.com/getting-started/install) installed
- A working Backstage instance
- Access to the Backstage codebase

If you do not have a Backstage instance yet, the [official getting started guide](https://backstage.io/docs/getting-started/) is the best place to begin.

---

## Step 1: Install the backend plugin

The Tech Insights backend is responsible for running fact retrievers, storing facts, and evaluating checks.

Install it in your backend package by running following command from Backstage root directory:

```bash
yarn --cwd packages/backend add @backstage-community/plugin-tech-insights-backend
```

---

## Step 2: Configure the backend plugin

In your backend, you need to register the Tech Insights backend plugin. With the new Backstage backend system, add it to your `packages/backend/src/index.ts`:

```typescript
backend.add(import('@backstage-community/plugin-tech-insights-backend'));
```

This registers the core Tech Insights backend, it also consists of built-in fact retrievers but they are not registered yet. In the [next post](/blog/setting-up-backstage-tech-insights-fact-retrievers/), we will see how to register these built-in fact retrievers and configure them in `app-config.yaml`.

---

## Step 3: Configure the database

Tech Insights stores facts in a Backstage database. If your Backstage instance already uses PostgreSQL (which is recommended for production), Unlike any other Backstage plugins, Tech Insights will use the same database connection.

No additional database configuration is needed beyond what Backstage already uses. The plugin creates its own tables automatically on startup.

If you are running SQLite for local development, that works too, but PostgreSQL is strongly recommended for anything beyond local testing.

As per standard Backstage practice where it creates database called `backstage_plugin_<plugin-name>`

For tech-insights, it will be `backstage_plugin_tech-insights` this is important if you want troubleshoot anything.

```
mydb=# \l
                                                           List of databases
              Name              | Owner | Encoding | Locale Provider |  Collate   |   Ctype    | Locale | ICU Rules | Access privileges
--------------------------------+-------+----------+-----------------+------------+------------+--------+-----------+-------------------
 backstage_plugin_app           | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_auth          | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_catalog       | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_kubernetes    | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_notifications | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_permission    | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_proxy         | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_scaffolder    | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_search        | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_signals       | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_tech-insights | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 backstage_plugin_techdocs      | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 mydb                           | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 postgres                       | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           |
 template0                      | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           | =c/root          +
                                |       |          |                 |            |            |        |           | root=CTc/root
 template1                      | root  | UTF8     | libc            | en_US.utf8 | en_US.utf8 |        |           | =c/root          +
                                |       |          |                 |            |            |        |           | root=CTc/root
(16 rows)
```

---

The package we just installed (`plugin-tech-insights-backend`) is the core framework. It handles scheduling, storage, and the API layer. It also has built-in fact retrievers, but they are not registered or activated yet.

By leveraging Backstage modular design, you can also write your own modules which will extend `tech-insights-backend` for custom fact retrieval and check logic, which we will cover later in this series.

---

## What is next?

The backend is now ready. In the [next post](/blog/setting-up-backstage-tech-insights-fact-retrievers/), we will install the built-in fact retrievers and configure it to start collecting data about your entities.
