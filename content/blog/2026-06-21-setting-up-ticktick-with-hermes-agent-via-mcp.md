+++
title = "Setting Up TickTick with Hermes Agent via MCP"
slug = "setting-up-ticktick-with-hermes-agent-via-mcp"
description = "Connect your TickTick tasks to Nous Research's Hermes Agent over MCP so your local AI agent can read, create, and manage your to-do list."
author = "Suraj Narwade"
date = "2026-06-21"
category = "Blog"
tags = ["mcp", "hermes-agent", "ticktick", "ai", "automation", "productivity"]
+++

I live in TickTick. Tasks, reminders, the weekly review, all of it. I also run [Hermes Agent](https://hermes-agent.nousresearch.com/) on my machine as an always-on assistant. For a while these two ran in separate worlds. I'd ask Hermes to plan my day, then manually copy things into TickTick like it was 2015.

Both speak MCP now, so that copy-paste step is gone. In this post I'll connect TickTick to Hermes Agent over MCP. Once it's wired up, you can just say "add a task to buy groceries tomorrow at 6pm" and Hermes drops it into TickTick for you.

## A Quick Word on the Pieces

Hermes Agent is an open-source, self-improving AI agent from Nous Research. It runs on your own computer or server, remembers what it learns, and ships with a built-in MCP client. That client discovers tools from external MCP servers at startup and makes them available to the agent.

MCP, the Model Context Protocol, is the open standard that lets AI clients talk to external tools and services. Think of it as a common plug. Any service that exposes an MCP server can be used by any MCP-aware agent without custom glue code.

TickTick ships an official MCP server. It exposes your tasks, lists, habits, focus records, and countdowns so an agent can read and manage them directly. That's the piece we point Hermes at.

## Prerequisites

- Hermes Agent installed and running (v0.2.0 or newer, which is where the MCP client landed)
- A TickTick account. The MCP server is available on Free, Pro, Max, Team, and Enterprise plans. Free accounts are limited to one custom connector, which is fine for this.
- TickTick's MCP endpoint: `https://mcp.ticktick.com`

## How TickTick's MCP Server Authenticates

The TickTick MCP server uses the Streamable HTTP transport. It supports OAuth and a Bearer Token. I went with the Bearer Token: you generate it once in TickTick, drop it in an env file, and Hermes sends it on every request. No browser login flow to babysit.

## Step 1: Generate a TickTick API Token

First, generate the token. In TickTick:

1. Open **Accounts**.
2. Go to **API Keys**.
3. Click **Create API Key**.
4. Copy the token. TickTick shows it once, so grab it now. If you lose it, create a new one and the old one stops working.

Keep this token handy for the next step. Treat it like a password: anyone with it can read and change your tasks.

## Step 2: Store the Token in Hermes

Don't paste the token straight into `config.yaml`. Hermes reads environment variables from `~/.hermes/.env`, so put it there and reference it from the config later. That keeps the secret out of a file you might share or commit.

Add the token to `~/.hermes/.env`:

```bash
MCP_TICKTICK_API_KEY=your-token-here
```

Hermes loads `.env` on startup, so the variable is available when it connects to TickTick.

## Step 3: Add TickTick to the Hermes Config

Hermes keeps its config at `~/.hermes/config.yaml`. MCP servers live under the `mcp_servers` section. Open the file and add a TickTick entry:

```yaml
mcp_servers:
  ticktick:
    url: https://mcp.ticktick.com
    headers:
      Authorization: Bearer ${MCP_TICKTICK_API_KEY}
    timeout: 120
    connect_timeout: 60
```

The `Authorization` header pulls the token from the env var you just set, so the secret never lives in this file. The `timeout` and `connect_timeout` values (in seconds) give the server some breathing room. TickTick's MCP endpoint can be a little slow to respond on the first call, and the defaults are tight enough that you may see the odd timeout without them. Bump them if you still hit one.

## Step 4: Reload and Verify

If Hermes is already running, pick up the new server without a full restart:

```
/reload-mcp
```

Then check that the TickTick tools registered. The interactive catalog and config view help here:

```bash
hermes mcp
```

You should see `ticktick` listed with its tools discovered. If it isn't showing up, double check the indentation in `config.yaml` (YAML is picky about spaces) and that the token in `.env` is correct.

## Step 5: Take It for a Spin

Now talk to Hermes like you'd talk to an assistant who can see your task list:

- "What's on my TickTick list for today?"
- "Add a task to call the dentist tomorrow morning."
- "Mark the grocery shopping task as done."
- "Move everything tagged work into next week."

Hermes figures out which TickTick tool to call and does it. The task shows up in your TickTick app on every device, because it's writing to your real account, not a local copy.

## Wrapping Up

That's the full loop. TickTick exposes your tasks over its official MCP server, Hermes connects to it with a few lines in `config.yaml`, and now your agent manages your to-do list in plain language. The same pattern works for any other MCP server you want to plug in, so once TickTick is running you can add your calendar, your notes, and your repos the same way and let Hermes tie them together.
