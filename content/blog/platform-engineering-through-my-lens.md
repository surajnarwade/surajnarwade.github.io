+++
title = "Platform Engineering through my lens"
author = "Suraj Narwade"
date = "2026-09-26"
category = "Blog"
+++


I stepped into Platform Engineering in 2019, before the term had the hype and buzz it has today.

Before that, I was a software engineer working in the Kubernetes space, mostly building CLI tools. I genuinely loved it. Writing Go, shaping developer workflows, making day-to-day tasks simpler, that part was fun.

But something kept bothering me.

Most of my work was focused on local clusters or limited environments. I wanted to keep doing what I enjoyed (building tools), but also learn what happens when Kubernetes meets real production: infrastructure, reliability, deployments at scale, operational pain, and all the invisible decisions that make systems stable.

So I moved into a Platform Engineering role at uSwitch in 2019.

And yes, I still wrote Go code for CLI tools. But now I also wrote controllers/operators, scripts, automation, and I started learning the infrastructure side properly. That mix, product-thinking plus engineering plus ops reality, is what pulled me in.

Enough about me. Let’s talk about what Platform Engineering actually is.

---

## What is Platform Engineering?

Platform Engineering is the discipline of building and operating an internal platform that helps developers ship software faster and safer.

The key idea is simple:

> Internal developers are your customers.
> 

A platform team builds things that internal developers use to build, deploy, run, and observe their services without needing to become experts in every part of infrastructure.

Also, “platform” does not mean the same thing everywhere.

In some companies it is heavily Kubernetes-focused. In others it is more about cloud primitives, security, developer experience, or paved paths. The definition changes based on what the organisation needs.

---

## Platform Engineering vs DevOps vs SRE (quick difference)

These three are often mixed up, so here is the simple version.

### DevOps

DevOps is mostly about culture and practices that help teams build and ship software continuously.

- Developers and ops work closely (often as one team)
- Emphasis on automation, CI/CD, and shorter feedback loops
- The goal is faster delivery with shared ownership

### SRE

SRE is a reliability-focused discipline.

- Uses engineering to solve operations problems
- Focuses on availability, latency, error rates, and reliability targets (SLOs/SLIs)
- Strong incident management and learning from failures

### Platform Engineering

Platform Engineering focuses on building internal products (platforms) that make it easier and safer for teams to deliver.

- Provides paved roads and safe defaults
- Reduces cognitive load for application teams
- Builds reusable capabilities like templates, tooling, self-service, and observability

A simple way to remember it:

- **DevOps** is how teams work.
- **SRE** is how reliability is ensured.
- **Platform Engineering** is what is built to enable teams.

---

## What can a platform include?

A platform can include a combination of the following.

### 1) The infrastructure layer

This is where services ultimately run. For example:

- Kubernetes (EKS / self-managed clusters)
- EC2
- ECS
- Networking, DNS, load balancers
- IAM, secrets management, certificates

Not every platform team owns all of this, but the platform is always built on top of something.

### 2) Capabilities that abstract the infrastructure

This is where platform engineering becomes real for developers.

Instead of every team reinventing the same deployment patterns, the platform team provides safe defaults and reusable building blocks, such as:

- **Golden path templates** (service skeletons with the right defaults)
- **Helm charts** with sensible standards baked in
- **Self-serve creation** (new service, new namespace, new environment, new pipeline)
- Internal tooling that reduces repeated manual steps

The goal is not more abstraction. The goal is less cognitive load and fewer sharp edges.

### 3) Interfaces to interact with the platform

A platform is only useful if people can actually use it.

Interfaces might include:

- Internal CLI tools (my personal favourite)
- Dashboards
- Portals / consoles
- Documentation that is actually usable

### 4) Observability as a platform

Observability is often a platform within the platform:

- Logging
- Metrics
- Tracing
- Dashboards and alerts
- Standard instrumentation patterns

If developers can’t see what their service is doing in production, everything becomes slower and riskier.

---

## A big part of the job: supporting internal developers

Platform teams don’t just build a thing and disappear.

Support is a major part of the responsibility because internal developers are the users of your platform. That support can show up in different ways:

- A Slack channel where teams ask questions, troubleshoot issues, or get guidance
- On-call support (depending on how the company operates)
- Helping during incidents
- Helping during out-of-hours incidents or maintenance windows (depends on company setup)

And this support is not a distraction from the job.

It is the job.

Because every support question is signal:

- something confusing
- something too manual
- something missing
- something that needs a better paved road

---

## Platform Engineering isn’t “a Kubernetes job”

Kubernetes is often involved, but Platform Engineering is not Kubernetes engineering.

It’s a discipline focused on:

- making delivery easier
- making production safer
- standardising the common paths
- enabling teams to move fast without breaking everything

You might use Kubernetes.
You might use ECS.
You might use something else.

The platform is defined by what your developers need, not by the trendiest tooling.

---

## Scaling up: Foundation Engineering and specialised platform teams

As companies grow, a single platform team often becomes too broad.

Depending on size, budget, and complexity, many organisations create what’s often called **Foundation Engineering**. I have seen this term used in both my previous and current companies, and across the industry.

Foundation Engineering can include multiple focused teams, for example:

- **Core Platform**
    
    The base runtime, networking patterns, cluster/platform primitives, core tooling.
    
- **Developer Experience (DevEx)**
    
    Golden paths, templates, internal portals, CLIs, documentation, reducing friction.
    
- **Stateful Platform**
    
    Databases, queues, caches, storage patterns, backups, operational tooling.
    
- **Data Platform**
    
    Data pipelines, governance, tooling for analytics/ML use cases.
    
- **SRE / Reliability**
    
    SLOs, incident practices, resilience, operational readiness, reliability tooling.
    

The naming varies, but the pattern is common: split the platform problem into focused areas so teams can go deeper and move faster.

---

## A few modern examples I’m seeing now

At my current workplace, the shape of “platform” includes a lot of what I’ve described above, and modern platform work often leans into:

- **Golden paths** that guide teams to the recommended way to build and run services
- **Self-service** provisioning (less ticket-driven work)
- **Consistent observability** so teams can debug confidently
- **Crossplane-style approaches** where infrastructure can be offered as products and APIs (instead of raw cloud resources)

In practice, this tends to look like developers getting an experience closer to:

> “I want a service with X. Give me the pipeline, runtime config, dashboards, and safe defaults.”
> 

…instead of:

> “Here’s a pile of YAML and cloud permissions. Good luck.”
> 

---

## What I’ve learned (so far)

Platform Engineering taught me that the hard part isn’t Kubernetes, Terraform, or writing controllers.

The hard part is building something that:

- is simple to use
- is secure by default
- scales across many teams
- doesn’t slow delivery
- reduces operational pain over time

And most importantly:

> A platform is successful when it becomes boring.
> 

Boring means:

- fewer repeated questions
- fewer manual steps
- fewer “snowflake” services
- fewer incidents caused by inconsistent patterns
- faster delivery with less stress

---

## Closing

I entered Platform Engineering because I wanted to keep building developer tooling, but also understand production infrastructure properly.

That decision gave me a wider lens: not just “how do we build this tool?” but also “how does this help teams ship reliably at scale?”

If you’re curious about Platform Engineering, start with this:

- internal developers are your customers
- paved roads beat custom adventures
- the platform is a product, not a side project

And if you’re already in the space, you already know the real work begins after the first version ships.