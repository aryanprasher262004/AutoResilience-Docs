# AutoResilience - Shared Backend Execution Guide

## 1. Who works on backend

The backend is shared between:

- the project owner,
- one backend teammate who works closely with the project owner.

The project owner also owns Litmus/chaos work.

Because chaos + final integration are already difficult, the backend teammate should take a **real independent chunk of backend work**, not just "help sometimes."

---

# 2. Recommended Backend Split

## Backend teammate - primary responsibility

Take the deterministic backend foundation:

```text
B1 Configuration
B2 API routing/versioning
B3 Experiment domain schemas
B4 Experiment state machine

D1 Local PostgreSQL
D2 SQLAlchemy setup
D3 Alembic
D4 Persistence

basic CRUD/history/report endpoint foundations
backend unit/API tests
```

This work is substantial, useful, and can be tested without waiting for Litmus.

## Project owner - primary responsibility

Take integration-heavy backend:

```text
Kubernetes adapter
Prometheus adapter
ChaosProvider / Litmus adapter
Preflight validator integration
Baseline collector
Experiment orchestrator
Timeline
Recovery detector
Alert verification
Analysis model
Resilience score
Final system integration
```

This is recommended because the project owner also owns the actual chaos environment and therefore understands the real Litmus/Kubernetes behavior.

---

# 3. Should the backend teammate work alone?

They should work independently on **their backend phases**, but not in isolation from architecture.

Good model:

```text
teammate implements a bounded backend phase
-> project owner reviews PR
-> teammate explains the design
-> merge
-> next phase
```

Do NOT do:

```text
both people edit the same backend files continuously
```

That creates conflicts and makes ownership unclear.

---

# 4. How Both People Still Learn the Backend

For every major backend PR, the author should explain:

```text
problem solved
files changed
inputs
outputs
important classes/functions
tests
failure modes
how another module uses it
```

The other person should review enough to answer:

> If this breaks, where would I start debugging?

That preserves learning without both writing every line.

---

# 5. Current Backend State

Existing:

```text
apps/api/
```

Environment:

```text
Python >= 3.12
uv
.venv
```

FastAPI works.

Current command:

```bash
cd apps/api
uv run fastapi dev app/main.py
```

Current endpoint:

```text
GET /health
```

Swagger:

```text
/docs
```

Current approximate structure:

```text
app/
|-- __init__.py
|-- main.py
|-- api/
|   |-- __init__.py
|   `-- routes/
|       |-- __init__.py
|       `-- health.py
`-- core/
    `-- __init__.py
```

Next phase:

```text
B1 - Configuration & Environment Management
```

---

# 6. Backend Teammate Phase B1 - Configuration

Goal:

Use `pydantic-settings` for typed environment configuration.

Before changes:

```bash
git status
git branch --show-current
find apps/api/app -maxdepth 5 -type f | sort
cat apps/api/pyproject.toml
```

Likely:

```text
apps/api/app/core/config.py
apps/api/.env.example
```

Real `.env` must stay ignored.

Initial settings may include:

```text
app name
environment
database URL
Prometheus URL
frontend origin
```

Do not add fake Litmus settings before the real integration method is known.

Verify:

```text
defaults work
env override works
FastAPI starts
health endpoint works
no secret committed
```

Commit example:

```text
feat(api): add typed application configuration
```

---

# 7. B2 - API Routing and Versioning

Goal:

Keep:

```text
GET /health
```

Add product route prefix:

```text
/api/v1
```

Use `APIRouter`.

Do not create empty endpoint files for hypothetical features.

Verify Swagger organization.

---

# 8. B3 - Domain Schemas

Define product-level concepts:

```text
ExperimentStatus
FaultType
ExperimentTarget
BlastRadius
ExperimentCreate
ExperimentSummary
ExperimentDetail
TimelineEvent
MetricSeries
ScoreBreakdown
```

Use Pydantic/enums.

Validation:

```text
positive duration
affected replicas >= 1
namespace required
workload required
known fault type
```

This phase should update the shared contract.

Frontend depends on this.

---

# 9. B4 - State Machine

Canonical states:

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

State transitions must be deterministic and tested without external systems.

---

# 10. Database Phases

## D1 PostgreSQL

Prefer reproducible containerized local DB.

Coordinate before creating shared root files.

## D2 SQLAlchemy

Create only:

```text
engine
session
Base
```

Use config from B1.

## D3 Alembic

Initialize once only.

Inspect before initialization.

## D4 Persistence

Persist:

```text
experiment identity
config
status
timestamps
summary evidence
score
```

Do not use PostgreSQL as a duplicate Prometheus time-series store.

---

# 11. Backend Teammate Additional Work

After persistence, the teammate can own:

```text
GET /experiments
GET /experiments/{id}
basic POST /experiments persistence layer
history filters
report data retrieval
API tests
```

The project owner later connects the create/run workflow to orchestration.

---

# 12. Project Owner Backend Integration Phases

## I1 Kubernetes Adapter

Used for:

```text
target discovery
namespace/workload validation
pod state
basic safety checks
```

## I2 Prometheus Client

Backend should query Prometheus through a dedicated client.

Handle:

```text
instant query
range query
timeouts
empty data
HTTP errors
response parsing
```

## I3 ChaosProvider

Concept:

```text
Experiment Service
-> ChaosProvider
-> Litmus
```

Operations:

```text
readiness
start
status
abort
```

Do not put Litmus-specific code directly in routes.

---

# 13. Orchestration Phases

## E1 Preflight

Check:

```text
cluster
target
monitoring
chaos engine
duration
blast radius
conflicting run
```

## E2 Baseline

Capture selected steady-state metrics.

## E3 Orchestrator

Coordinate:

```text
validate
baseline
inject
observe
recover
complete
```

Do not make one giant function.

## E4 Timeline

Record important UTC events.

## E5 Recovery

Do not define recovery only as "pod Running."

Use application-level evidence.

---

# 14. Scoring Phases

First compute evidence:

```text
baseline availability
minimum availability
downtime
baseline/peak latency
baseline/peak error rate
fault time
alert time
detection time
recovery time
recovered yes/no
```

Then score.

Score must be:

```text
deterministic
documented
tested
explainable
```

AI must not calculate the official score.

---

# 15. Backend Common Errors

## Editor cannot import FastAPI

Check editor interpreter:

```text
apps/api/.venv/bin/python
```

## `.venv` broken after directory move

This already happened once.

Only if actually broken:

```bash
rm -rf .venv
uv sync
```

## Migration conflicts

One backend owner controls migration history.

Never initialize Alembic twice.

## Prometheus returns empty

Check:

```text
query
labels
time range
scrape
metric existence
```

Do not treat missing as zero.

## Experiment gets stuck

Determine layer:

```text
orchestrator
Litmus
Kubernetes
Prometheus
recovery rule
```

---

# 16. Backend Branch Examples

Backend teammate:

```text
feature/api-config
feature/api-domain
feature/api-state-machine
feature/api-persistence
feature/api-history
```

Project owner:

```text
feature/api-prometheus
feature/api-chaos-provider
feature/api-orchestrator
feature/api-recovery
feature/api-scoring
```

---

# 17. Shared Backend Handoff Rule

After any backend phase:

```text
Phase:
Author:
Branch:
Commit:
Files:
What works:
Tests:
Contract changed:
Known limitations:
Who consumes this:
Next backend phase:
```

The reviewer should understand this before merge.

---

# 18. Recommended AI Prompt - Backend Teammate

```text
Read the shared integration contract and shared backend guide.

I am the backend teammate.
My current phase is <PHASE>.

Before coding:
- ask for git status/current branch,
- inspect apps/api,
- inspect relevant files,
- do not create duplicate modules.

I primarily own backend foundation, domain, DB, persistence, CRUD/history/report foundations, and backend tests.
Do not implement Litmus/Prometheus/Kubernetes integration unless the project owner explicitly hands it to me.

Give 2-4 connected steps.
At completion verify, provide commit instructions, update the backend current-state file, and generate a phase handoff.
```

---

# 19. Recommended AI Prompt - Project Owner Backend Session

```text
Read the shared integration contract and shared backend guide.

I am the project owner.
My current backend phase is <PHASE>.

I own integration-heavy backend work: Kubernetes, Prometheus, Litmus/ChaosProvider, orchestration, recovery, analysis, scoring, and final integration.

Before coding:
- ask for git status/current branch,
- inspect existing backend files,
- inspect the latest backend handoff,
- do not duplicate files owned by the backend teammate.

Give 2-4 connected steps.
At completion verify, provide commit instructions, update current state, and generate a phase handoff.
```
