
---
title: "End of Forced Scale: The End of NLB as an Intermediary between API Gateway and ALB"
datePublished: 2025-11-21
slug: end-of-forced-scale-the-end-of-nlb-as-an-intermediary-between-api-gateway-and-alb
tags: aws, api-gateway, alb, nlb, architecture, cloud
---

[Leia em Português 🇧🇷](./pt-BR.md)

![Architecture diagram showing the simplification of API Gateway integration with ALB](./img/20251121-fim-da-escala-forcada-o-fim-do-nlb-como-intermediario-entre-api-gateway-e-alb.png)

For years, one of the small "headaches" in AWS solution architecture was the need to expose internal services, balanced by a private Application Load Balancer (ALB), through API Gateway. The solution, while functional, always felt like a workaround: it was mandatory to place a Network Load Balancer (NLB) as an intermediary.

`API Gateway -> VPC Link -> NLB -> ALB -> Microservices`

This NLB existed for a single reason: API Gateway's VPC Link historically only integrated with it. It worked, but added an infrastructure layer that, in most scenarios, was just a traffic forwarder, bringing costs, latency, and operational complexity.

Recently, AWS removed this barrier. In an announcement celebrated by cloud architects everywhere, **API Gateway REST now supports direct private integration with Application Load Balancer via VPC Link.**

This represents a fundamental change in how we design network topologies for private APIs.

## Technical Analysis: The Impact of Removing the NLB

To understand the relevance of this update, let's break down the three main direct advantages.

### 1. Simplified Topology and Reduced Management Surface

Every component in an architecture is a point of configuration, monitoring, and potential failure. The intermediary NLB required its own Target Group, Health Checks, logs, and CloudWatch metrics. While simple, it was one more element in the chain.

The new architecture is linear and intuitively cleaner:

`API Gateway -> VPC Link -> ALB -> Microservices`

This not only simplifies the solution diagram but also centralizes advanced routing logic (based on path, host, headers) directly in the ALB, which is the right tool for the job, without the need for additional NLB configurations.

### 2. Cost and Latency Reduction

Let's be direct: the NLB, like any managed service, has a cost. It consists of an hourly fee and a data processing fee (LCU - Load Balancer Capacity Unit). For high-traffic APIs, this cost was not negligible.

Besides the financial cost, there's the performance cost. Each network hop introduces latency. Removing the NLB eliminates a hop, resulting in a small but measurable reduction in end-to-end response time. In systems that depend on low latency, every millisecond counts.

### 3. Flexibility in Microservices Architecture

The ALB is a much richer tool in routing features than the NLB. It operates at layer 7 (application), allowing complex rules. By connecting API Gateway directly to it, we gain agility.

Imagine a scenario with multiple services (e.g., `/users`, `/orders`, `/products`) running in different ECS or EKS clusters, all served by the same ALB. With direct integration, API Gateway can simply forward all traffic to the ALB, which then uses its internal rules to route the request to the correct service. The logic is consolidated where it should be.

## Migration Considerations: Is It Time to Turn Off My NLB?

As with any engineering decision, the answer is: **it depends on the context.**

*   **Ideal Migration Scenario:** You have an architecture where the NLB serves exclusively as an intermediary for one or more ALBs. In this case, migration is highly recommended and the gain is clear and immediate.

*   **Scenarios Requiring Caution:** If your NLB serves other purposes, the decision is more complex. For example, if you rely on the static IP addresses an NLB can provide for integrations with legacy systems, or if other services (non-HTTP) consume the NLB directly, it still has its place in the architecture. In these cases, migration may be partial or simply not worth the effort.

## Conclusion: A Natural and Welcome Evolution

The direct integration between API Gateway and private ALB is not just a new feature. It is the correction of an old architectural friction, showing that AWS remains attentive to the practical needs of the developer community.

It's a step towards simpler, more efficient, and cost-effective architectures—three pillars that all of us, engineers and architects, constantly seek.

---
**Official Reference:** [AWS Compute Blog - Build scalable and resilient REST APIs using Amazon API Gateway private integration with Application Load Balancer](https://aws.amazon.com/blogs/compute/build-scalable-rest-apis-using-amazon-api-gateway-private-integration-with-application-load-balancer/)

[Read in Portuguese 🇧🇷](./pt-BR.md)
