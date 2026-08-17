 Starting a New AI Session
Files to Attach (In Exact Order)


Frontend Teammate:
MASTER_AI_HANDOFF.md
01_SHARED_INTEGRATION_CONTRACT.md
02_FRONTEND_TEAMMATE_EXECUTION_GUIDE.md
FRONTEND_CURRENT_STATE.md
Latest relevant phase handoff (if continuing)


Backend Teammate:
MASTER_AI_HANDOFF.md
01_SHARED_INTEGRATION_CONTRACT.md
03_BACKEND_SHARED_EXECUTION_GUIDE.md
BACKEND_CURRENT_STATE.md
Latest relevant phase handoff


Project Owner (Chaos/Infra):
MASTER_AI_HANDOFF.md
01_SHARED_INTEGRATION_CONTRACT.md
04_CHAOS_INFRA_OWNER_EXECUTION_GUIDE.md
CHAOS_CURRENT_STATE.md
Latest relevant phase handoff



Copy and paste this exactly after attaching files:

( EDIT THE Workstream and other part according to your work )
Read all attached AutoResilience documents before giving instructions.

Workstream: <FRONTEND | BACKEND | CHAOS>
Current phase: <PHASE_ID> (e.g., B1, UI3, C2)
Current goal: Use CURRENT_STATE.md as the source of truth.

Before creating or editing code:
1. Ask for git status
2. Ask for current branch
3. Inspect the relevant file tree
4. Inspect the files you intend to modify
5. Do not create duplicates

Give me 2-4 connected steps at a time.
Explain new concepts briefly.
Stay inside this phase unless a dependency blocks us.

When this phase is complete:
- Verify it
- Tell me what to commit
- Update/generate CURRENT_STATE content
- Generate a phase handoff
- Tell me the next recommended phase




 Ending a Work Session


Mandatory End-of-Session Checklist

Run git status and review changes.
Note current branch and files changed.
Record last successful verification.
Record any unresolved errors/blockers.
Record the exact next step.
Update <WORKSTREAM>_CURRENT_STATE.md.
Commit code if work is in a stable state.
If phase is complete: Create phase handoff, merge/review PR, set CURRENT_STATE to next phase.



THERE IS A DOC REPO ( seperate from the code repo )
AutoResilience-Docs/
├── docs/
│   ├── MASTER_AI_HANDOFF.md              # Portable engineering context
│   ├── team/
│   │   ├── 01_SHARED_INTEGRATION_CONTRACT.md
│   │   ├── 02_FRONTEND_TEAMMATE_EXECUTION_GUIDE.md
│   │   ├── 03_BACKEND_SHARED_EXECUTION_GUIDE.md
│   │   └── 04_CHAOS_INFRA_OWNER_EXECUTION_GUIDE.md
│   ├── architecture/                     # Diagrams & ADRs
│   ├── concepts/                         # Domain concepts (e.g., Resilience Score)
│   └── scoring/                          # Resilience scoring methodology
└── journey/
    ├── current/
    │   ├── FRONTEND_CURRENT_STATE.md     # Frontend workstream state
    │   ├── BACKEND_CURRENT_STATE.md      # Backend workstream state
    │   └── CHAOS_CURRENT_STATE.md        # Chaos/infra workstream state
    └── handoffs/
        ├── frontend/                     # Frontend phase handoffs
        ├── backend/                      # Backend phase handoffs
        └── chaos/                        # Chaos phase handoffs

        understand this and add respective changes accordingly 
        will provided access or ask ~Aryan Prasher for access

Switching AI Models

When switching from ChatGPT → Claude → Gemini → Cursor, conversation history is lost.
Before switching:
Complete the End-of-Session Checklist.
Update CURRENT_STATE.md with your exact state.
Commit and push your work.
When starting with a new AI:
Attach the standard files (Section 3).
Use the Standard Opening Prompt.
If the previous session ended unexpectedly, also provide:

   git status
   git branch --show-current
   git diff --stat

Add to prompt: "My previous AI session ended unexpectedly. Use the attached project/state files plus the Git state below to reconstruct exactly where the work stopped before suggesting changes."




 CURRENT_STATE System

Why It Exists

Prevents context loss, enables parallel work, tracks progress, and facilitates smooth handoffs between humans and AI.
Who Updates It

Frontend teammate → FRONTEND_CURRENT_STATE.md
Backend teammate → BACKEND_CURRENT_STATE.md
Project Owner → CHAOS_CURRENT_STATE.md

When to Update
Before stopping for the day, before starting a new chat, after solving a major blocker, or after changing phases. Do not update after every tiny command.



Git Workflow


Branch Naming


feature/<workstream>-<phase-or-feature>
Examples: feature/api-config, feature/web-experiment-builder, feature/chaos-pod-failure
Commit Naming (Conventional Commits)
<type>(<scope>): <description>
Examples:


feat(api): add typed application configuration
feat(web): implement experiment builder wizard
fix(metrics): handle missing Prometheus samples
docs: document pod failure experiment


PR & Merge Workflow
Rebase on latest main.
Run linting/tests (npm run lint && npm run build or uv run pytest && uv run ruff check .).
Create PR with clear description, testing steps, and screenshots (if UI).
Require at least one review from a teammate.
Squash and merge. Delete branch.


Conflict Workflow
git checkout main && git pull origin main
git checkout feature/<branch> && git rebase main
Resolve conflicts manually (understand both sides, do not blindly accept "ours" or "theirs").
git add <resolved-files> && git rebase --continue
git push origin feature/<branch> --force-with-adjacent




PROJECT MILESTONE

Mid-Semester (Month 2-2.5) - Prove Architecture Works
Frontend: Design system, product shell, mock API layer, overview, experiment builder, live experiment room.
Backend: B1-B4 (config, routing, schemas, state machine), DB foundation, basic history API, tests.
Project Owner: kind cluster, Online Boutique deployed, Prometheus/Alertmanager working, Litmus installed, manual pod failure proven, Litmus integration handoff provided.
Demo Goal: Show healthy target, functional frontend (mocked), real pod failure, real Prometheus evidence, and clear explanation of what is real vs. mocked.




Final Semester (Month 3-4) - Complete Product
Frontend: History, detail pages, score UI, service risk, reports, real backend integration, hardening.
Backend: History/filter endpoints, report data models, API tests, DB migrations, persistence refinements.
Project Owner: Preflight validator, baseline collector, orchestrator, recovery detector, alert verification, multiple chaos types (CPU, memory, network), analysis model, resilience score, full integration, CI resilience gate.





AI Rules



How AI Should Behave
Inspect files before creating anything.
Give 2-4 connected steps at a time.
Stay inside the current phase.
Verify work before marking it complete.
Generate phase handoffs when done.


How to Avoid AI Hallucinations
Signs: Imports for libraries we don't use, fake Litmus CRD names, confident explanations of wrong concepts.
Catch them: Test the code immediately. Cross-reference with official Litmus/Prometheus documentation.
If suspected: Reply: "I think this might be incorrect. Can you verify: 1. Does this library/function actually exist? 2. Is this the correct API? 3. Show me the documentation."


How to Avoid Overengineering
Push back if the AI suggests: "This seems more complex than needed. Can we start with a simpler solution and add complexity only if we need it?"