# AutoResilience - AI Session Context Protocol (Interim)

## Purpose

This file prevents loss of context when:

```text
a chat gets too long
credits finish
someone switches ChatGPT -> Claude -> Gemini -> another model
a teammate stops for the day
a different teammate needs to understand the work
```

This is the interim protocol.

After Step 4, this will be turned into a polished common teammate operating guide/PDF.

---

# 1. Files Every Workstream Maintains

Recommended documentation structure:

```text
AutoResilience-Docs/
|
|-- AI_START_HERE.md
|-- docs/
|   |-- MASTER_AI_HANDOFF.md
|   `-- team/
|       |-- 01_SHARED_INTEGRATION_CONTRACT.md
|       |-- 02_FRONTEND_TEAMMATE_EXECUTION_GUIDE.md
|       |-- 03_BACKEND_SHARED_EXECUTION_GUIDE.md
|       `-- 04_CHAOS_INFRA_OWNER_EXECUTION_GUIDE.md
|
`-- journey/
    |-- current/
    |   |-- FRONTEND_CURRENT_STATE.md
    |   |-- BACKEND_CURRENT_STATE.md
    |   `-- CHAOS_CURRENT_STATE.md
    |
    `-- handoffs/
        |-- frontend/
        |-- backend/
        `-- chaos/
```

Separate current-state files reduce merge conflicts.

---

# 2. What to Attach at the Start of an AI Session

## Frontend teammate attaches

```text
MASTER_AI_HANDOFF.md
01_SHARED_INTEGRATION_CONTRACT.md
02_FRONTEND_TEAMMATE_EXECUTION_GUIDE.md
FRONTEND_CURRENT_STATE.md
```

If continuing a completed previous phase, also attach the latest relevant phase handoff.

## Backend teammate/project owner attaches

```text
MASTER_AI_HANDOFF.md
01_SHARED_INTEGRATION_CONTRACT.md
03_BACKEND_SHARED_EXECUTION_GUIDE.md
BACKEND_CURRENT_STATE.md
```

If the session is about Litmus/Prometheus integration, also attach the latest chaos integration handoff.

## Chaos owner attaches

```text
MASTER_AI_HANDOFF.md
01_SHARED_INTEGRATION_CONTRACT.md
04_CHAOS_INFRA_OWNER_EXECUTION_GUIDE.md
CHAOS_CURRENT_STATE.md
```

---

# 3. Standard Opening Message

Use:

```text
Read all attached AutoResilience documents before giving instructions.

Workstream: <FRONTEND | BACKEND | CHAOS>
Current phase: <PHASE ID>
Current goal: use CURRENT_STATE.md as the source of truth.

Before creating or editing code:
1. ask for git status,
2. ask for current branch,
3. inspect the relevant file tree,
4. inspect the files you intend to modify,
5. do not create duplicates.

Give me 2-4 connected steps at a time.
Explain new concepts briefly.
Stay inside this phase unless a dependency blocks us.

When this phase is complete:
- verify it,
- tell me what to commit,
- update/generate CURRENT_STATE content,
- generate a phase handoff,
- tell me the next recommended phase.
```

---

# 4. Current-State File Template

Every workstream keeps one.

Example:

```markdown
# BACKEND CURRENT STATE

Last updated: 2026-08-17

## Active Phase
B1 - Configuration & Environment

## Current Branch
feature/api-config

## Current Goal
Introduce typed pydantic-settings configuration.

## Completed
- FastAPI scaffold
- /health works
- uv environment works

## Files Relevant Now
- apps/api/app/main.py
- apps/api/app/core/
- apps/api/pyproject.toml

## Files Created/Modified This Session
- ...

## Last Successful Verification
- ...

## Current Error / Blocker
None

## Next Exact Step
Inspect app/core and create config.py only if it does not already exist.

## Do Not Start Yet
- PostgreSQL
- Litmus integration

## Latest Commit
<hash or not committed yet>
```

---

# 5. When to Update CURRENT_STATE

Update it:

```text
before stopping for the day
before intentionally starting a new chat
after solving a major blocker
after changing phase
after architecture/contract changes
```

You do not need to update it after every tiny command.

---

# 6. End-of-Session Checklist

Before leaving a session:

```text
[ ] run git status
[ ] note branch
[ ] note files changed
[ ] record last successful verification
[ ] record any unresolved error
[ ] record exact next step
[ ] update workstream CURRENT_STATE.md
[ ] commit code if the work is in a stable state
```

If the phase is complete:

```text
[ ] create phase handoff
[ ] merge/review PR as appropriate
[ ] set CURRENT_STATE to next phase
```

---

# 7. Phase Handoff File

When a phase is complete, create:

```text
journey/handoffs/<workstream>/<phase>-<name>.md
```

Example:

```text
journey/handoffs/backend/B1-config-complete.md
```

Template:

```markdown
# Phase Handoff

## Phase
B1 - Configuration & Environment

## Author
...

## Branch
...

## Commit
...

## What Was Built
...

## Files Created
...

## Files Modified
...

## How It Works
...

## How It Was Verified
...

## Important Decisions
...

## Shared Contract Changed?
No

## Known Limitations
...

## Next Recommended Phase
B2 - API Routing & Versioning
```

---

# 8. If Credits End Suddenly Before Updating State

Do not panic.

Give the next AI:

```text
MASTER_AI_HANDOFF.md
shared integration contract
your workstream guide
latest CURRENT_STATE.md
```

Then also provide:

```bash
git status
git branch --show-current
git diff --stat
```

and, if needed:

```bash
git diff
```

Then paste only the last relevant messages from the previous AI chat.

Opening line:

```text
My previous AI session ended unexpectedly.
Use the attached project/state files plus the Git state below.
First reconstruct exactly where the work stopped before suggesting changes.
```

---

# 9. If Switching AI Models Normally

Do NOT paste an entire huge old chat unless required.

Use:

```text
master context
shared contract
role guide
current state
latest handoff
```

Then say:

```text
Continue the active phase from CURRENT_STATE.md.
```

That should be enough.

---

# 10. How to Avoid Duplicate Work Between Teammates

Before starting a new phase:

```bash
git checkout main
git pull origin main
git status
find <owned-area> -maxdepth 5 -type f | sort
```

Read latest teammate handoffs when the phase depends on another workstream.

Examples:

Frontend integrating backend:

```text
read latest backend API handoff
```

Backend integrating Litmus:

```text
read latest chaos Litmus integration handoff
```

---

# 11. What Must Be Documented vs What Does Not Need a Document

Document:

```text
architecture decision
API contract change
experiment behavior
setup procedure
important bug/workaround
phase completion
current work state
```

Do not create documents for every minor variable rename or CSS adjustment.

---

# 12. What to Say When Ending an AI Chat

Ask:

```text
We are stopping this AutoResilience session now.

Based only on what we actually completed:
1. generate the updated content for my <WORKSTREAM>_CURRENT_STATE.md,
2. tell me whether the current phase is complete,
3. if complete, generate the phase handoff,
4. list the exact Git commands/checks I should run,
5. give me the title and opening prompt for the next AI chat.

Do not claim unfinished work is complete.
```

---

# 13. What the Final Post-Step-4 Guide Will Add

After Step 4, create a separate polished team operating guide/PDF that consolidates:

```text
how everyone starts work
which files to attach
how sessions end
how handoffs work
how Git branches work
how to avoid duplicate files
how to know the next task
how to switch AI models
how to preserve learning
how to review another teammate's work
mid-sem/final milestone workflow
```

This interim protocol should be updated if Steps 3-4 change the workflow.
