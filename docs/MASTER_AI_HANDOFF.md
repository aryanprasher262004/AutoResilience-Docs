# AutoResilience — Master AI Handoff and Execution Prompt

## Purpose of This Document

This document is the portable engineering context for the **AutoResilience** project.

It must allow any capable AI assistant — ChatGPT, Claude, Gemini, Cursor, Copilot, or another model — to continue development without requiring the developer to repeatedly explain the project.

When this document is supplied to an AI assistant, the developer should normally only need to say:

```text
Start Phase B3
```

or:

```text
Continue Phase F2
```

or:

```text
I am getting this error during Phase I3:
<paste error>
```

The assistant must use this document as the source of architectural context.

---

# 1. Project Identity

## Project Name

**AutoResilience**

## Project Type

Automated Kubernetes Chaos Engineering and Resilience Testing Platform.

## Core Objective

AutoResilience should allow a user to:

1. select a Kubernetes workload
2. configure a controlled failure
3. validate whether the experiment is safe
4. record the application's normal baseline
5. execute the experiment through LitmusChaos
6. observe application degradation
7. determine whether and when the application recovers
8. verify monitoring and alerting behavior
9. calculate an explainable Resilience Score
10. compare results with previous experiments
11. generate a resilience report
12. optionally use the result as a CI/CD quality gate

---

# 2. Product Philosophy

AutoResilience must NOT become only:

```text
React
  ↓
FastAPI
  ↓
LitmusChaos
  ↓
Grafana
```

That would primarily be integration work.

The intended product is:

```text
Target Discovery
      ↓
Experiment Configuration
      ↓
Safety Validation
      ↓
Baseline Measurement
      ↓
Controlled Chaos
      ↓
Live Evidence
      ↓
Recovery Analysis
      ↓
Explainable Resilience Score
      ↓
Historical Comparison
      ↓
Actionable Report
      ↓
CI/CD Reliability Gate
```

LitmusChaos performs fault injection.

Prometheus collects metrics.

Alertmanager provides alerting information.

Kubernetes hosts the workload.

AutoResilience provides the orchestration, safety, analysis, scoring, reporting, history, CI/CD integration, and product experience around those technologies.

---

# 3. Product Quality Goal

This is a university capstone project, but it should be engineered like an early-stage developer tool.

Prioritize:

```text
correctness
reproducibility
safety
understandability
maintainability
clear architecture
good UX
defensible measurements
```

over:

```text
large feature count
unnecessary enterprise architecture
AI-generated complexity
random integrations
visual effects without product value
```

---

# 4. Current Scope

## MVP

The initial product supports:

- one Kubernetes cluster
- Kubernetes workloads
- a realistic microservices target application
- LitmusChaos
- Prometheus
- Alertmanager
- FastAPI
- PostgreSQL
- Next.js / React / TypeScript
- GitHub Actions
- report generation
- experiment history
- explainable resilience scoring

Potential chaos scenarios:

- pod failure
- CPU stress
- memory stress
- network latency
- packet loss

Three reliable and well-evaluated scenarios are preferable to five unreliable ones.

## Explicitly Not Required for MVP

Do not introduce these unless the developer explicitly changes scope:

- multi-cluster orchestration
- multi-cloud support
- service mesh
- Kafka
- Redis
- Kubernetes operators written specifically for AutoResilience
- production SSO
- enterprise RBAC
- automated production remediation
- Terraform unless infrastructure requirements justify it
- machine-learning anomaly detection
- LLM-based score calculation

---

# 5. Main Technology Stack

## Frontend

```text
Next.js
React
TypeScript
Tailwind CSS
```

Preferred UI ecosystem when needed:

```text
shadcn/ui
Lucide
Recharts
TanStack Query
React Flow
```

Do not install every library immediately.

Dependencies should be introduced when the feature requiring them begins.

---

## Backend

```text
Python >= 3.12
FastAPI
Pydantic
pydantic-settings
SQLAlchemy
Alembic
psycopg
httpx
PostgreSQL
```

Dependency manager:

```text
uv
```

Development tools:

```text
pytest
pytest-asyncio
ruff
mypy
```

---

## Platform

```text
Docker
Kubernetes
kind or equivalent local cluster
kubectl
Helm
LitmusChaos
```

---

## Observability

```text
Prometheus
Alertmanager
Grafana
```

Grafana may be used for deep observability.

The AutoResilience product frontend should display curated experiment-specific metrics instead of simply embedding Grafana everywhere.

---

## CI/CD

```text
Git
GitHub
GitHub Actions
```

---

# 6. Repository Strategy

There are TWO repositories.

---

## Repository A — AutoResilience

Contains actual implementation.

Expected structure:

```text
AutoResilience/
│
├── README.md
├── .gitignore
│
├── apps/
│   ├── web/
│   └── api/
│
├── packages/
│   └── contracts/
│
├── chaos/
│   ├── templates/
│   └── policies/
│
├── monitoring/
│   ├── prometheus/
│   ├── alertmanager/
│   └── grafana/
│
├── infra/
│   ├── kind/
│   ├── helm/
│   └── namespaces/
│
├── scripts/
├── tests/
├── docs/
│
└── .github/
    └── workflows/
```

---

## Repository B — AutoResilience-Docs

Contains project knowledge.

Expected structure:

```text
AutoResilience-Docs/
│
├── README.md
│
├── docs/
│   ├── 01-project-overview.md
│   ├── architecture/
│   ├── concepts/
│   ├── decisions/
│   ├── experiments/
│   ├── product/
│   ├── scoring/
│   └── setup/
│
├── journey/
│
└── assets/
    ├── architecture/
    ├── diagrams/
    ├── screenshots/
    └── reports/
```

This Master AI Handoff document should preferably live at:

```text
AutoResilience-Docs/docs/MASTER_AI_HANDOFF.md
```

---

# 7. Critical Anti-Duplication Rule

Before creating ANY new file or directory, the AI assistant must first instruct the developer to check whether it already exists.

Preferred commands:

```bash
tree -L 4
```

or:

```bash
find . -maxdepth 4 -type f | sort
```

When working inside the backend:

```bash
find apps/api -maxdepth 5 -type f | sort
```

When working inside the frontend:

```bash
find apps/web -maxdepth 5 -type f | sort
```

The assistant must NEVER assume a file does not exist.

Before creating:

```text
app/core/config.py
```

the assistant should confirm that:

```text
apps/api/app/core/config.py
```

does not already contain relevant implementation.

If the file already exists:

```text
READ → UNDERSTAND → MODIFY
```

Do NOT recreate it.

---

# 8. Architecture Ownership Boundaries

To keep parallel development safe, respect these ownership boundaries.

## Frontend

Primary area:

```text
apps/web/
```

Frontend development should avoid modifying:

```text
apps/api/
chaos/
monitoring/
infra/
```

unless integration work is explicitly being performed.

---

## Backend

Primary area:

```text
apps/api/
packages/contracts/
```

Backend may eventually integrate:

```text
chaos/
monitoring/
```

but should not casually modify frontend components.

---

## Chaos / Infrastructure

Primary areas:

```text
chaos/
infra/
```

and selected monitoring configuration when required.

Do not modify application backend/frontend code unless coordination has occurred.

---

# 9. Contract-First Integration Principle

Parallel work must not depend on everyone waiting for everyone else.

Use stable contracts between modules.

Example:

Frontend should eventually call:

```text
GET /api/v1/experiments
POST /api/v1/experiments
GET /api/v1/experiments/{id}
```

Even before those APIs are implemented, frontend development may use mock data matching the expected response schema.

Later:

```text
MockDataProvider
      ↓
API Client
```

can replace mock responses without rebuilding the UI.

Similarly:

Backend should use an abstraction such as:

```text
ChaosProvider
```

instead of scattering Litmus-specific commands throughout the application.

This allows:

```text
Backend
   ↓
ChaosProvider
   ↓
LitmusChaos
```

---

# 10. Experiment Lifecycle

Every experiment requires an explicit lifecycle.

Initial model:

```text
CREATED
    ↓
VALIDATING
    ↓
BASELINING
    ↓
INJECTING
    ↓
OBSERVING
    ↓
RECOVERING
    ↓
COMPLETED
```

Possible exceptional states:

```text
VALIDATION_FAILED
INJECTION_FAILED
ABORTED
UNKNOWN
```

Do not simplify this to:

```text
running = true/false
```

The system must know what stage failed.

---

# 11. Platform Failure vs Target Failure

This distinction is mandatory.

Example target failure:

```text
Pod crashed
Application unavailable
Recovery took 32 seconds
```

This is valid resilience evidence.

Example platform failure:

```text
Prometheus unreachable
Litmus API failed
Kubernetes credentials invalid
Backend crashed
```

This must NOT reduce the target application's Resilience Score as though the application failed.

Represent platform failures separately.

---

# 12. Observability Model

Experiments are divided into:

```text
BASELINE
   ↓
FAILURE
   ↓
RECOVERY
```

Potential metrics:

```text
availability
latency
error rate
CPU
memory
pod state
alert state
detection time
recovery time
```

Each metric should have timestamps aligned with experiment events.

Important experiment events include:

```text
baseline_started
baseline_completed
chaos_requested
chaos_confirmed
fault_observed
alert_fired
chaos_stopped
recovery_detected
experiment_completed
```

---

# 13. Resilience Score

The scoring model is not finalized yet.

It will likely consider:

```text
availability
recovery performance
latency degradation
error behavior
alert effectiveness
```

Example UX only:

```text
Resilience Score        73 / 100

Availability            27 / 30
Recovery                18 / 30
Latency                 13 / 20
Errors                  11 / 15
Alerting                 4 / 5
```

The final formula must be:

- deterministic
- reproducible
- documented
- explainable
- based on measured evidence
- tested
- defensible academically

Do NOT let an AI invent arbitrary weights and treat them as scientifically valid.

---

# 14. Safety Model

Chaos experiments require safety validation.

Potential validations:

```text
cluster reachable
namespace exists
target workload exists
target healthy
monitoring working
Litmus available
valid experiment type
duration within limit
blast radius within limit
target replica count sufficient
no conflicting experiment
```

Unsafe requests must fail BEFORE fault injection.

---

# 15. AI Usage Policy

AI may later assist with:

## Experiment Copilot

Natural language:

```text
Test checkout by killing one replica for 45 seconds.
```

↓

structured experiment proposal.

But execution still requires deterministic validation.

---

## Resilience Analyst

Receives structured experiment results and explains them.

AI may generate:

- plain-language findings
- evidence-backed recommendations
- suggested next experiments

AI may NOT:

- execute chaos directly
- bypass safety checks
- calculate the authoritative Resilience Score
- fabricate metrics
- mark experiments successful
- modify infrastructure without explicit action

AI features are optional and should be implemented only after the core product works.

---

# 16. Development Milestones

There are three academic stages.

## Stage 0 — Synopsis

Status:

```text
DONE
```

Includes:

- project selection
- problem statement
- objectives
- proposed architecture
- technology stack
- methodology
- expected results
- project plan

---

# 17. Stage 1 — Mid-Semester Target

Approximate remaining window:

```text
~1.5 months
```

The goal is NOT to finish the complete product.

The mid-sem system should prove that the architecture works.

Required outcome:

```text
Kubernetes
   ↓
Target Application
   ↓
Monitoring
   ↓
LitmusChaos
   ↓
Controlled Failure
   ↓
Observable Impact
   ↓
FastAPI Orchestration Foundation
```

The team should be able to demonstrate at least one working chaos scenario.

---

# 18. Stage 2 — Final Target

Approximate project horizon:

```text
~3 months
```

Expected:

- multiple experiments
- safety validation
- baseline capture
- Prometheus integration
- alert verification
- recovery analysis
- Resilience Score
- experiment persistence
- polished frontend
- history
- reports
- comparisons
- CI/CD gate
- testing
- evaluation
- documentation
- final demo

---

# 19. Master Phase Index

Use these phase names when talking to an AI assistant.

---

# FOUNDATION

## F0 — Repository Foundation

Status:

```text
MOSTLY COMPLETE
```

Includes:

- source repository
- documentation repository
- basic folder structure
- `.gitignore`
- Git conventions
- frontend scaffold
- backend scaffold

---

## F1 — Backend Python Environment

Status:

```text
COMPLETE
```

Completed:

```text
uv installed
pyproject.toml created
uv.lock created
.venv working
FastAPI installed
FastAPI CLI working
editor interpreter corrected
```

---

## F2 — Backend Health API

Status:

```text
COMPLETE
```

Current endpoint:

```text
GET /health
```

Expected response:

```json
{
  "status": "ok"
}
```

Swagger works at:

```text
/docs
```

---

# BACKEND FOUNDATION

## B1 — Configuration & Environment Management

Status:

```text
CURRENT PHASE
```

Goal:

Introduce centralized application configuration.

Expected concepts:

```text
pydantic-settings
.env
.env.example
environment variables
typed settings
development configuration
```

Expected location:

```text
apps/api/app/core/config.py
```

Do NOT commit real `.env`.

Commit:

```text
.env.example
```

Typical future configuration fields:

```text
APP_NAME
ENVIRONMENT
DATABASE_URL
PROMETHEUS_URL
```

Litmus/Kubernetes fields should only be added when required.

Completion criteria:

- typed settings class exists
- `.env` loading works
- `.env.example` documented
- real `.env` ignored
- application uses settings
- test/startup still works
- changes committed

---

## B2 — API Structure & Versioning

Goal:

Establish:

```text
/api/v1
```

and structured route registration.

Potential structure:

```text
app/
├── api/
│   ├── router.py
│   └── routes/
├── core/
├── models/
├── schemas/
├── services/
└── main.py
```

Do not create empty architecture for hypothetical features.

Only introduce directories when useful.

---

## B3 — Experiment Domain Model

Goal:

Define what an AutoResilience experiment actually is.

Likely concepts:

```text
Experiment
ExperimentStatus
FaultType
ExperimentTarget
ExperimentConfig
ExperimentResult
```

Define Pydantic schemas before database implementation.

Include validation.

---

## B4 — Experiment State Machine

Goal:

Implement lifecycle transitions.

Example:

```text
CREATED → VALIDATING → BASELINING → ...
```

Invalid transitions should be rejected.

State management must be testable independently.

---

# DATABASE

## D1 — PostgreSQL Local Environment

Goal:

Run PostgreSQL reproducibly.

Prefer Docker Compose/local container rather than requiring manual PostgreSQL installation.

---

## D2 — SQLAlchemy Setup

Goal:

Create:

```text
engine
session factory
Base
database configuration
```

---

## D3 — Alembic

Goal:

Introduce database migrations.

Never manually modify database production schema without migration history.

---

## D4 — Experiment Persistence

Persist:

```text
experiment configuration
timestamps
states
result metadata
score
```

Do not store high-volume Prometheus raw time series blindly in PostgreSQL unless justified.

---

# KUBERNETES

## K1 — Local Cluster

Goal:

Create reproducible local Kubernetes environment.

Preferred:

```text
kind
```

Store reproducible config under:

```text
infra/kind/
```

---

## K2 — Target Application

Goal:

Deploy realistic microservices application.

Expected candidate:

```text
Google Online Boutique
```

Verify:

```text
pods healthy
services reachable
application usable
```

---

## K3 — Kubernetes Access from Developer Machine

Verify:

```text
kubectl cluster-info
kubectl get nodes
kubectl get pods -A
```

Document context usage.

---

# OBSERVABILITY

## O1 — Prometheus Deployment

Goal:

Prometheus successfully collects cluster/application metrics.

---

## O2 — Application Metrics

Determine which metrics AutoResilience requires.

Do not collect metrics simply because they exist.

---

## O3 — Prometheus Query Validation

Manually prove queries for:

```text
availability
latency
errors
resource use
pod health
```

before integrating them into FastAPI.

---

## O4 — Alertmanager

Create/verify useful alerting.

Measure:

```text
fault timestamp
alert timestamp
detection delay
```

---

## O5 — Backend Prometheus Client

Backend should query Prometheus through a dedicated client/service abstraction.

Do not scatter HTTP requests across API routes.

---

# CHAOS ENGINE

## C1 — LitmusChaos Installation

Install and verify LitmusChaos.

Document exact installation procedure.

---

## C2 — Manual Pod Failure

This is the FIRST critical chaos milestone.

Run manually.

Document:

```text
target
experiment configuration
fault start
observable effect
Kubernetes reaction
recovery
```

Do not automate until this works reliably.

---

## C3 — Experiment Templates

Store standardized experiment definitions under:

```text
chaos/templates/
```

---

## C4 — Safety Policies

Store application safety rules under:

```text
chaos/policies/
```

---

## C5 — Backend Chaos Provider

Implement abstraction:

```text
ChaosProvider
```

with Litmus implementation behind it.

Backend business logic should not become tightly coupled to shell commands.

---

## C6 — Additional Fault Types

Only after pod failure is stable.

Candidates:

```text
CPU
memory
network latency
packet loss
```

---

# EXPERIMENT ORCHESTRATION

## E1 — Preflight Validator

Validate environment before experiment.

---

## E2 — Baseline Collector

Capture baseline window.

---

## E3 — Chaos Execution

Trigger validated experiment.

---

## E4 — Observation Window

Monitor target after fault injection.

---

## E5 — Recovery Detector

Define what "recovered" mathematically means.

Do not rely purely on "pod is Running."

---

## E6 — Experiment Timeline

Store timestamps/events throughout lifecycle.

---

# SCORING

## S1 — Metric Normalization

Define how metrics become comparable scoring components.

---

## S2 — Score Formula Research

Use credible references and experimental reasoning.

Clearly distinguish academic evidence from project-specific design choices.

---

## S3 — Score Engine

Implement deterministic scoring.

---

## S4 — Score Explanation

Return:

```text
component
points
maximum
reason for deduction
evidence
```

---

## S5 — Score Validation

Repeat experiments and examine variance.

---

# FRONTEND

## UI1 — Design System

Establish:

```text
spacing
typography
colors
cards
tables
statuses
forms
charts
navigation
```

Avoid generic AI-dashboard styling.

---

## UI2 — Product Shell

Navigation:

```text
Overview
Experiments
Services
Templates
Reports
CI Gates
Settings
```

---

## UI3 — Overview Dashboard

Use meaningful mock data until backend APIs are ready.

---

## UI4 — Experiment Builder

Wizard:

```text
Target
→ Fault
→ Safety
→ Baseline
→ Review
```

---

## UI5 — Live Experiment Room

High-priority screen.

Show:

```text
experiment state
metrics
events
fault timestamp
alert timestamp
recovery
abort
```

---

## UI6 — Experiment History

Filtering/search/history.

---

## UI7 — Experiment Details

Complete report for individual run.

---

## UI8 — Explainable Score

Score decomposition and evidence.

---

## UI9 — Service Risk View

Potential topology/heatmap views.

---

## UI10 — Reports

Product-quality report UX.

---

## UI11 — Backend Integration

Replace mock provider with real API client.

Do not rewrite visual components when switching to real data.

---

# CI/CD

## CI1 — Basic GitHub Actions

Check:

```text
frontend lint/build
backend lint
backend tests
```

---

## CI2 — Branch Protection

Require:

```text
PR
approval
successful checks
resolved conversations
```

---

## CI3 — Resilience Quality Gate

Late-stage feature.

Example:

```text
deployment
↓
resilience experiment
↓
score
↓
pass/fail gate
```

Do not implement before scoring is stable.

---

# REPORTING

## R1 — Result Model

Define report data independently from PDF rendering.

---

## R2 — In-App Report

Primary implementation.

---

## R3 — Export

PDF can be added after report data is stable.

---

# AI

## A1 — Resilience Analyst

Optional.

Only after scoring and analysis are reliable.

---

## A2 — Experiment Copilot

Optional.

Only after experiment schema and safety validator are stable.

---

# TESTING & EVALUATION

## T1 — Unit Tests

Priority targets:

```text
configuration
state machine
validators
recovery logic
score
```

---

## T2 — API Tests

Test critical endpoints.

---

## T3 — Integration Tests

Test subsystem boundaries.

---

## T4 — Repeated Chaos Experiments

Perform repeated runs.

---

## T5 — Evaluation Dataset

Store experimental results systematically.

---

## T6 — Final Evaluation

Analyze:

```text
consistency
variance
recovery
score behavior
failure-type differences
```

---

# DOCUMENTATION

## DOC1 — Architecture Documentation

Maintain diagrams and flows.

---

## DOC2 — ADRs

Record important decisions.

Naming:

```text
ADR-001-...
ADR-002-...
```

---

## DOC3 — Experiment Documentation

Every supported experiment should explain:

```text
purpose
target
configuration
expected effect
metrics
risk
recovery condition
```

---

## DOC4 — Setup Guide

A new teammate should be able to run the project.

---

## DOC5 — Journey Log

Record meaningful milestones.

Not every coding session requires a LinkedIn post.

---

## DOC6 — Product User Guide

Explain how an eventual user operates AutoResilience.

---

# 20. Dependencies Between Major Phases

Critical dependency graph:

```text
Environment
    ↓
Kubernetes
    ↓
Target Application
    ↓
Observability
    ↓
Manual Chaos
    ↓
Backend Integration
    ↓
Automated Experiment Lifecycle
    ↓
Scoring
    ↓
Reports
    ↓
CI Gate
```

Frontend can proceed in parallel using mock contracts.

Database work can proceed once Experiment schemas stabilize.

---

# 21. Mid-Semester Recommended Completion

By mid-semester, prioritize:

```text
F0-F2
B1-B4
K1-K3
O1-O3
C1-C2
basic frontend product shell
at least one experiment workflow
basic CI
documentation
```

It is acceptable if advanced scoring/reporting is incomplete.

The demonstration should prove real technical integration.

---

# 22. Final Semester Completion

Before final evaluation, target:

```text
complete experiment orchestration
multiple chaos types
baseline/recovery analysis
database persistence
historical experiments
Resilience Score
reports
polished frontend
alert verification
comparison/evaluation
GitHub Actions
documentation
```

Optional AI features come last.

---

# 23. Git Rules

## Never Develop Major Features Directly on Main

Preferred:

```bash
git checkout main
git pull origin main
git checkout -b feature/<name>
```

Examples:

```text
feature/backend-config
feature/experiment-state-machine
feature/frontend-dashboard
feature/litmus-pod-failure
feature/prometheus-integration
```

---

# 24. Commit Convention

Use Conventional Commit-style prefixes.

```text
feat:
fix:
docs:
test:
refactor:
chore:
ci:
```

Better:

```text
feat(api): add experiment creation endpoint

feat(web): implement experiment builder

feat(chaos): add pod failure experiment

fix(metrics): handle missing Prometheus samples

test(scoring): add recovery penalty cases

docs: document pod failure experiment

ci: add backend test workflow
```

Avoid:

```text
updates
final code
changes
working version
new stuff
```

---

# 25. Before Every Commit

Run:

```bash
git status
```

Review files.

Never accidentally commit:

```text
.env
.venv/
node_modules/
credentials
kubeconfig
secrets
generated build directories
```

Then:

```bash
git add <specific-files>
git status
git commit -m "..."
```

---

# 26. Sync Rule

Before starting a new branch:

```bash
git checkout main
git pull origin main
git checkout -b feature/<feature>
```

If remote moved ahead while local work exists, prefer understanding the state before using force operations.

Never suggest:

```bash
git push --force
```

as a normal solution.

---

# 27. AI Assistant Operating Instructions

When this document is provided to an AI assistant, follow these rules.

## Rule 1 — Identify Requested Phase

If developer says:

```text
Start B1
```

find:

```text
B1 — Configuration & Environment Management
```

and work only on that feature.

---

## Rule 2 — Inspect Before Modifying

Before creating files, request:

```bash
git status
```

and an appropriate tree/find command.

Example:

```bash
find apps/api -maxdepth 5 -type f | sort
```

Use the result to avoid duplicate files.

---

## Rule 3 — Explain New Concepts

When introducing something such as:

```text
Alembic
dependency injection
Repository pattern
React Query
Prometheus range query
```

briefly explain:

```text
what it is
why AutoResilience needs it
why it is being introduced now
```

Then proceed.

---

## Rule 4 — Give 2–4 Connected Steps

The developer prefers incremental work.

Avoid:

```text
Step 1 through Step 37
```

Also avoid unnecessarily requiring a new response after every trivial command.

Preferred:

```text
Why

Step 1
Step 2
Step 3

Expected result
```

Wait for developer output.

---

## Rule 5 — Troubleshoot Scientifically

If an error occurs:

1. read the exact error
2. identify likely layer
3. inspect current state
4. test the smallest hypothesis
5. apply targeted correction

Do not immediately recommend:

```text
reinstall everything
delete repository
force push
switch technologies
```

---

## Rule 6 — Four-Pass Quality Process

For important architectural or implementation work:

### Generate

Create proposed solution.

### Specialist Review

Review as appropriate expert:

```text
SRE
backend engineer
frontend engineer
Kubernetes engineer
database engineer
security engineer
```

### Verify

Check:

```text
correctness
compatibility
edge cases
dependencies
```

Use authoritative documentation when information may have changed.

### Correct

Return corrected implementation.

---

## Rule 7 — Don't Overengineer

Before introducing a new:

```text
service
database
message queue
framework
abstraction
agent
deployment tool
```

ask:

> What specific current problem does this solve?

If there is no strong answer, don't introduce it.

---

# 28. Feature Completion Protocol

A phase is complete only when:

```text
implementation works
basic verification passes
tests appropriate for the phase pass
relevant documentation updated
Git status reviewed
work committed
```

Then the AI should say:

```text
PHASE B1 COMPLETE
```

and provide:

```text
What was implemented
Files changed
How it works
Commands used
Verification performed
Commit made
Next recommended phase
```

---

# 29. Portable Continuation Record

At the end of EVERY completed phase, generate a small handoff record.

Format:

```text
AUTORESILIENCE PHASE HANDOFF

Completed Phase:
B1 — Configuration & Environment

Branch:
feature/backend-config

Commit:
<commit hash if available>

Files Created:
...

Files Modified:
...

What Works:
...

Important Decisions:
...

Known Limitations:
...

Next Recommended Phase:
B2 — API Structure & Versioning
```

Save important handoffs under:

```text
AutoResilience-Docs/journey/
```

or update this master document's status section.

This means another AI can recover project state even if the previous conversation is unavailable.

---

# 30. Conversation Length Control

Do not let one AI conversation become the permanent project thread.

Recommended:

```text
one chat = one major phase
```

Examples:

```text
Backend — Config Management
Backend — Experiment Domain
Kubernetes — Local Cluster
Observability — Prometheus
Chaos — Pod Failure
Frontend — Experiment Builder
```

When the current phase is completed, the AI should explicitly say:

```text
This phase has reached a clean stopping point.

Start a new chat titled:
<recommended title>

Paste:
<short phase continuation prompt>
```

---

# 31. New Chat Continuation Prompt

When beginning a new chat, the developer can paste this Master AI Handoff document and then say:

```text
PROJECT: AutoResilience

PHASE: B1 — Configuration & Environment

Work only on this phase.

Give me 2–4 connected steps at a time.
Inspect existing files before creating anything.
Explain important concepts.
Do not redesign the architecture unless a genuine technical issue requires it.
When the phase is complete, verify it, tell me what to commit, and generate the next-phase handoff.
```

---

# 32. Ultra-Short Usage

If the AI already has this document loaded, the developer should be able to write only:

```text
Start B1.
```

or:

```text
Continue C2.
```

or:

```text
Phase O3. I get this error:

<paste error>
```

The AI should infer the rest from this document.

---

# 33. Current Actual Project State

## Repositories

Created:

```text
AutoResilience
AutoResilience-Docs
```

---

## Frontend

Next.js frontend created at:

```text
apps/web/
```

It currently contains the default scaffold.

No production AutoResilience UI has been implemented yet.

---

## Backend

Backend:

```text
apps/api/
```

Current Python:

```text
>=3.12
```

Dependency management:

```text
uv
```

Current `pyproject.toml` contains approximately:

```text
FastAPI
SQLAlchemy
Alembic
psycopg
pydantic-settings
httpx
pytest
pytest-asyncio
ruff
mypy
```

---

## Backend Environment Issue Already Solved

Backend was accidentally created at:

```text
apps/web/apps/api
```

then moved to:

```text
apps/api
```

This broke `.venv` interpreter paths.

Solution already performed:

```bash
rm -rf .venv
uv sync
```

Current interpreter correctly points to:

```text
apps/api/.venv/bin/python3
```

Do not repeat this fix unless the environment actually becomes invalid again.

---

## FastAPI

Working.

Current command:

```bash
uv run fastapi dev app/main.py
```

Swagger:

```text
http://127.0.0.1:8000/docs
```

Health:

```text
GET /health
```

returns:

```json
{
  "status": "ok"
}
```

---

## Current Backend Structure

Approximately:

```text
apps/api/app/
├── __init__.py
├── main.py
├── api/
│   ├── __init__.py
│   └── routes/
│       ├── __init__.py
│       └── health.py
└── core/
    └── __init__.py
```

---

# 34. Current Phase

Resume development at:

```text
B1 — Configuration & Environment Management
```

`pydantic-settings` is already installed.

Planned location:

```text
apps/api/app/core/config.py
```

Before creating it, inspect current files.

The immediate objectives are:

```text
centralized Settings class
.env loading
.env.example
safe secret handling
FastAPI uses settings
configuration verification
commit
```

Do NOT jump to PostgreSQL yet.

---

# 35. Current Git Approach

Existing project scaffolding has already been committed.

Do not recreate the project.

Before continuing:

```bash
git status
git branch --show-current
git pull
```

Prefer a feature branch for new bounded work.

For B1, recommended:

```text
feature/backend-config
```

---

# 36. Documentation Status

Documentation repo has:

```text
README.md
docs/01-project-overview.md
```

and the main scaffold directories.

Continue documenting meaningful decisions and milestones.

Do not spend development time writing documentation that adds no useful information.

---

# 37. Developer Learning Requirement

The developer wants to understand the entire system even when teammates implement portions.

When explaining completed work, include:

```text
What problem this code solves
Where it sits in the architecture
Input
Output
Dependencies
Failure modes
How to test it
How to debug it
```

Do not only provide code.

---

# 38. Golden Rule

Every engineering decision should help answer:

> How does this help AutoResilience safely run, observe, analyze, or explain a resilience experiment?

If it doesn't meaningfully contribute to that goal, question whether it belongs in the MVP.

---

# END OF MASTER AI HANDOFF