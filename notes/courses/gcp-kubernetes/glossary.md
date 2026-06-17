# Glossary

| Term | Definition | Reference |
|---|---|---|
| Alert | A notification triggered when a budget threshold (e.g., 50/90/100%) is hit | [GCP Fundamentals](gcp-fundamentals.md#billing) |
| Allocation Quota | Limits total resources held at once; does not reset over time | [GCP Fundamentals](gcp-fundamentals.md#quota-types) |
| `allAuthenticatedUsers` | IAM special identifier for any authenticated Google account | [GCP Fundamentals](gcp-fundamentals.md#1-the-who-principals--identities) |
| `allUsers` | IAM special identifier for fully public (unauthenticated) access | [GCP Fundamentals](gcp-fundamentals.md#1-the-who-principals--identities) |
| App Engine | GCP's PaaS for simple web apps and mobile backends | [GCP Fundamentals](gcp-fundamentals.md#compute-services) |
| Basic Role | Broad legacy IAM role (Owner/Editor/Viewer); avoid in production | [GCP Fundamentals](gcp-fundamentals.md#2-the-what-roles-and-permissions) |
| Binding | IAM construct connecting Principals to exactly one Role | [GCP Fundamentals](gcp-fundamentals.md#4-the-glue-iam-policy) |
| `bq` | GCP CLI tool specific to BigQuery | [GCP Fundamentals](gcp-fundamentals.md#interactions--clients) |
| Budget | A soft spending limit for a timeframe; alerts but doesn't block | [GCP Fundamentals](gcp-fundamentals.md#billing) |
| Cloud Run | GCP's serverless containers (scale-to-zero) | [GCP Fundamentals](gcp-fundamentals.md#compute-services) |
| Cloud Run Functions | GCP's FaaS for event-driven tasks | [GCP Fundamentals](gcp-fundamentals.md#compute-services) |
| Cloud Shell | Debian-based VM with `gcloud` preinstalled, accessible from the web console | [GCP Fundamentals](gcp-fundamentals.md#interactions--clients) |
| Compute Engine | GCP IaaS providing VMs with full OS control | [GCP Fundamentals](gcp-fundamentals.md#compute-services) |
| Concurrent Quota | Limits active ongoing operations; slot frees when one finishes | [GCP Fundamentals](gcp-fundamentals.md#quota-types) |
| Condition | Optional IAM logic that grants access only when criteria are met (time, IP, etc.) | [GCP Fundamentals](gcp-fundamentals.md#4-the-glue-iam-policy) |
| Custom Role | IAM role created by hand-picking specific permissions | [GCP Fundamentals](gcp-fundamentals.md#2-the-what-roles-and-permissions) |
| Docker | Most popular container runtime; written in Go | [GCP Fundamentals](gcp-fundamentals.md#up-next-course-tbd) |
| Edge Network | GCP's points of presence near users for content delivery | [GCP Fundamentals](gcp-fundamentals.md#infrastructure-hierarchy) |
| Folder | Optional IAM resource-hierarchy level grouping projects; nestable up to 10 levels | [GCP Fundamentals](gcp-fundamentals.md#resource-hierarchy) |
| `gcloud` | Primary GCP command-line tool | [GCP Fundamentals](gcp-fundamentals.md#interactions--clients) |
| GKE | Google Kubernetes Engine — managed Kubernetes service (CaaS) | [GCP Fundamentals](gcp-fundamentals.md#compute-services) |
| Global | Top GCP infrastructure scope (services like global LB and DNS) | [GCP Fundamentals](gcp-fundamentals.md#infrastructure-hierarchy) |
| Google Group | A collection of Google Accounts and/or service accounts | [GCP Fundamentals](gcp-fundamentals.md#1-the-who-principals--identities) |
| IAM Policy | Collection of bindings attached to a resource | [GCP Fundamentals](gcp-fundamentals.md#4-the-glue-iam-policy) |
| Inheritance (IAM) | Permissions granted at a higher level flow down to all children | [GCP Fundamentals](gcp-fundamentals.md#resource-hierarchy) |
| LXC | Linux container runtime; suits data-intensive apps and ops | [GCP Fundamentals](gcp-fundamentals.md#up-next-course-tbd) |
| MIG | Managed Instance Group — auto-scales Compute Engine VMs | [GCP Fundamentals](gcp-fundamentals.md#compute-services) |
| Multi-Region | GCP scope spanning large geographies (US, EU, Asia) | [GCP Fundamentals](gcp-fundamentals.md#infrastructure-hierarchy) |
| Organization | Top GCP resource-hierarchy node, linked to Workspace/Cloud Identity domain | [GCP Fundamentals](gcp-fundamentals.md#resource-hierarchy) |
| Permission | Most granular IAM unit (e.g., `storage.buckets.get`) | [GCP Fundamentals](gcp-fundamentals.md#2-the-what-roles-and-permissions) |
| Podman | Daemonless container runtime with stronger security model than Docker | [GCP Fundamentals](gcp-fundamentals.md#up-next-course-tbd) |
| Predefined Role | Fine-grained IAM role maintained by Google (e.g., "Storage Object Admin") | [GCP Fundamentals](gcp-fundamentals.md#2-the-what-roles-and-permissions) |
| Principal | An entity that can be granted access (user, service account, group, etc.) | [GCP Fundamentals](gcp-fundamentals.md#1-the-who-principals--identities) |
| Project | Required GCP resource-hierarchy level; where APIs are enabled and billing links | [GCP Fundamentals](gcp-fundamentals.md#resource-hierarchy) |
| Quota | Hard limit on resource consumption; adjustable per project | [GCP Fundamentals](gcp-fundamentals.md#billing) |
| Rate Quota | Limits action frequency; resets automatically after a time window | [GCP Fundamentals](gcp-fundamentals.md#quota-types) |
| Region | A specific GCP geographic location (e.g., `us-east1`) | [GCP Fundamentals](gcp-fundamentals.md#infrastructure-hierarchy) |
| Report | A visual GCP dashboard of historical or forecasted spend | [GCP Fundamentals](gcp-fundamentals.md#billing) |
| Resource (IAM) | The thing being accessed (VM, bucket, table, cluster, etc.) | [GCP Fundamentals](gcp-fundamentals.md#3-the-where-resources-and-hierarchy) |
| Role | A named bundle of IAM permissions | [GCP Fundamentals](gcp-fundamentals.md#2-the-what-roles-and-permissions) |
| Service Account | IAM identity for an application or VM rather than a person | [GCP Fundamentals](gcp-fundamentals.md#1-the-who-principals--identities) |
| System Limit | Fixed architectural ceiling that cannot be changed (e.g., GCS 5TB object) | [GCP Fundamentals](gcp-fundamentals.md#quotas-vs-system-limits) |
| Vagrant | Tool providing strong isolation on a host machine (VM-flavored) | [GCP Fundamentals](gcp-fundamentals.md#up-next-course-tbd) |
| VPC | Virtual Private Cloud; max 15,000 VM instances (a system limit) | [GCP Fundamentals](gcp-fundamentals.md#quotas-vs-system-limits) |
| Zone | Isolated deployment area within a region (e.g., `us-east1-a`) | [GCP Fundamentals](gcp-fundamentals.md#infrastructure-hierarchy) |
