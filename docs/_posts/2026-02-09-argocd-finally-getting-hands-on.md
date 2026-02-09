---
layout: post
title:  "🐙 ArgoCD: Finally Getting My Hands Dirty"
date:   2026-02-09 10:30:00 +0000
categories: platform-engineering
---
I'm pumped. After months of reading about it, watching talks, and seeing it pop up in every "modern platform engineering" conversation, I'm finally going to use ArgoCD at work. Not just play around with it locally—actually put it into production. So before I dive in, here's why I think this tool is such a big deal.

**What is ArgoCD?**

At its core, ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes. That's a mouthful, but the idea is straightforward: you declare the desired state of your Kubernetes resources in Git, and ArgoCD makes sure your cluster matches that state. If something drifts, ArgoCD detects it and can automatically sync things back. Git becomes your single source of truth.

It's built on the [GitOps principles](https://www.gitops.tech/) pioneered by Weaveworks, and it's become the de facto standard for GitOps in the Kubernetes world. It's a CNCF graduated project, which tells you something about its maturity and adoption.

**Why ArgoCD is Powerful for Platform Engineering**

The beauty of ArgoCD really shines when you're building an Internal Developer Platform (IDP). Here's why:

**1. Self-Service for Developers**

With ArgoCD, developers can deploy applications by simply opening a PR to update manifests in Git. No need to learn `kubectl`, no need for special access to the cluster, no need to ping the platform team for every deployment. The platform team sets up the guardrails (policies, RBAC, namespaces), and developers get safe, self-service deployments.

**2. Auditability and Compliance**

Everything goes through Git, which means every change has a commit, a timestamp, and an author. You get an audit trail for free. Need to know who deployed what and when? Check the Git history. This is huge for compliance and troubleshooting.

**3. Drift Detection and Reconciliation**

Manual changes to the cluster? Someone ran a `kubectl edit` to "fix something real quick"? ArgoCD will notice and either flag it or automatically revert it, depending on your configuration. This keeps your environments predictable and prevents the dreaded "it works on my machine" syndrome.

**4. Multi-Cluster, Multi-Tenant Support**

ArgoCD can manage applications across multiple clusters from a single control plane. This is a game-changer for platform teams managing dev, staging, and production environments—or even multiple production clusters across regions. Add in RBAC and project-based isolation, and you've got a scalable multi-tenant deployment system.

**5. Progressive Delivery and Rollbacks**

ArgoCD integrates beautifully with tools like Argo Rollouts for blue-green deployments, canary releases, and automatic rollbacks. This means you can build sophisticated deployment strategies without writing a ton of custom scripts.

**6. Observability**

The ArgoCD UI gives you a real-time view of your applications: what's deployed, what's synced, what's out of sync, and the health of each resource. It's visual, intuitive, and makes troubleshooting so much easier. No more SSHing into clusters and running 20 `kubectl` commands to understand the state of things.

**ArgoCD and Internal Developer Platforms**

If you're building an IDP, ArgoCD is often the centerpiece of the deployment layer. Here's how it fits in:

- **App Templates**: Combine ArgoCD with [Backstage](https://backstage.io/) or another portal, and developers can spin up new services from templates. The platform creates the Git repo with the manifests, registers the app in ArgoCD, and developers are off to the races.

- **Environment Promotion**: Want to promote an app from staging to production? Update the manifest in the production branch or folder, and ArgoCD handles the rest. Simple, predictable, and traceable.

**What's Next for Me**

At work, we're setting up ArgoCD to manage deployments across our Kubernetes clusters. The plan is to start with a few pilot applications, build confidence, and then gradually onboard more teams.

I'll be documenting the journey here—what works, what doesn't, lessons learned, and maybe some patterns worth stealing. If you're thinking about adopting ArgoCD or GitOps in general, I hope my notes help.

Let's get declarative.

**Resources to Dive Deeper:**

- [ArgoCD Official Docs](https://argo-cd.readthedocs.io/en/stable/) — The best place to start
- [GitOps Principles](https://www.gitops.tech/) — Understanding the foundation
