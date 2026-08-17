# AutoResilience - Chaos / Infrastructure Owner Execution Guide

## 1. Who owns this

The project owner owns the complete chaos/infrastructure workstream.

Primary locations:

```text
infra/
chaos/
monitoring/
```

This is one of the hardest workstreams because it depends on several real systems behaving correctly together.

---

# 2. Why this work is difficult

Unlike normal application code, failures can come from many places:

```text
Docker resources
kind
kubectl context
Kubernetes scheduling
RBAC
Helm
Litmus version behavior
target selectors
Prometheus scraping
missing metrics
Alertmanager timing
laptop CPU/memory
```

A failed experiment does not automatically mean the target application is weak.

You must determine whether:

```text
the intended fault happened
```

or:

```text
the chaos platform itself failed
```

---

# 3. What you own

```text
local Kubernetes cluster
target microservices
LitmusChaos
manual experiment validation
experiment templates
chaos safety
Prometheus deployment
Prometheus query validation
Alertmanager
optional Grafana debug dashboards
infrastructure documentation
backend integration handoff
```

You also own the backend's final Litmus integration because you understand the real execution behavior.

---

# 4. Branches

Examples:

```text
feature/infra-kind-cluster
feature/infra-online-boutique
feature/infra-observability
feature/chaos-litmus-install
feature/chaos-pod-failure
feature/chaos-cpu-stress
feature/chaos-network-latency
```

Merge stable subphases regularly.

---

# 5. First inspection

```bash
git status
git branch --show-current
find infra chaos monitoring -maxdepth 5 -type f | sort
```

Check:

```bash
docker --version
kind --version
kubectl version --client
helm version
```

Do not recreate existing setup.

---

# 6. K1 - kind Cluster

Goal:

A clean, reproducible development cluster.

Store only the configuration actually needed.

Likely location:

```text
infra/kind/
```

Possible:

```text
cluster.yaml
README.md
```

Verification:

```bash
kind get clusters
kubectl config current-context
kubectl cluster-info
kubectl get nodes
kubectl get pods -A
```

Document teardown.

Do not add multi-node complexity without a real need.

---

# 7. K2 - Target Microservices

Expected target:

```text
Google Online Boutique
```

Record:

```text
namespace
workloads
services
replica counts
important dependencies
how to access frontend
normal resource usage
```

Verify healthy state before chaos.

---

# 8. O1 - Prometheus

Deploy and verify monitoring before chaos.

Need:

```text
Prometheus healthy
target/cluster scraped
useful labels known
queries manually verified
```

Recommended catalog:

```text
monitoring/prometheus/queries.md
```

For each query:

```text
Metric:
Purpose:
PromQL:
Expected labels:
Healthy result:
Fault result:
Limitation:
```

Do not invent application metrics that do not exist.

---

# 9. O2 - Alertmanager

Create a small useful alert set.

Need:

```text
fault timestamp
alert fired timestamp
alert resolved timestamp
```

This later supports detection-time evaluation.

---

# 10. C1 - LitmusChaos Installation

Use the official documentation for the version actually installed.

Do not rely on AI memory for:

```text
CRDs
workflow APIs
Helm values
resource names
GraphQL/API details
```

Document:

```text
version
installation
namespace
components
health check
uninstall
```

---

# 11. C2 - Manual Pod Failure

This is the first critical experiment.

Do not automate it first.

Document:

```text
target
namespace
replicas
fault duration
affected replicas
Litmus run ID
start time
fault evidence
Kubernetes evidence
Prometheus evidence
recovery evidence
cleanup
```

You should be able to answer:

> How do I know the fault actually happened?

and:

> How do I know the application actually recovered?

---

# 12. C3 - Repeatable Pod Failure Template

Once manual run is proven, turn it into a reusable template.

Expose only useful product parameters:

```text
namespace
workload
duration
affected replicas
```

Document required permissions and expected status behavior.

---

# 13. C4 - Safety Policies

Define:

```text
allowed namespace
forbidden namespace
max duration
max affected replicas
minimum healthy replicas
abort process
```

Backend later enforces product-level safety.

Chaos-side restrictions provide additional protection.

---

# 14. C5 - Backend Integration Handoff

Create a concise Litmus integration record for the backend.

Must include:

```text
exact installed version
how to start experiment
inputs
how to identify run
how to query status
how to confirm injection
how to abort
how errors appear
permissions
success example
failure example
```

The backend must not guess this.

---

# 15. Mid-Sem Goal

By mid-sem:

```text
kind reproducible
Online Boutique healthy
Prometheus working
queries documented
Alertmanager basic alert works
Litmus installed
manual pod failure works
pod template repeatable
safety documented
backend Litmus contract available
```

This is more valuable than five unreliable chaos types.

---

# 16. Additional Faults

After pod failure:

```text
CPU stress
memory stress
network latency
packet loss
```

For every experiment verify actual effect, not just Litmus completion.

---

# 17. CPU Stress

Record:

```text
stress level
duration
CPU evidence
application impact
recovery
```

---

# 18. Memory Stress

Watch for:

```text
memory pressure
OOM
restart
resource limits
recovery
```

Start conservatively.

---

# 19. Network Latency

Use a meaningful service dependency.

Record:

```text
where latency was injected
amount
duration
affected services
latency/error changes
recovery
```

---

# 20. Packet Loss

Prove measurable impact.

Do not mark success only because the chaos workflow ended.

---

# 21. Troubleshooting Order

When chaos fails, separate layers:

```text
1 local machine resources
2 Docker
3 kind/Kubernetes
4 target workload
5 RBAC
6 Litmus
7 Prometheus
8 Alertmanager
9 backend integration
```

Do not reinstall everything immediately.

---

# 22. Common Problems

## Wrong kubectl context

Always check before destructive work:

```bash
kubectl config current-context
```

## Pending pods

Inspect:

```bash
kubectl describe pod <pod>
kubectl get events -A --sort-by=.lastTimestamp
```

## No Prometheus data

Check:

```text
scrape
labels
query
time range
metric availability
```

## Alert not firing

Check:

```text
PromQL condition
evaluation interval
for duration
fault duration
labels
```

## Litmus says run started but target unchanged

Check:

```text
target selector
RBAC
service account
experiment events
actual target events
```

---

# 23. What Backend Needs From You

After each infrastructure/chaos milestone provide:

```text
cluster context/name
namespace
target workloads
verified queries
alert names
Litmus execution contract
sample success output
sample failure output
known platform errors
```

---

# 24. AI Prompt for Chaos Session

```text
Read the AutoResilience shared integration contract and chaos/infrastructure owner guide.

I am the project owner and I fully own this workstream.
Current phase: <PHASE>.

Before commands:
- ask for git status and branch,
- inspect infra/, chaos/, monitoring/,
- check installed tool versions when relevant,
- use official docs for the actual installed Litmus/Kubernetes/Prometheus versions,
- do not duplicate manifests/scripts.

Give 2-4 connected steps at a time.
Explain what each step proves.
At completion provide:
verification,
evidence,
files changed,
commit message,
backend handoff,
current-state update,
next phase.
```
