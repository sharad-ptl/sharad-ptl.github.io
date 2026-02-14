---
layout: post
title: "📐 Designing an IDP: The BACK Stack on Azure"
date: 2026-02-14 10:00:00 +0000
categories: platform-engineering
---

I got it into my head to build an Internal Developer Platform on Azure — the kind where developers get a portal, pick a template, and end up with a running service and maybe a database, without chasing the platform team. So I'm doing it: **Backstage**, **ArgoCD**, **Crossplane**, **Kyverno** (the BACK stack) on AKS, with **zero Terraform**. Everything lives in Git; every change goes through a PR. Here's how it fits together at a high level — and why I think the payoff is worth the learning curve.

### The shape of the platform

The architecture is four layers. At the top, **developers** see Backstage (the portal) and software templates (the golden paths). One layer down, **GitOps**: ArgoCD runs the show — app-of-apps, self-managing, watching Git and keeping the cluster in sync. Below that, the **platform API**: Crossplane (XRDs, compositions, Azure provider) plus cluster add-ons like ingress and cert-manager. At the bottom, **infrastructure**: AKS, ACR, managed identities — the real Azure resources.

![BACK stack architecture — four layers: Developer (Backstage, templates), GitOps (ArgoCD), Platform API (Crossplane, add-ons), Infrastructure (AKS, ACR, Azure)]({{ "/assets/idp-back-architecture.png" | relative_url }})

The fun part is the bootstrap. A script spins up a temporary kind cluster, uses Crossplane to provision the *real* Azure infra, installs ArgoCD on AKS, then deletes kind. After that, ArgoCD owns everything — including itself — and Crossplane on AKS adopts that infra for good. No Terraform state, no snowflake scripts. Git wins, and you get to stop worrying about "where's the state file?"

### How it flows

Developer hits Backstage, chooses a template. The template might create a new repo, add an ArgoCD Application to the GitOps repo, and optionally drop in a Crossplane claim for a database. ArgoCD sees the changes and syncs: the app lands on the cluster, Crossplane provisions Azure resources from the claim. One loop from "I need a service" to "it's running," and it's all in Git — auditable, reviewable, repeatable.

I split the implementation across a handful of repos (bootstrap scripts, GitOps definitions, platform API, Backstage app, templates) so each has a clear job — but the important thing is the layering and the single source of truth, not the exact repo count.

### Why this stack?

| Tool | Role |
|------|------|
| **Backstage** | One place to discover stuff, run templates, see deployment status |
| **ArgoCD** | Git as source of truth, cluster stays in sync, self-managing |
| **Crossplane** | One declarative tool for both "create the cluster" and "give me a database" — same API, same Git flow |
| **Kubernetes** | Where it all runs. The glue is Git |

([ArgoCD](https://argo-cd.readthedocs.io/en/stable/) · [Crossplane](https://docs.crossplane.io/) · [Backstage](https://backstage.io/docs/))

### Why no Terraform?

Honestly? I wanted to see if I could do it. I don't hate Terraform — I just wanted to push the "GitOps all the way" idea and see whether Crossplane could own the whole infra story, from cluster to database. It can. One tool, one flow, everything in Git. The upside is real (no state files, one API); the main reason I tried was curiosity.

Next post: the bootstrap script in detail — the ignition key, what it actually does, and AKS stop/start for cost control. Full build guide lives in the project; these posts are the short version.

---

**Resources to dive deeper:**

- [Backstage documentation](https://backstage.io/docs/) — Developer portals and software templates
- [ArgoCD documentation](https://argo-cd.readthedocs.io/en/stable/) — GitOps for Kubernetes
- [Crossplane documentation](https://docs.crossplane.io/) — Cloud-native control planes and the Azure provider
- [GitOps principles](https://www.gitops.tech/) — The foundation: Git as source of truth
- [CNCF Platforms whitepaper](https://tag-app-delivery.cncf.io/whitepapers/platforms/) — Platform engineering in the wild
- [AKS documentation](https://learn.microsoft.com/en-us/azure/aks/) — Azure Kubernetes Service
