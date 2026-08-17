# AutoResilience - Revised Integration and Milestone Plan

## 1. Final Team Split

### Frontend teammate

Owns:

```text
apps/web/
```

### Backend teammate

Primarily owns deterministic backend core:

```text
configuration
routing
schemas
state machine
database
persistence
CRUD/history/report foundations
tests
```

### Project owner

Owns:

```text
all chaos/infrastructure
Prometheus/Litmus/Kubernetes integration
orchestration
recovery analysis
resilience scoring
final backend integration
```

This is the recommended load balance.

---

# 2. Why this split is better

The project owner already wants LitmusChaos.

Litmus + infrastructure + final integration is the hardest and least predictable work.

Therefore the backend teammate should not be used only as a helper.

They should independently deliver the deterministic backend foundation while the project owner reviews and later connects it to the real infrastructure.

The frontend teammate can work independently using mocks.

---

# 3. Difficulty

```text
Frontend               6.5/10
Backend Core           7.5/10
Chaos / Infrastructure 8.5/10
Final Integration      9/10
```

The project owner's combined workload is the highest.

If schedule slips, move additional deterministic backend tasks to the backend teammate before moving chaos ownership away from the project owner.

---

# 4. Dependency Flow

```text
Frontend mocks
      |
      | shared contract
      v
Backend domain/API
      |
      | adapter contracts
      v
Chaos/Prometheus/Kubernetes
```

Frontend can work early without real backend.

Backend core can work without real chaos.

Integration-heavy backend must wait until the chaos owner proves actual external behavior.

---

# 5. Mid-Sem - Approx. 1.5 Months

## Frontend teammate target

```text
design system
product shell
mock API layer
overview
experiment builder
live experiment room
```

## Backend teammate target

```text
B1 config
B2 routing
B3 domain schemas
B4 state machine
DB foundation
experiment persistence
basic history/API
tests
```

## Project owner target

```text
kind
Online Boutique
Prometheus
Alertmanager
Litmus
manual pod failure
repeatable pod template
Litmus integration handoff
initial Prometheus/backend adapter work
```

## Mid-Sem combined demo

Show:

```text
healthy target
functional frontend
real pod failure
real Prometheus/Kubernetes evidence
backend experiment/domain foundation
clear explanation of what is real vs mocked
```

---

# 6. Final - Approx. 3 Months

## Frontend

Add:

```text
history
experiment detail
score UI
service risk
reports
real backend integration
hardening
```

## Backend teammate

Add/support:

```text
history/filter endpoints
report data model/endpoints
API tests
DB migrations
persistence refinements
integration support
```

## Project owner

Add:

```text
preflight
baseline
orchestration
timeline
recovery detection
alert verification
multiple chaos types
analysis
score
full integration
CI resilience gate
evaluation support
```

---

# 7. Weekly Integration Rule

At least once per week:

```text
everyone pulls latest main
everyone explains current phase
shared contract changes are reviewed
small finished work is merged
blockers are identified
```

Do not wait for milestone week.

---

# 8. Merge Order

Recommended:

```text
1 shared contract/domain
2 stable chaos/observability environment
3 backend persistence/API
4 backend adapters/orchestration
5 frontend mock-to-real wiring
6 scoring/reporting
7 CI
8 evaluation/polish
```

---

# 9. Conflict Rule

If two people need the same file:

```text
coordinate before editing
```

Prefer one owner per file during a phase.

Do not solve semantic conflicts by blindly choosing Git "ours/theirs."

---

# 10. Final Rule

The workstream is not complete because:

```text
my branch works
```

It is complete when:

```text
merged main works
```
