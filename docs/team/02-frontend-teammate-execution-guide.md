# AutoResilience - Frontend Teammate Execution Guide (Expanded)

## In simple words - what your job is

You are building the complete website/product interface that a user will actually interact with.

You are **not waiting for the backend**.

While backend/chaos are still being built, your frontend will behave like a real product using realistic mock API responses. Later we replace those mock responses with the real FastAPI endpoints.

Your job is successful if:

```text
the UI looks finished
the workflows are clickable
forms validate
charts use coherent data
experiment states change
loading/error/empty states work
and switching from mock data to real backend data does not require rebuilding the pages
```

The most important screens are:

```text
Overview
Experiment Builder
Live Experiment Room
Experiment History
Experiment Detail
Explainable Score
Service Risk
Reports
```

## What not to do

Do not build a "college dashboard" made only of random stat cards.

Do not hard-code data directly into every page.

Do not create a giant frontend that depends on Litmus or Prometheus.

The frontend only understands AutoResilience API/product objects.

---


## 1. Mission

You own the product frontend.

Primary ownership:

```text
apps/web/
```

Your job is to build a usable developer/SRE product interface that works convincingly with mock API responses during parallel development and later connects to the FastAPI backend without a major rewrite.

Read `01_SHARED_INTEGRATION_CONTRACT.md` before starting.

---

# 2. Non-Negotiable Requirements

The frontend must:

- look like a real developer tool,
- be functional before real backend data is available,
- use realistic placeholder/mock data,
- include loading, empty, error, running, completed, failed, and unknown states,
- avoid directly depending on LitmusChaos or Prometheus,
- consume AutoResilience product-level data contracts,
- be responsive for laptop/demo resolutions,
- use accessible semantic HTML,
- keep terminology consistent,
- allow mock API responses to be replaced by real FastAPI responses later.

The frontend must NOT:

- directly call Litmus,
- directly call Prometheus,
- embed Grafana as the whole product,
- hard-code data throughout components,
- create dashboards only to fill space,
- use excessive gradients/glassmorphism/glowing cards,
- create meaningless charts,
- invent backend fields without updating the shared contract.

---

# 3. Branch Plan

Use one branch per frontend subphase.

Examples:

```text
feature/web-design-system
feature/web-product-shell
feature/web-overview
feature/web-experiment-builder
feature/web-live-experiment
feature/web-history
feature/web-reports
feature/web-api-integration
```

Start from current main:

```bash
git checkout main
git pull origin main
git checkout -b feature/web-<phase>
```

---

# 4. First Session - Inspect, Do Not Re-Scaffold

The Next.js application already exists.

Do NOT run `create-next-app` again.

Inspect:

```bash
git status
git branch --show-current
cat apps/web/package.json
find apps/web/src -maxdepth 5 -type f | sort
```

Inspect existing:

```text
apps/web/src/app/layout.tsx
apps/web/src/app/page.tsx
apps/web/src/app/globals.css
```

Do not destroy existing work unless you understand it.

---

# 5. Recommended Frontend Libraries

Install only when the relevant phase begins.

## shadcn/ui

Use for quality primitives such as:

```text
Button
Card
Dialog
Sheet
Tabs
Table
Select
Dropdown
Tooltip
Badge
Skeleton
Alert
Command
```

Customize the components. Do not ship an untouched demo look.

## Lucide React

Use one consistent icon family.

## TanStack Query

Use for server/API state:

```text
queries
mutations
cache
refetching
polling
```

Do not place server state into random React contexts.

## Recharts

Use for:

```text
latency
availability
error rate
score trend
experiment comparison
```

Prefer simple readable charts.

## React Flow

Use only when implementing the service topology/risk view.

Do not install it during the first UI phase unless needed.

## React Hook Form + Zod

Use for Experiment Builder:

```text
React Hook Form -> form state
Zod -> validation/schema
```

## Mock Service Worker (MSW)

Use MSW to keep the frontend independent from backend completion.

The UI makes normal HTTP requests to the expected AutoResilience API. MSW intercepts them in mock mode.

Later:

```text
mock mode OFF
FastAPI ON
```

The page components should not need rewriting.

---

# 6. Suggested Frontend Structure

Do not create all files at once.

Target structure as features appear:

```text
apps/web/src/
|
|-- app/
|   |-- layout.tsx
|   |-- page.tsx
|   `-- (dashboard)/
|       |-- overview/
|       |-- experiments/
|       |   |-- page.tsx
|       |   |-- new/
|       |   `-- [id]/
|       |-- services/
|       |-- reports/
|       |-- ci-gates/
|       `-- settings/
|
|-- components/
|   |-- ui/
|   |-- layout/
|   |-- charts/
|   |-- experiments/
|   |-- resilience/
|   `-- services/
|
|-- lib/
|   |-- api/
|   |-- mocks/
|   |-- validation/
|   `-- utils/
|
|-- hooks/
`-- types/
```

Responsibilities:

```text
components/ui/       reusable design primitives
components/charts/   chart wrappers
components/experiments/ experiment-specific UI
lib/api/             API request functions
lib/mocks/           mock handlers/fixtures
types/               product domain types
```

---

# 7. Design Direction - Avoid AI Slop

Aim for:

```text
clean
professional
engineering-focused
restrained palette
clear hierarchy
high readability
purposeful status colors
consistent spacing
```

Avoid:

```text
purple gradients everywhere
glass cards everywhere
giant hero sections in the dashboard
random glowing icons
huge empty areas
every value in an identical stat card
meaningless doughnut charts
fake AI widgets
```

Use a consistent spacing scale and type hierarchy.

Statuses should be easy to scan.

Example semantic status families:

```text
healthy / completed
running / observing
warning / degraded
failed
unknown
neutral
```

Do not rely only on color; pair status colors with text/icons.

---

# 8. Navigation

Persistent left navigation:

```text
Overview
Experiments
Services
Templates
Reports
CI Gates
Settings
```

Top bar:

```text
AutoResilience
current cluster/environment
cluster health
```

---

# 9. Core Screens

## Overview

Should answer:

- Is the connected environment healthy?
- What is the recent resilience trend?
- Which services appear weakest?
- Which experiments recently failed?
- What should the user investigate?

Possible sections:

```text
Overall Resilience
Experiment Count
Failed Checks
Resilience Trend
Service Risk
Recent Experiments
```

## Experiment Builder

Wizard:

```text
1 Target
2 Fault
3 Safety
4 Conditions
5 Review
```

Inputs may include:

```text
namespace
workload
fault type
duration
affected replicas
optional load generation
```

Show blast-radius estimate.

Do not expose raw YAML as the primary UI.

## Live Experiment Room

Highest-value screen.

Show:

```text
experiment name
target
current state
elapsed time
abort

baseline marker
fault-injection marker
alert marker
recovery marker

availability
p95 latency
error rate

event timeline
recovery status
provisional score if supplied
```

Mock run should visibly progress.

Example timeline:

```text
10:42:03 Chaos requested
10:42:05 Fault confirmed
10:42:07 Target degraded
10:42:10 Alert fired
10:42:25 Replica restored
10:42:31 Recovery detected
```

## Experiment History

Support:

```text
search
fault filter
status filter
date filter
score
target
```

## Experiment Detail

Show:

```text
configuration
timeline
baseline vs failure vs recovery
metrics
score
score explanation
alerts
issues
recommendations
```

## Service Risk

Build in this order:

1. service risk table,
2. service x fault heatmap,
3. topology graph only if useful/time permits.

## Reports

Use the same result data as experiment detail.

Do not create separate fake report state.

---

# 10. Mock Data Strategy

Mocks must be realistic and internally consistent.

Create scenarios:

```text
fast successful recovery
slow recovery
alert missing
injection failed
unknown outcome
high network latency impact
```

Bad mock:

```text
score 95
availability 20%
never recovered
status HEALTHY
```

Good mock records tell a coherent story.

---

# 11. UI0 - Design System

## Goal

Establish consistent visual rules.

Tasks:

1. inspect current scaffold,
2. set design tokens,
3. initialize shadcn only if needed,
4. define typography,
5. define spacing/radius,
6. define canonical status badge mapping,
7. create layout primitives.

Likely files:

```text
src/app/globals.css
src/components/ui/*
src/components/layout/*
src/lib/utils/*
```

Verification:

```bash
cd apps/web
npm run lint
npm run build
```

Completion:

- consistent primitives,
- clean build,
- responsive base layout.

Suggested AI prompt:

```text
We are in AutoResilience Frontend Phase UI0.
Read the shared integration contract.
Inspect package.json and existing src files first.
Help me establish a restrained developer-tool design system using the existing Next.js/Tailwind project.
Do not build full product pages yet.
```

---

# 12. UI1 - Product Shell + Mock Infrastructure

## Goal

Make the application feel like AutoResilience.

Tasks:

1. sidebar,
2. top environment bar,
3. dashboard route layout,
4. product TypeScript types,
5. API request layer,
6. MSW infrastructure,
7. realistic mock scenarios.

Possible files:

```text
src/components/layout/app-sidebar.tsx
src/components/layout/top-bar.tsx
src/lib/api/client.ts
src/lib/mocks/handlers.ts
src/lib/mocks/data/*
src/types/*
```

Completion:

- navigation works,
- mock HTTP requests work,
- UI does not know whether backend is mock or real.

---

# 13. UI2 - Overview

Implement:

```text
cluster status
overall score
experiment count
failed checks
trend
service risk summary
recent experiments
```

Required states:

```text
loading
success
empty
error
```

Data must come through the API/mock layer.

---

# 14. UI3 - Experiment Builder

Use:

```text
React Hook Form
Zod
```

Validation examples:

```text
duration > 0
affected replicas >= 1
namespace required
workload required
known fault type
```

Frontend safety display is informational.

Backend remains authoritative.

Completion:

- complete wizard,
- validation,
- mock create request,
- redirect to live experiment.

---

# 15. UI4 - Live Experiment Room

Use mock polling/state transitions initially.

Display:

```text
experiment lifecycle
metrics
timeline
current status
abort
recovery
```

Completion:

- mock run changes state over time,
- charts update,
- timeline grows,
- abort works,
- failure/unknown can be demonstrated.

This must be functional, not a static screen.

---

# 16. Mid-Sem Frontend Gate

Ideally completed:

```text
design system
product shell
mock API
overview
experiment builder
live experiment room
```

It should be demoable without backend completion.

---

# 17. UI5 - History + Detail

Implement:

```text
history table
filters
detail page
timeline
metric comparison
score section
```

---

# 18. UI6 - Explainable Score

Display:

```text
total
component scores
maximums
deductions
reason
evidence
comparison with prior equivalent run
```

Frontend never calculates authoritative score.

---

# 19. UI7 - Service Risk

First:

```text
service risk table
fault heatmap
```

Optional later:

```text
React Flow topology
```

Topology must communicate useful relationships/risk.

---

# 20. UI8 - Reports + CI Gates

Reports:

```text
experiment summary
metrics
score
weaknesses
recommendations
```

CI Gates:

Keep initial UI simple and truthful.

Do not fake enterprise deployment management.

---

# 21. UI9 - Real Backend Integration

Replace mocks incrementally:

```text
1 /health
2 targets
3 experiment list
4 experiment detail
5 create experiment
6 active metrics/state
7 score/report
8 service risk
```

After each:

```text
disable corresponding mock
test real backend
test backend unavailable
test error response
```

Use TanStack Query.

Start with polling for live experiment updates.

Only add WebSockets/SSE if backend truly provides them and polling is insufficient.

---

# 22. UI10 - Product Hardening

Test:

```text
loading
slow API
empty
400
404
422
500
backend unavailable
UNKNOWN experiment
long names
many rows
small laptop
```

Add:

```text
empty states
error recovery
skeletons
useful tooltips
accessible labels
keyboard-friendly controls
```

---

# 23. Common Frontend Problems

## Hydration Errors

Likely causes:

- browser-only state differs from server render,
- random/time-based values rendered server-side,
- client-only library used incorrectly.

Fix by understanding Server/Client Component boundaries. Do not put `"use client"` everywhere.

## Chart Has Zero Width

Check parent sizing, flex/grid behavior, and actual responsive dimensions.

## TypeScript Mismatch

Do not silence with `any`.

Compare response with shared contract.

## CORS

During real integration this is generally coordinated with backend. Do not bypass browser security.

## MSW Not Intercepting

Check worker setup, mock mode, handler URL, and browser network logs.

## shadcn Looks Generic

Customize density, typography, navigation, tables, and status language.

---

# 24. Frontend Checks

Before commit:

```bash
cd apps/web
npm run lint
npm run build
cd ../..
git status
```

Commit example:

```bash
git add apps/web
git status
git commit -m "feat(web): implement experiment builder"
```

---

# 25. Definition of Done

Frontend is final-ready when:

- all major screens exist,
- critical screens are functional,
- mock layer is isolated,
- finalized real APIs work,
- no major TypeScript/build errors,
- loading/error/empty states exist,
- charts communicate clearly,
- canonical statuses are respected,
- UI never directly depends on Litmus/Prometheus,
- the full user journey can be demonstrated without editing code live.


# Additional Frontend Quality Review Before Final Merge

Review every major screen for:

```text
Does this screen answer a real user question?
Is the most important action obvious?
Are status/error messages understandable?
Does mock data tell a coherent story?
Are destructive actions clearly different?
Can a user understand baseline -> failure -> recovery?
Can a user tell platform failure from target failure?
Are charts readable without hovering every point?
Does the UI still work if there is no data?
Does the UI still work if names are long?
Does it fit a normal laptop screen?
```

## Recommended Component Philosophy

Create components because they repeat a meaningful UI/domain concept.

Good examples:

```text
ExperimentStatusBadge
ExperimentTimeline
MetricChart
ScoreBreakdown
ServiceRiskTable
PreflightCheckList
```

Avoid over-fragmenting:

```text
TitleText
CardTitleText
SmallBlueNumber
GreenStatusText
```

## Accessibility Basics

At minimum:

```text
form inputs have labels
buttons have clear text/accessible names
status is not represented by color alone
focus states remain visible
dialogs can be closed with keyboard
tables have meaningful headers
charts have nearby textual summary where important
```

## Performance Basics

Do not optimize prematurely, but avoid obvious problems:

```text
do not refetch the entire dashboard every second
do not render thousands of chart points if a smaller window is enough
do not load heavy topology libraries on pages that do not use them
do not put all pages into client components
```

## Frontend Handoff to Backend Integration

When a page is ready for real data, provide:

```text
endpoint expected
request shape
response shape
mock handler used
screen states supported
fields actually rendered
fields currently ignored
```

This makes mock-to-real wiring predictable.
