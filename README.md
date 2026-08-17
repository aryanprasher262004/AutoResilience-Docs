AutoResilience Documentation

Documentation repository for AutoResilience, an automated Chaos Engineering and resilience-testing platform for Kubernetes.

What is AutoResilience?

AutoResilience is a self-service platform for running controlled chaos experiments against Kubernetes workloads, observing how the target system behaves, measuring recovery, and producing an explainable Resilience Score.

The project uses existing chaos and observability tools for the infrastructure-level work, while AutoResilience focuses on the product layer around them:

experiment configuration and orchestration

safety and blast-radius validation

baseline measurement

live recovery monitoring

resilience scoring

historical comparison

reporting

CI/CD resilience gates

evidence-based recommendations

Core Product Flow

Select Target
    ↓
Configure Experiment
    ↓
Safety / Pre-flight Validation
    ↓
Capture Baseline
    ↓
Inject Controlled Failure
    ↓
Observe Impact
    ↓
Detect Recovery
    ↓
Calculate Resilience Score
    ↓
Generate Report / Recommendation

Current Scope

The current capstone release is focused on:

a single Kubernetes cluster

Kubernetes-hosted microservices

LitmusChaos-based fault injection

Prometheus-based metrics collection

Alertmanager-based alert verification

FastAPI orchestration backend

PostgreSQL persistence

React / Next.js frontend

CI/CD integration with GitHub Actions

Multi-cluster orchestration, production-grade SSO, automatic remediation, and non-Kubernetes environments are intentionally outside the initial scope.

Repository Purpose

This repository contains the understanding, design, operation, and evolution of AutoResilience.

The source-code repository is kept separate so that this repository can remain focused on:

architecture

terminology

design decisions

setup guides

experiment documentation

scoring methodology

product usage

engineering journey

screenshots and reports

Documentation Map

docs/
├── 01-project-overview.md
├── architecture/
│   ├── system-architecture.md
│   ├── experiment-lifecycle.md
│   └── observability.md
├── concepts/
│   ├── chaos-engineering.md
│   ├── resilience-score.md
│   ├── blast-radius.md
│   ├── steady-state.md
│   ├── recovery-time.md
│   └── service-risk.md
├── experiments/
├── scoring/
│   └── methodology.md
├── setup/
│   └── local-development.md
├── product/
│   └── user-guide.md
└── decisions/
    ├── ADR-001-litmuschaos.md
    └── ADR-002-single-cluster.md

Project Status

Status: Active development
Current phase: Environment setup and core architecture

The first technical milestone is intentionally small:

Run one reproducible Kubernetes chaos experiment end-to-end, observe the failure through Prometheus, detect recovery, and understand the complete lifecycle before automating additional fault types.

Architecture Principles

AutoResilience follows a few important rules:

Chaos execution must remain deterministic and safety-controlled.

The platform must distinguish its own failures from target-system resilience failures.

Unknown outcomes must never be reported as successful experiments.

The Resilience Score must be explainable and reproducible.

AI-generated recommendations, if added, must be backed by measured evidence.

The orchestrator should remain outside the target workload's failure domain.

The MVP should prioritize reliability and clarity over feature count.

Engineering Decisions

Important technical decisions are recorded as Architecture Decision Records (ADRs).

Each ADR should explain:

decision

context

alternatives considered

reason

trade-offs

consequences

This prevents architecture choices from becoming undocumented assumptions.

Development Journey

The journey/ directory records progress across the project:

journey/
├── week-01.md
├── week-02.md
└── ...

These entries should include:

what was attempted

what worked

what failed

what was learned

architecture changes

next milestone

Related Repository

The implementation repository contains the actual frontend, backend, Kubernetes, monitoring, chaos, and CI/CD code.

autoresilience/
├── apps/
├── chaos/
├── monitoring/
├── infra/
├── packages/
├── scripts/
└── tests/

Disclaimer

AutoResilience is currently being built and evaluated in a dedicated test environment. Chaos experiments should never be executed against production or important workloads without appropriate organizational approval, isolation, and safety controls.
