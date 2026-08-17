# AutoResilience - Shared Integration Contract

## 1. Why this file exists

This file is the shared agreement between the frontend, backend, and chaos/infrastructure work.

If every teammate follows this document, they can work separately without inventing incompatible names, files, JSON formats, or architecture.

---

# 2. Team Ownership

## Frontend teammate

Primary ownership:

```text
apps/web/
```

May read:

```text
packages/contracts/
```

Should not independently modify:

```text
apps/api/
chaos/
infra/
monitoring/
```

---

## Backend teammate + project owner

Primary ownership:

```text
apps/api/
packages/contracts/
```

Recommended split:

### Backend teammate primarily owns

```text
configuration
routing
schemas
state machine
database setup
models
persistence
CRUD/history/report API foundations
backend tests
```

### Project owner primarily owns

```text
Kubernetes integration adapter
Prometheus integration adapter
ChaosProvider / Litmus integration
experiment orchestrator
recovery detection
resilience analysis
resilience scoring
final integration behavior
```

Both review the shared API/domain contract.

---

## Project owner - Chaos / Infrastructure

Primary ownership:

```text
infra/
chaos/
monitoring/
```

Includes:

```text
kind
target app
Prometheus deployment
Alertmanager
LitmusChaos
experiment templates
safety policies
manual experiment proof
PromQL validation
```

---

# 3. Do Not Edit the Same Backend File at the Same Time

Two people can share backend work safely if ownership is split by phase/file.

Bad:

```text
Person A edits app/api/routes/experiments.py
Person B edits app/api/routes/experiments.py
both keep working for three days
```

Better:

```text
Backend teammate:
app/domain/
app/schemas/
app/db/
app/api/

Project owner:
app/integrations/
app/services/orchestration/
app/services/analysis/
app/services/scoring/
```

Exact folders should only be created when phases require them.

If one person needs to modify another person's owned file:

1. pull latest main,
2. coordinate,
3. make a small PR,
4. explain why the shared file changed.

---

# 4. Product Flow

```text
Target Discovery
-> Experiment Configuration
-> Safety Validation
-> Baseline Measurement
-> Controlled Chaos
-> Live Evidence
-> Recovery Analysis
-> Explainable Resilience Score
-> History / Comparison
-> Report / CI Gate
```

Frontend never talks directly to Litmus or Prometheus.

Correct:

```text
Frontend -> FastAPI -> Prometheus
Frontend -> FastAPI -> ChaosProvider -> Litmus
```

---

# 5. Canonical Experiment Status

Use these values across frontend/backend:

```text
CREATED
VALIDATING
BASELINING
INJECTING
OBSERVING
RECOVERING
COMPLETED
VALIDATION_FAILED
INJECTION_FAILED
ABORTED
UNKNOWN
```

Do not create alternate underlying values such as:

```text
DONE
FINISHED
SUCCESS_OK
```

Frontend may show friendly labels but maps back to the canonical values.

---

# 6. Canonical Fault Types

Initial product-level values:

```text
POD_FAILURE
CPU_STRESS
MEMORY_STRESS
NETWORK_LATENCY
PACKET_LOSS
```

Litmus internal names can differ.

The translation belongs in the backend chaos adapter.

---

# 7. Provisional API Contract

Current real endpoint:

```text
GET /health
```

Planned product prefix:

```text
/api/v1
```

Provisional endpoints:

```text
GET  /api/v1/targets
GET  /api/v1/experiments
POST /api/v1/experiments
GET  /api/v1/experiments/{experiment_id}
POST /api/v1/experiments/{experiment_id}/abort
GET  /api/v1/experiments/{experiment_id}/metrics
GET  /api/v1/services/risk
GET  /api/v1/reports/{experiment_id}
```

The backend may refine this during its contract phase.

After frontend uses a finalized route/shape, changes require coordination.

---

# 8. Provisional Shared Data

## Experiment summary

```json
{
  "id": "exp_001",
  "name": "Checkout Pod Failure",
  "status": "COMPLETED",
  "fault_type": "POD_FAILURE",
  "namespace": "online-boutique",
  "target": "checkoutservice",
  "started_at": "2026-08-20T10:00:00Z",
  "completed_at": "2026-08-20T10:03:15Z",
  "resilience_score": 78
}
```

## Create experiment

```json
{
  "name": "Checkout Pod Failure",
  "fault_type": "POD_FAILURE",
  "target": {
    "namespace": "online-boutique",
    "workload": "checkoutservice"
  },
  "duration_seconds": 45,
  "blast_radius": {
    "affected_replicas": 1
  },
  "load_generation": false
}
```

## Timeline event

```json
{
  "timestamp": "2026-08-20T10:01:10Z",
  "type": "FAULT_OBSERVED",
  "message": "checkoutservice replica became unavailable",
  "source": "kubernetes"
}
```

## Metric series

```json
{
  "metric": "p95_latency_ms",
  "unit": "ms",
  "points": [
    {
      "timestamp": "2026-08-20T10:00:00Z",
      "value": 118.0
    }
  ]
}
```

## Score breakdown

```json
{
  "total": 73,
  "maximum": 100,
  "components": [
    {
      "name": "Availability",
      "score": 27,
      "maximum": 30,
      "reason": "Availability briefly dropped below baseline.",
      "evidence": "Minimum availability: 91.2%"
    }
  ]
}
```

---

# 9. Mock-to-Real Frontend Rule

Frontend should use:

```text
Component
-> query hook
-> API client
-> HTTP request
-> MSW mock during independent work
OR
-> real FastAPI during integration
```

Do not put huge mock arrays directly inside pages.

---

# 10. Chaos-to-Backend Contract

The chaos owner must provide the backend owner with:

```text
Litmus version
how experiment is started
required inputs
how run ID is identified
how status is queried
how successful injection is proven
how experiment is aborted
how errors appear
required permissions
sample successful result
sample failed result
```

The backend should not guess Litmus behavior.

---

# 11. Observability Contract

Chaos owner provides:

```text
verified PromQL
metric meaning
labels
sample normal result
sample degraded result
known missing metrics
alert names
```

Backend owns the Prometheus HTTP client and recovery analysis.

Frontend consumes only backend product data.

---

# 12. Anti-Duplication Rule

Before creating files:

```bash
git status
git branch --show-current
find . -maxdepth 4 -type f | sort
```

Workstream-specific:

```bash
find apps/web -maxdepth 5 -type f | sort
find apps/api -maxdepth 5 -type f | sort
find chaos infra monitoring -maxdepth 5 -type f | sort
```

If file exists:

```text
READ -> UNDERSTAND -> MODIFY
```

Do not create duplicates with names such as:

```text
config_new.py
config2.py
final_config.py
```

unless there is a genuine reason.

---

# 13. Branch Rule

Never do long feature work directly on main.

```bash
git checkout main
git pull origin main
git checkout -b feature/<name>
```

Small PRs are preferred.

---

# 14. Commit Convention

```text
feat(web): ...
feat(api): ...
feat(chaos): ...
feat(infra): ...
fix(...): ...
test(...): ...
docs: ...
refactor(...): ...
ci: ...
chore: ...
```

---

# 15. Platform Failure vs Target Failure

Target failure affects resilience:

```text
application unavailable
latency increases
error rate rises
recovery fails
```

Platform failure means AutoResilience could not evaluate correctly:

```text
Prometheus unavailable
Litmus failed to start
Kubernetes credentials invalid
backend crashed
result unknown
```

Platform failure must not automatically reduce the target's Resilience Score.

---

# 16. Timestamp Rule

Use UTC in API/persistence:

```text
2026-08-20T10:01:10Z
```

Frontend may display local time.

This is required for aligning:

```text
baseline
fault
alert
recovery
```

---

# 17. Shared Definition of Done

A feature is not complete because:

```text
it works on my branch
```

It is complete when:

```text
it works after merge with current main
```
