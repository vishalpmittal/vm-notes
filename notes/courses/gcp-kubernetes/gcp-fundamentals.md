# GCP Fundamentals — Getting Started with GKE (Coursera)

Notes from the Coursera "Getting Started with Google Kubernetes Engine" course. This file covers GCP foundations (hierarchies, compute, IAM, billing, clients) that the course establishes before getting into GKE specifically.

## Key Takeaways

- GCP has **two parallel hierarchies**: an **infrastructure** hierarchy (Global → Multi-Region → Region → Zone → Edge) for where compute lives, and a **resource** hierarchy (Organization → Folders → Projects → Resources) for how access and billing flow
- The compute spectrum spans **IaaS → CaaS → PaaS → FaaS**: Compute Engine (VMs) → GKE (containers) → App Engine (PaaS) → Cloud Run / Cloud Run Functions (serverless). Cloud Run is the middle-ground "serverless containers" sweet spot
- **IAM has four moving parts**: Principals (who) + Roles (what) + Resources (where) + Policy (the glue) — and permissions **inherit down** the resource hierarchy
- **Quota ≠ system limit**: quotas are adjustable per project (request an increase); system limits are fixed architectural constraints (e.g., 5TB max GCS object)
- For day-to-day work, the **gcloud CLI** + **Cloud Shell** combo is the productive default — Console is for exploration, APIs/SDKs for automation

## Infrastructure Hierarchy

Where compute physically lives, from broadest to narrowest:

| Level | Scope | Description |
|---|---|---|
| **Global** | World | Services like Cloud Load Balancing and DNS operate here to route traffic globally |
| **Multi-Region** | Large geographies | Broad areas (US, EU, Asia); data replicated across multiple regions for max DR |
| **Region** | Specific geo | A specific location (e.g., `us-east1` in South Carolina); high-bandwidth links connect regions |
| **Zone** | Data center cluster | Deployment area within a region (e.g., `us-east1-a`); zones are isolated from each other |
| **Edge Network** | Global entry points | Caching nodes / Points of Presence near users for content delivery acceleration |

**Why it matters:** services pick their level based on the failure isolation and latency tradeoff you want. A global LB hides the regional layer; a regional DB does not.

## Resource Hierarchy

How access and billing flow, with physical-org analogies:

| Level | Physical analogy | Requirement | Purpose |
|---|---|---|---|
| **1. Organization** | The company | Root node | Top-level container; linked to Google Workspace / Cloud Identity domain |
| **2. Folders** | The departments | Optional | Group projects by department, environment, or team; nestable up to 10 levels |
| **3. Projects** | The workspaces | Required | The fundamental "bucket" — APIs are enabled and billing is linked here |
| **4. Resources** | The tools | Leaf nodes | The actual services: Compute Engine VMs, GCS buckets, GKE clusters, etc. |

**Key rule — inheritance:** permissions flow *down* the hierarchy. Grant a role at the Folder level and the principal inherits it on every Project and Resource inside.

## Compute Services

Picking the right runtime depends on how much control vs. how much management you want:

| Service | Category | Abstraction | Scaling | Best for |
|---|---|---|---|---|
| **Compute Engine** | IaaS (VMs) | Low (full control) | Manual or Auto (MIGs) | Legacy apps, custom OS needs, HPC |
| **GKE (Kubernetes)** | CaaS (containers) | Medium | Automated (Pods & Nodes) | Complex microservices and container orchestration |
| **Cloud Run** | Serverless containers | High | Automatic (scale-to-zero) | Modern web apps, APIs, stateless microservices |
| **App Engine** | PaaS | High | Automatic | Simple web apps, mobile backends (code-first) |
| **Cloud Run Functions** | FaaS (serverless) | Highest | Automatic (per-event) | Event-driven tasks (image resizing, webhooks) |

The progression left-to-right: more managed = less control but less ops burden.

## IAM — The Four-Part Mental Model

### 1. The "Who" (Principals / Identities)

A **Principal** is an entity that can be granted access. Types:

- **Google Account** — individual users (`alice@gmail.com`)
- **Service Account** — identity for an application or VM (not a person)
- **Google Group** — collection of accounts and/or service accounts
- **Google Workspace / Cloud Identity Domain** — all users in a company domain
- **`allAuthenticatedUsers` / `allUsers`** — special identifiers for authenticated or fully public access

### 2. The "What" (Roles and Permissions)

GCP does **not** grant individual permissions directly to principals. Instead, it groups them into roles:

- **Permission** — the most granular unit (e.g., `storage.buckets.get`)
- **Role** — a named bundle of permissions

Three role tiers:

| Tier | What it is | When |
|---|---|---|
| **Basic Roles** | Owner, Editor, Viewer — very broad | **Avoid in production** — too coarse |
| **Predefined Roles** | Fine-grained, managed by Google (e.g., "Storage Object Admin") | Most production cases |
| **Custom Roles** | Hand-pick specific permissions | When predefined doesn't fit and least-privilege matters |

### 3. The "Where" (Resources and Hierarchy)

The **Resource** is the thing being accessed. Access is managed *through* the resource hierarchy (Organization → Folder → Project → Resource). Inheritance flows down — grant at Folder level, inherited at every Project and Resource inside.

### 4. The "Glue" (IAM Policy)

| Component | What it does |
|---|---|
| **Binding** | Connects one or more Principals to exactly one Role |
| **Policy** | A collection of bindings attached to a resource |
| **Condition** | (Optional) Logic that grants access only when criteria are met (time, source IP, etc.) |

A policy is the artifact you actually edit; bindings are the rows inside it.

## Billing

Four interrelated concepts:

| Term | What it is | Goal | Resource level | Infra level |
|---|---|---|---|---|
| **Budget** | A *soft* spending limit for a timeframe | Track costs against a plan | Org, Folder, Project | Global (filterable) |
| **Alert** | A notification (email/SMS/Pub/Sub) triggered by a budget | Warn at 50%, 90%, 100% | Org, Folder, Project | Global |
| **Quota** | A *hard* limit on resource consumption | Prevent runaway costs, fair sharing | Project (primary) | Regional & Zonal |
| **Report** | A visual dashboard of historical/forecasted spend | Analyze trends, find anomalies | Any | Regional (price varies by location) |

**Budget vs Quota — the key distinction:** budgets *alert* (soft), quotas *block* (hard). Budgets don't stop spending; quotas do.

### Quota Types

| Type | How it works | Example |
|---|---|---|
| **Allocation Quotas** | Limits the total number of resources held at once; does *not* reset over time — delete to free | Max 100 CPUs in a region; 50 Static IPs |
| **Rate Quotas** | Limits frequency of actions; resets automatically after a time window | 30,000 Vertex AI inference requests per minute |
| **Concurrent Quotas** | Limits active ongoing operations; slot frees when an operation finishes | Max 10 concurrent BigQuery export jobs |

### Quotas vs System Limits

- **Quotas** — adjustable. Go to *IAM & Admin → Quotas* and request an increase
- **System Limits** — fixed architectural ceilings. Cannot be changed (e.g., GCS object max = 5TB; VPC max = 15,000 VM instances)

## Interactions / Clients

How you actually talk to GCP:

- **`console.cloud.google.com`** — web UI; best for exploration, dashboards, occasional changes
- **SDK** — programmatic access:
  - **`gcloud`** — primary CLI
  - **`gcloud storage`** — GCS-specific
  - **`bq`** — BigQuery-specific
- **Cloud Shell** — Debian-based VM accessible from the console; provides CLI access in a web interface; useful when you don't have local `gcloud` configured
- **APIs** — direct REST/gRPC for custom integrations
- **Mobile App** — phone application for monitoring on the go

The pragmatic default: **`gcloud` from a local terminal** for routine work, **Cloud Shell** when working from someone else's machine or for quick one-offs, **Console** for visual exploration.

## Up Next (Course TBD)

The course's own TBD section flagged these as upcoming topics:

### Container Vendors

- **Docker** — most popular, robust ecosystem
- **Podman** — daemonless architecture, stronger security model than Docker
- **LXC** — better for data-intensive apps and operations (closer to OS-level containers)
- **Vagrant** — high isolation on the running physical machine (more VM-flavored than container-flavored)

### Docker

- Written in Go

### Docker Swarm vs. Kubernetes

(Section to be filled in as the course covers it.)

## Related Notes

- [Docker vs Kubernetes](../../infra/docker-vs-kubernetes.md) — repo-level coverage of K8s fundamentals
- [Cloud-native data architecture](../../system-design/database/cloud-native-data-architecture.md) — GCP/AWS/Azure data services from the architecture lens

---

**Source:** https://www.coursera.org/learn/google-kubernetes-engine/home/welcome
**Source:** /Users/vimittal/Downloads/GCP Kubernetes Engine.html (course notes)
**Date:** 2026-06-17
**Tags:** gcp, google-cloud, kubernetes, gke, iam, billing, compute-engine, cloud-run, app-engine, cloud-functions, infrastructure-hierarchy, resource-hierarchy, quotas, system-limits, gcloud, cloud-shell, course-notes, coursera
