# AutoResilience - Step 2 Team Execution Pack (Revised)

## What this pack is

This pack tells each person **what they own, where they should work, what order to work in, how hard their work is, and how their work later becomes one product**.

The project is now split like this:

### Role A - Frontend teammate

Owns the complete product frontend in:

```text
apps/web/
```

They can work almost independently by using realistic mock API responses that match the shared contract.

### Role B - Backend teammate + project owner together

The backend is shared, but **not by editing the same files at the same time**.

The recommended split is:

```text
Backend teammate:
configuration
API routing
domain schemas
state machine
database
persistence
CRUD/history/report endpoints
tests

Project owner:
reviews backend contracts
owns Prometheus/Kubernetes/Litmus adapters
owns orchestration
owns recovery analysis
owns resilience scoring
owns final backend integration
```

This keeps the backend teammate productive while the project owner still understands and controls the technically risky integration path.

### Role C - Project owner - Chaos / Infrastructure

The project owner fully owns:

```text
infra/
chaos/
monitoring/
```

This includes Kubernetes, LitmusChaos, Prometheus/Alertmanager setup, manual experiment proof, experiment templates, safety limits, and the handoff that the backend needs.

---

# Difficulty - Simple View

Approximate difficulty is based on implementation uncertainty, debugging difficulty, cross-system integration, and failure risk.

| Workstream | Difficulty | Why |
|---|---:|---|
| Frontend | 6.5/10 | Large amount of UI work, but it can be isolated with mocks and stable contracts. |
| Backend Core | 7.5/10 | Requires good API/domain/database design and testing, but is mostly deterministic application code. |
| Chaos / Infra | 8.5/10 | Kubernetes + Litmus + Prometheus + RBAC + laptop resource issues make failures less predictable. |
| Final Orchestration / Integration | 9/10 | Connects backend, Litmus, Kubernetes, metrics, recovery logic, scoring, and failure handling. |

Because the project owner wants to do LitmusChaos personally, **the backend teammate should take a substantial part of the deterministic backend core**. Otherwise the project owner will become the bottleneck.

---

# What Each File Does

## `00_STEP2_README.md`

Start here.

It explains:

- who does what,
- which work is hardest,
- how the team is split,
- which document each person should read next.

## `01_SHARED_INTEGRATION_CONTRACT.md`

This is the **agreement between everybody**.

It defines:

- shared folder ownership,
- API names,
- statuses,
- fault types,
- data shapes,
- Git rules,
- how frontend mocks match backend responses,
- what the backend expects from the chaos work.

If this file changes, all affected teammates need to know.

## `02_FRONTEND_TEAMMATE_EXECUTION_GUIDE.md`

This is the frontend teammate's A-to-Z guide.

It explains:

- what the final product UI should look and behave like,
- what libraries are recommended,
- what pages/features to build,
- how to use realistic mock APIs,
- how to later replace mocks with FastAPI,
- what branch to create,
- what common errors can happen,
- how to verify each frontend phase.

## `03_BACKEND_SHARED_EXECUTION_GUIDE.md`

This is for the project owner and backend teammate.

It explains:

- how two people can safely share backend work,
- which backend phases belong primarily to the teammate,
- which risky integration phases belong primarily to the project owner,
- which files should not be edited concurrently,
- what order the backend should be built in,
- how to test and hand off each backend phase.

## `04_CHAOS_INFRA_OWNER_EXECUTION_GUIDE.md`

This is for the project owner.

It explains:

- local Kubernetes setup,
- target microservices,
- Prometheus,
- Alertmanager,
- LitmusChaos,
- first manual pod failure,
- experiment templates,
- additional faults,
- safety and troubleshooting,
- what exact information must be handed to backend.

## `05_INTEGRATION_AND_MILESTONE_PLAN.md`

This is the team-wide timeline.

It explains:

- what should be ready by mid-sem,
- what should be ready by final,
- when workstreams must integrate,
- which teammate depends on which output,
- what order to merge things.

## `06_AI_SESSION_CONTEXT_PROTOCOL.md`

This is the interim no-context-loss guide.

It explains:

- what documents to attach when starting an AI chat,
- what opening message to send,
- what Markdown state file each person maintains,
- what to update before ending a session,
- how to resume in another AI model,
- what to do if credits end in the middle of a session.

A more polished final PDF/team operating guide will be created after Step 4.

---

# Important Working Rule

The team may work independently, but **do not keep three giant branches open until the final month**.

Use small feature branches:

```text
feature/web-overview
feature/api-experiment-domain
feature/chaos-pod-failure
```

When a phase is stable:

```text
feature branch
-> Pull Request
-> review
-> merge to main
```

Then the next phase starts from updated `main`.

That is how separate work becomes one product instead of three incompatible codebases.
