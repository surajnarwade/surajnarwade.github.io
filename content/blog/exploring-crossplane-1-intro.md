+++
title = "Exploring Crossplane #1: Introduction"
author = "Suraj Narwade"
date = "2024-10-28T20:00:46.064Z"
category = "Blog"
series = ["Exploring Crossplane"]
+++


![crossplane.png](/images/crossplane.png)

(Image taken from official crossplane [website](https://docs.crossplane.io/latest/getting-started/introduction/))

Managing infrastructure across multiple cloud providers can be complex and challenging. Enter Crossplane—a powerful, open-source Kubernetes native control plane that allows developers and platform teams to use Kubernetes to manage cloud resources like databases, storage, and networking across multiple cloud providers (AWS, GCP, Azure, etc.) and not limited to this and much more other non-cloud resources as well such as Pagerduty, Github, etc. In this introduction to Crossplane, we’ll explore its core capabilities, why it’s gaining popularity, and where it’s particularly useful.

In this first part of our exploration of Crossplane, we’ll cover the basics: what Crossplane is, why it’s gaining popularity, and how it fits into a Kubernetes-native, multi-cloud strategy.

---

### What is Crossplane?

[Crossplane](https://www.crossplane.io/) is a Kubernetes-native, open-source control plane designed to extend Kubernetes capabilities to manage not only applications but also the cloud infrastructure they rely on. Created by [Upbound](https://www.upbound.io/), Crossplane turns Kubernetes clusters into fully functional control planes that can provision, manage, and observe infrastructure on cloud providers and on-premise environments using standard Kubernetes APIs.

Simply put, it provides a unified API to manage cloud infrastructure in the same way you manage applications within Kubernetes.

Crossplane is a Kubernetes add-on that extends the Kubernetes API to manage cloud infrastructure resources. Created by Upbound, Crossplane enables organizations to use Kubernetes not only for application management but also for provisioning cloud infrastructure.

In short, Crossplane lets you treat infrastructure like applications—using familiar Kubernetes manifests to handle deployment, updates, and scaling.

### Why Use Crossplane?

Crossplane is gaining traction for its ability to bridge the gap between application developers and infrastructure teams, empowering both sides to operate independently yet cohesively. Here are a few reasons why Crossplane is worth considering:

1. **Unified Management**: Crossplane standardizes the way resources are created and managed across multiple cloud providers, eliminating the need for different management tools or interfaces.
2. **Kubernetes-Native API**: With Crossplane, you manage cloud infrastructure the Kubernetes way. Developers familiar with Kubernetes can quickly pick up Crossplane without needing to learn a new set of tools or languages.
3. **Composability**: Crossplane’s concept of “Compositions” allows platform teams to define reusable infrastructure blueprints, called "compositions." Developers can then use these blueprints without worrying about the underlying infrastructure details, enhancing productivity and reducing complexity.
4. **Multi-Cloud Flexibility**: Crossplane enables organizations to adopt a multi-cloud strategy by abstracting cloud resources. This provides the flexibility to deploy resources across various cloud providers or switch between them without drastic changes in infrastructure management.
5. **Separation of Concerns**: By enabling teams to create infrastructure abstractions (blueprints), Crossplane allows infrastructure and development teams to operate independently, reducing bottlenecks and dependency cycles.

### Key Concepts in Crossplane

To fully grasp Crossplane’s functionality, it’s helpful to understand its foundational concepts:

- **Providers**: Providers in Crossplane represent cloud service providers like AWS, Azure, or GCP. These providers can be installed into Kubernetes, enabling Crossplane to provision resources across multiple clouds.
- **Managed Resources**: Crossplane uses CRDs (Custom Resource Definitions) to represent cloud resources, such as `RDSInstance` or `Bucket`. These resources are managed by the providers and can be created, modified, and deleted using Kubernetes manifests.
- **Compositions**: A Composition is a blueprint that defines how a higher-level resource, like a “database,” maps to a specific set of cloud resources (e.g., an RDS instance in AWS). Compositions allow you to define reusable infrastructure patterns that can be instantiated by developers without needing to know the specifics.
- **Crossplane Control Plane**: Crossplane’s control plane runs on a Kubernetes cluster and is responsible for managing all the resources declared in the Kubernetes manifests.

### How Does Crossplane Work?

Crossplane relies on Kubernetes’ extensibility to act as an interface for provisioning cloud infrastructure. When you install Crossplane into a Kubernetes cluster, it adds CRDs that represent cloud resources and infrastructure primitives.

Here’s a quick overview of the workflow with Crossplane:

1. **Define Infrastructure Requirements**: A developer defines the infrastructure requirements for an application, such as a database or a storage bucket, using Kubernetes CRDs.
2. **Crossplane Control Plane**: Crossplane receives these specifications and provisions the necessary infrastructure via its providers.
3. **Composition**: Infrastructure teams can create compositions to abstract away details and provide developers with a simplified, declarative interface for provisioning complex infrastructure stacks.

### Crossplane Use Cases

Crossplane has a range of practical applications, including:

1. **Multi-Cloud Resource Management**: Manage resources across different cloud providers from a single Kubernetes cluster. Crossplane simplifies switching between providers and managing resources in hybrid or multi-cloud environments.
2. **Self-Service Infrastructure for Development Teams**: Platform teams can create reusable infrastructure blueprints (Compositions) that developers can deploy on demand, empowering them to deploy resources without relying on infrastructure teams.
3. **Kubernetes-Native PaaS**: By using Crossplane to handle both applications and infrastructure, organizations can build their own Platform-as-a-Service (PaaS), giving developers an integrated and scalable environment.
4. **Cost Optimization and Flexibility**: Because Crossplane abstracts cloud resources, teams can optimize costs by distributing resources across providers or moving resources to more cost-effective options when needed.

### Getting Started with Crossplane

Before diving into advanced configurations, let’s cover the basics of getting started with Crossplane:

1. **Install Crossplane**: Install Crossplane on a Kubernetes cluster, usually using Helm or YAML manifests.
2. **Install a Provider**: Add cloud providers like AWS, Azure, or GCP by installing Crossplane provider packages.
3. **Provision Resources**: Use CRDs to declare and manage cloud infrastructure as you would any Kubernetes resource.
4. **Use Compositions**: Define reusable compositions and create instances for specific applications or environments.

---

### Wrapping Up

Crossplane is a powerful tool for organizations looking to streamline infrastructure management in a Kubernetes-native way. By using Kubernetes as a single control plane, Crossplane simplifies multi-cloud management, empowers developers, and enables consistent infrastructure patterns. In Part 2, we’ll get hands-on with installing Crossplane, setting up providers, and deploying our first cloud resource using a Kubernetes manifest.

Stay tuned!