# Project Memory Templates

Use these templates when initializing or repairing project continuity documents. Keep them concise and adapt them to the repository.

When instantiating these templates, write human-facing content in the user's primary working language. Keep file paths, YAML keys, stable enum values, code identifiers, and API names in English unless the project explicitly chooses otherwise.

Use these templates together with the focused references in this folder. In particular, use `project-types.md` to decide which docs are actually needed, `task-and-handoff-system.md` for status fields, and `acceptance.md` for user acceptance versus owner approval versus local verification.

## Template Index

- Minimal file set
- Agent instruction entrypoints: `AGENTS.md`, `CLAUDE.md`
- **Lightweight state: `STATE.md`** (first file a new agent reads)
- Core docs: product, architecture, page map, roadmap, release, UI, API
- Work tracking: tasks index, task file, dev log, handoff
- Decisions and API examples

## Minimal File Set

```text
AGENTS.md
CLAUDE.md (when Claude Code is expected)
docs/
├── STATE.md                  # Lightweight state snapshot (read first)
├── product.md
├── architecture.md
├── page-map.md
├── roadmap.md
├── releases/
│   └── RELEASE-001-example.md
├── ui.md
├── api.md
├── tasks.md
├── tasks/
│   └── TASK-001-example.md
├── dev-log.md
├── handoff.md
└── decisions/
    └── 001-example.md
```

## docs/STATE.md

The first file a new agent reads. Must be under 40 lines, structured, and sufficient to answer "where am I, what am I doing, what's next".

```markdown
---
updated: YYYY-MM-DD
phase: planning | development | testing
step: 1
step_name: product_document | tech_stack | page_style | frontend_dev | mock_data | preliminary_acceptance | backend_dev | integration | full_testing
current_task: ""
current_task_file: ""
current_release: RELEASE-001
next_action: continue_implementation
blocked: false
blocker: ""
---

# Project State

## Current Phase
- Phase: planning / development / testing
- Step: N — step name
- Release: RELEASE-xxx

## Current Task
- TASK-xxx: title
- Status: current / blocked
- Next: exact next action in plain language

## Key Decisions
- Product: one-line MVP summary
- Tech: frontend + backend + database
- Design: style direction (if confirmed)

## Blockers
- None / description + unblock action

## Recent Completions
- TASK-xxx: title (done)
```

Rules:
- Update after every task status change, phase transition, user confirmation, or session end.
- Keep under 40 lines. No prose paragraphs.
- If this file is fresh, agents do not need to read handoff.md or tasks.md to orient.
- `step` values: 1=product_document, 2=tech_stack, 3=page_style, 4=frontend_dev, 5=mock_data, 6=preliminary_acceptance, 7=backend_dev, 8=integration, 9=full_testing
- STATE.md and handoff.md frontmatter must agree on `current_task`, `next_action`, `blocked`, and acceptance state. Update them together; if they conflict, treat the most recently updated file as the state to resolve, then repair both.
- `current_task_file` must hold the exact path (e.g. `docs/tasks/TASK-003-inventory-list.md`) so agents never guess task filenames.

## AGENTS.md

```markdown
# Agent Instructions

## Read First
- docs/STATE.md (lightweight snapshot — read this first, skip the rest if it's fresh)
- docs/handoff.md (only if STATE.md is missing or stale)
- docs/tasks.md (only if deeper task context needed)
- active docs/tasks/TASK-xxx.md (only if current_task identified)
- docs/product.md, docs/architecture.md, docs/page-map.md, docs/roadmap.md, docs/ui.md, docs/api.md (only when the task requires specific context)
- docs/dev-log.md (only when historical context needed)

## Documentation Language
- Write human-facing documentation in the user's primary working language.
- Keep YAML keys, stable enum values, file paths, code identifiers, and API names in English for machine readability.

## Cross-Agent Entrypoints
- AGENTS.md is the canonical project instruction file unless the project explicitly chooses another canonical file.
- If Claude Code is used, create or update CLAUDE.md and require it to read AGENTS.md before work.
- If another agent surface is used, create the equivalent instruction entrypoint when needed.
- Critical rules must either be repeated in each entrypoint or the entrypoint must point to the canonical file and require reading it first.
- If instruction files are mirrored instead of pointer-based, update them together whenever critical rules change.

## Default Development Flow

Phase 1 — Planning (规划阶段):
1. Step 1 `product_document`: confirm product direction and version boundaries (docs/product.md); confirm the first release finish line (docs/roadmap.md, docs/releases/).
2. Step 2 `tech_stack`: confirm technical selection (docs/architecture.md); classify project type: UI product, backend/API, library, CLI, infra/ops, data/AI, or mixed.

Phase 2 — Development (开发阶段; steps shown for UI products — for backend/API, library, CLI, infra/ops, or data/AI projects, substitute the relevant contract, interface, environment, or evaluation steps instead of forcing page/UI steps):
3. Step 3 `page_style`: confirm docs/page-map.md and style direction; implement and accept the Design Anchor (docs/ui.md).
4. Step 4 `frontend_dev`: implement all release pages following the accepted anchor.
5. Step 5 `mock_data`: mock service layer covering meaningful states; pages depend on adapters, not raw mock files.
6. Step 6 `preliminary_acceptance`: user validates pages and interactions under mock data.
7. Step 7 `backend_dev`: implement backend against docs/api.md contracts (draft contracts early enough to guide mocks, stabilize after validation).
8. Step 8 `integration`: replace mock adapters with real APIs; verify flows end-to-end.

Phase 3 — Testing (全量测试阶段):
9. Step 9 `full_testing`: explicit test case table for user manual acceptance; deployment preparation.

Rules:
- Keep docs/STATE.md `phase`/`step` aligned with this flow at all times.
- Steps are sequential; a step's gate must pass before the next step starts.
- Plan later releases at finish-line granularity only; break release N+1 into detailed tasks only after release N passes release acceptance.
- Update dev-log, roadmap/release progress, tasks, STATE.md, and handoff before ending work.

## Setup Phase Rule
Complete project setup/design in one continuous session whenever possible: product direction, lifecycle/release plan, technical selection, page map, initial API draft, initial UI constraints, and first task backlog. Use the current agent session's internal plan for setup progress. Do not add extra persistent setup-progress files unless the user explicitly asks for them; repository handoff docs are primarily for the longer development phase.

At the start of setup/design, create or update AGENTS.md first so the user does not need to invoke `agent-project-continuity` repeatedly. During setup/design, short prompts such as "继续设计", "下一个问题", or "继续" mean: continue the interactive design flow, ask the next single decision question, or record the latest answer before moving on.

## Short Resume Prompts
When the user says "continue development", "继续开发", "continue yesterday's task", "继续昨天的任务", or similar:
1. Read docs/STATE.md. If it is fresh and internally consistent, orient from it alone — do not read handoff.md or tasks.md just to re-confirm what STATE.md already says.
2. If STATE.current_task exists and the next action involves it, read that TASK file.
3. Read docs/handoff.md only if STATE.md is missing, stale, or insufficient for the next action.
4. Read docs/tasks.md only if no current task is identified; select the first task in Current, then Ready.
5. Follow next_action from STATE.md (or handoff.md when STATE.md is unavailable).
6. If blocked is true and cannot be resolved locally, ask the user only for the missing decision or resource.

Minimal read set for a fresh resume: AGENTS.md + docs/STATE.md + the active TASK file. Read more only when the next action requires it.

Supported next_action values:
- continue_implementation
- wait_for_user_acceptance
- wait_for_owner_approval
- wait_for_release_acceptance
- ask_product_question
- fix_verification_failure
- start_next_ready_task
- verify_external_state
- blocked

## Status Query Rule
When the user asks "当前进行到哪一步", "下一步做什么", "当前阶段", "进度汇报", "where are we", "what is next", or similar:
1. Read docs/STATE.md. If it is fresh, answer from it: development phase, lifecycle step, current task, blocker, acceptance state, and exact next action.
2. Read docs/handoff.md only if STATE.md is missing, stale, or the question needs detail STATE.md does not carry.
3. Read docs/roadmap.md, the active docs/releases/ file, docs/tasks.md, or the active TASK file only when the question requires release-level progress or task-level detail.
4. Check git status if the answer mentions implementation changes.

Do not update docs for a status answer unless docs contradict each other or current code state.

## User Confirmation Required
- MVP scope
- Lifecycle/release plan and first release/phase finish line
- Project type when it changes the development flow
- Technical selection
- Page map before Design Anchor implementation
- Key user states and boundary states that affect structure, navigation, trust, data entry, permissions, onboarding, or core workflows
- Page style direction before Design Anchor implementation
- First accepted Design Anchor
- Global UI direction changes
- Stable API contracts when they affect long-term behavior
- Backend technology choices
- Major data model changes
- Release or publishing actions

## Scope Rules
- Follow the active TASK document.
- Record out-of-scope discoveries as new tasks.
- Do not rewrite unrelated code or decisions without user approval.

## Task Transition Guard
- Do not start a new task unless the current task is Done, explicitly Blocked, or the user explicitly asks to switch tasks.
- The next task must have an existing docs/tasks/TASK-xxx.md file before implementation starts.
- Do not invent task IDs in chat. Create the task file first during task planning, then reference it.
- If user acceptance is pending, keep the current task active and present the acceptance table instead of moving on.
- Do not start optional work after the active release/phase exit criteria are met. Present release/phase acceptance instead.
- Do not mark a task Done, give a final task-complete response, or start the next task until required documentation has been updated.

## Documentation Completion Rule
Task completion requires documentation updates, but update only the files whose state changed:
- Status query or resume answer: do not update docs unless docs contradict each other or current code state.
- Small bug fix, small UI adjustment, or local verification note: update docs/dev-log.md; update docs/handoff.md or the active task only if next action, blocker, acceptance status, or scope changed.
- Task completion: update docs/tasks.md, the active docs/tasks/TASK-xxx.md, docs/dev-log.md, and docs/handoff.md.
- Release/phase progress change: also update docs/roadmap.md and the active docs/releases/ file.
- Product, architecture, page map, UI contract, API contract, or decision change: update the corresponding source document.

If required documentation cannot be updated, keep the task Current or Blocked and record the reason in handoff.

Use structured metadata values:
- task status: ready, current, blocked, done
- acceptance_status: not_required, pending, accepted, failed
- next_action: continue_implementation, wait_for_user_acceptance, wait_for_owner_approval, wait_for_release_acceptance, ask_product_question, fix_verification_failure, start_next_ready_task, verify_external_state, blocked

## Feature Expansion
For an established project, do not rerun full setup when adding a feature. Read existing docs, ask feature-scoped questions, assess impact, update affected docs only, create atomic tasks, and update handoff.

Place new feature tasks by dependency and priority:
- Blocking dependency: insert before or alongside affected tasks and mark blocked tasks.
- Current release/phase requirement: add to the active release/phase only when it is necessary for the agreed exit criteria.
- Later release/phase enhancement: add to that release/phase and do not interrupt current work.
- Optional/non-MVP polish: add to Backlog.

Always tell the user which release/phase a new feature is being assigned to and why. If it changes the active release/phase goal or exit criteria, ask for confirmation before implementation.

## Lifecycle And Release Planning
- Define the project lifecycle during setup/design. The structure is project-specific and may be versions, phases, milestones, or another naming scheme chosen with the user.
- Create docs/roadmap.md for the full lifecycle overview.
- Create docs/releases/RELEASE-xxx.md files for active and near-term releases/phases.
- Every implementation task should declare which release/phase it belongs to.
- Each release/phase must define included scope, excluded scope, required tasks, exit criteria, and release-level acceptance.
- At the end of each meaningful task, check whether the active release/phase exit criteria are now met.
- If the active release/phase is complete, proactively present release/phase acceptance and do not continue adding features blindly.
- New features discovered during development must be classified into current release/phase, later release/phase, or backlog before implementation.

## Acceptance And Approval Protocol
Use the lightest validation level that fits the change.

User-visible UI, interaction, workflow, release behavior, or externally visible integration behavior requires scoped user acceptance before Done. Durable product, stable API, architecture, data model, security, privacy, or release-scope decisions require owner approval. Internal refactors, tests, tooling, and behavior-preserving changes require local verification.

Present user acceptance as a table:

| 序号 | 验收项 | 操作步骤 | 预期结果 | 是否通过 |
| --- | --- | --- | --- | --- |
| 1 |  |  |  | 待用户确认 |

Rules:
- Do not infer acceptance from vague approval or progress prompts such as "继续", "下一步", or "看起来不错".
- Do not mark the task Done or start the next task until required user acceptance or owner approval is explicitly confirmed.
- If any item fails, keep the task active, fix the issue, and present an updated acceptance table.
- Owner approval can be recorded as a short decision checklist or decision record when there is no manual user action.
- Internal refactors that preserve accepted behavior need a verification table, not user acceptance.
- Small adjustments within an accepted UI contract can be locally verified and recorded in dev-log.md, then batched for acceptance when they affect scoped task behavior or the visible contract.

## UI Control Scope
Drive UI controls from confirmed user workflows, not from every data model or API field. Do not expose form fields, filters, settings, or configuration controls merely because the underlying model contains them.
```

## CLAUDE.md

Use this template when Claude Code is expected to work on the project.

```markdown
# Claude Code Instructions

This project uses AGENTS.md as the canonical project instruction source.

Before starting any work:
1. Read docs/STATE.md (lightweight snapshot — orient here first).
2. Read AGENTS.md for operating rules.
3. Read docs/handoff.md only if STATE.md is missing or stale.
4. Read docs/roadmap.md and active docs/releases/RELEASE-xxx.md only when release context is needed.
5. Read docs/tasks.md and active docs/tasks/TASK-xxx.md only when task context is needed.

Critical rules:
- Do not start a new task unless the current task is Done, explicitly Blocked, or the user explicitly asks to switch tasks.
- Do not invent task IDs. The next task must have an existing docs/tasks/TASK-xxx.md file before implementation starts.
- Do not mark a task Done or start the next task until required documentation updates are complete for the changed scope.
- Every task must belong to a release/phase or backlog.
- Do not silently expand the active release/phase. Classify new features into current release/phase, later release/phase, or backlog.
- If the active release/phase exit criteria are met, present release/phase acceptance before continuing feature work.
- User-visible UI, interaction, workflow, release behavior, or externally visible integration behavior requires scoped structured user acceptance before Done.
- Durable product, stable API, architecture, data model, security, privacy, or release-scope decisions require owner approval.
- Vague prompts such as "继续", "下一步", "看起来不错", or "continue" do not count as user acceptance.
- When asked about status or next steps, read docs/handoff.md, docs/roadmap.md, the active release/phase file, docs/tasks.md, and the active TASK file before answering.

If CLAUDE.md and AGENTS.md diverge, follow AGENTS.md as canonical, report the mismatch, and update both files when appropriate.
```

## docs/product.md

```markdown
---
status: draft
updated: YYYY-MM-DD
---

# Product

## Discovery Conversation Rule
- Ask one key decision question at a time.
- Prefer 2-3 concrete options with one recommended default when useful.
- Keep a free-form answer path open.
- Record each confirmed decision before asking the next question.
- Do not send a long list of unrelated product questions in one message.

## Product Direction Questions
- Target user:
- Core scenario:
- MVP goal:
- Main pages:
- Priority workflows:
- Required data:
- Explicitly out of scope:
- Open questions:

## Example Question Format
Question: Who is the first target user?

Options:
- A. Personal user (Recommended): fastest MVP path for self-managed inventory.
- B. Family user: supports shared inventory, but needs account and permission decisions.
- C. Small shared group: useful for dorms or teams, but adds workflow complexity.

Confirmed answer:
```

## docs/architecture.md

```markdown
---
status: draft
frontend_stack: ""
backend_stack: ""
database: ""
auth: ""
deployment: ""
updated: YYYY-MM-DD
---

# Architecture

## Technical Selection
- Frontend:
- Backend:
- Database / storage:
- Authentication:
- Deployment / hosting:
- Test strategy:
- Language:
- Dependency policy:

## Platform Constraints
- 

## Run And Verify
- Dev:
- Build:
- Test:
- Preview:

## Open Questions
- 
```

## docs/page-map.md

```markdown
---
status: draft
design_anchor: ""
requires_user_acceptance: true
acceptance_status: pending
updated: YYYY-MM-DD
---

# Page Map

## Global Navigation
- Entry:
- Tabbar / stack:
- Back behavior:

## MVP Pages

### Page Name / route/path
- Purpose:
- Required features:
- Primary interactions:
- States:
- Data dependencies:
- Entry points:
- Destination links:
- Design role: Design Anchor | Design Extension

## Key User States And Boundary States
- Normal:
- Empty:
- Loading:
- Error:
- Permission / access:
- Boundary / edge:
- Multi-state combinations:
- States requiring user confirmation:

## Out Of Scope Pages
- 

## Open Questions
- 
```

## docs/roadmap.md

```markdown
---
status: draft
current_release: RELEASE-001
lifecycle_scheme: ""
updated: YYYY-MM-DD
---

# Roadmap

## Lifecycle Scheme
- Scheme: version | phase | milestone | custom
- Naming convention:
- Confirmed by user: pending

## Planning Rule
- Roadmap defines where the software lifecycle is going.
- Tasks define how the current release/phase is implemented.
- New features must be assigned to current release/phase, a later release/phase, or backlog before implementation.
- When a release/phase exit criteria are met, stop feature expansion and request release-level acceptance.

## Releases / Phases

### RELEASE-001 / Name
- Status: planned | active | ready_for_acceptance | accepted | released | deferred
- Purpose:
- Target users:
- Finish line:
- Release file: docs/releases/RELEASE-001-example.md
- Required tasks:
- Optional tasks:
- Deferred to later:

### RELEASE-002 / Name
- Status: planned
- Purpose:
- Finish line:
- Release file:

## Backlog Themes
- 
```

## docs/releases/RELEASE-xxx.md

```markdown
---
id: RELEASE-001
name: First usable release
status: planned
type: release
required_for_launch: true
acceptance_status: pending
updated: YYYY-MM-DD
---

# RELEASE-001 First Usable Release

## Purpose

## Target Users

## Included Scope
- 

## Explicitly Excluded Scope
- 

## Exit Criteria
| 序号 | 完成条件 | 关联任务 | 状态 | 备注 |
| --- | --- | --- | --- | --- |
| 1 |  |  | pending |  |

## Required Tasks
- TASK-001

## Optional Tasks
- 

## Deferred To Later Releases
- 

## Release Acceptance Table
| 序号 | 验收项 | 操作步骤 | 预期结果 | 是否通过 |
| --- | --- | --- | --- | --- |
| 1 |  |  |  | 待用户确认 |

## Notes
```

## docs/ui.md

```markdown
---
status: draft
design_mode: design-anchor
anchor_page: ""
requires_user_acceptance: true
acceptance_status: pending
updated: YYYY-MM-DD
---

# UI Contract

Status: draft
Design mode: Design Anchor | Design Extension
Anchor page:

## Visual Direction

## Style Direction Confirmation
- Confirmed by user: pending
- Desired tone:
- Density:
- Color preferences:
- Component feel:
- Explicit dislikes:

## Example Style Question
Question: What style should the first page use?

Options:
- A. Clean and practical (Recommended): quiet, readable, suitable for repeated daily use.
- B. Warm and home-like: softer colors and friendlier details, but slightly less dense.
- C. Bold and expressive: stronger visual personality, but higher risk of distracting from utility.

Confirmed answer:

## Colors
- Primary:
- Warning:
- Danger:
- Background:
- Surface:
- Text primary:
- Text secondary:

## Typography
- Page title:
- Section title:
- Body:
- Caption:
- Emphasis:

## Spacing
- Page padding:
- Section gap:
- Card padding:
- List item gap:

## Components
### Buttons

### Cards

### Lists

### Forms

### Empty / Loading / Error States

## Interaction
- Tap feedback:
- Navigation:
- Back behavior: if a custom back action has no previous page in the stack, navigate to the home page unless the task defines a different rule.
- Dialogs / drawers:
- Form validation:
- Low-risk platform-conventional interactions may use sensible defaults and be recorded here.
- Product behavior, data outcomes, trust-sensitive interactions, and global navigation changes require user confirmation.

## Copy Tone

## Do Not
- Do not introduce a new global visual direction without user confirmation.
```

## docs/tasks.md

```markdown
# Tasks

## Current
- [ ] TASK-001 Create running frontend prototype

## Current - Acceptance Pending
- [ ] TASK-004 Review accepted visual and interaction checklist

## Ready
- [ ] TASK-002 Add mock service layer

## Blocked
- [ ] TASK-003 Implement backend reminders
  - Blocked by: reminder strategy not validated

## Backlog
- [ ] TASK-009 Optional polish task

## Done
- [x] TASK-000 Initialize project memory docs
```

## docs/tasks/TASK-xxx.md

```markdown
---
id: TASK-001
title: Create running frontend prototype
status: current
type: frontend-prototype
release: RELEASE-001
release_required: true
release_exit_criteria: []
module: home
priority: high
design_mode: design-anchor
page_map_status: accepted
depends_on: []
blocked_by: []
blocks: []
corrects: ""
requires_user_acceptance: true
acceptance_status: pending
verification_status: pending
updated: YYYY-MM-DD
---

# TASK-001 Create running frontend prototype

Status: Current

## Goal
Build the first runnable frontend page as the design source of truth.

## Scope
- Page layout
- Representative static content
- Key UI states
- Basic navigation or interaction needed to judge the design

## Out Of Scope
- Backend integration
- Persistent storage
- Final API stabilization

## Context
- Product basis: docs/product.md
- Technical basis: docs/architecture.md
- Roadmap: docs/roadmap.md
- Release / phase: docs/releases/RELEASE-xxx.md
- Page map: docs/page-map.md
- UI rules: docs/ui.md
- API draft: docs/api.md

## Design Continuity
- Mode: Design Anchor | Design Extension
- Anchor page:
- Shared components to inspect:
- Existing UI patterns to reuse:
- New UI patterns introduced:

## Implementation Steps
1. Inspect the existing frontend structure.
2. Read docs/architecture.md and docs/page-map.md.
3. Read docs/ui.md and inspect the anchor page when this is a Design Extension.
4. Create or update the target page.
5. Add representative preview fixtures for meaningful user states; keep them separate from markup/templates.
6. Render normal, empty, and edge states when relevant.
7. Adjust spacing, hierarchy, copy, and interaction in code.
8. Record low-risk default interactions in docs/ui.md; ask the user about high-impact interactions.
9. Update docs/ui.md if this task establishes or extends reusable UI patterns.
10. Verify the page in the available local preview environment.

## Acceptance Criteria
- Page runs in the project environment.
- Page is usable as the current visual reference.
- Preview fixtures or mock data cover the required states.
- Preview scenarios include meaningful non-happy-path states for this page or explicitly document why they are not relevant.
- User-requested visual states are shown through the available preview surface, screenshot, simulator, or exact local URL when feasible.
- Business data is not hard-coded directly in page markup/templates.
- No direct page dependency on raw mock files when a service layer is expected.
- Design Extension pages follow docs/ui.md and the accepted anchor page.
- UI controls are tied to confirmed user workflows, not simply all available model/API fields.
- Existing shared components are reused where appropriate, or the exception is documented.
- Base components extracted after user acceptance preserve the accepted page appearance.
- Frontend task is not marked Done until user acceptance is complete.
- Any user-visible UI, interaction, workflow, release behavior, or externally visible integration behavior has a scoped user acceptance table.
- Any durable product, stable API, architecture, data model, security, privacy, or release-scope decision has owner approval recorded.
- Task is linked to the correct release/phase or explicitly marked as backlog.
- Active release/phase exit criteria are checked after this task is complete.
- Required documentation updates are complete before this task is marked Done.

## User Acceptance Table
Use this table when `requires_user_acceptance: true`.

| 序号 | 验收项 | 操作步骤 | 预期结果 | 是否通过 |
| --- | --- | --- | --- | --- |
| 1 | Visual layout and style | Open the target preview page and inspect the changed area. | The page matches the accepted UI direction and has no obvious layout, spacing, or copy issues. | 待用户确认 |
| 2 | Scoped interactions | Perform each interaction included in this task, such as navigation, dialogs, forms, tabs, filters, or state switches. | Each scoped interaction behaves as described in this TASK file. | 待用户确认 |
| 3 | Page states | View the required normal, empty, loading, error, or edge states when relevant. | Required states are understandable, consistent, and do not show broken UI. | 待用户确认 |
| 4 | Regression check | Revisit the directly affected accepted page or flow. | This task's visible changes did not introduce obvious regressions. | 待用户确认 |

Do not mark Done until the user explicitly confirms acceptance. Vague prompts such as "continue" or "下一步" do not count as acceptance.

## Verification
| 序号 | 验证项 | 命令或操作 | 结果 | 备注 |
| --- | --- | --- | --- | --- |
| 1 | Build/test/lint |  | pending |  |
| 2 | Preview/manual check |  | pending |  |
| 3 | Not verified |  | pending |  |

User acceptance: pending

## Documentation Updates
- docs/tasks.md: pending
- Active TASK file: pending
- docs/dev-log.md: pending
- docs/handoff.md: pending
- docs/roadmap.md or docs/releases/ if release progress changed: not applicable
- docs/product.md, docs/architecture.md, docs/page-map.md, docs/ui.md, docs/api.md, or docs/decisions/ if changed: not applicable

Do not mark Done until required documentation updates are complete.

## After User Acceptance
- Extract base components when repeated patterns are clear and extraction is in scope.
- Otherwise create a follow-up component extraction task.
- Mark the task Done only after implementation, verification, documentation updates, and user acceptance are complete.

## Notes
```

## docs/dev-log.md

```markdown
---
updated: YYYY-MM-DD
---

# Dev Log

## Reading Guide
- Resume work from docs/STATE.md and the active TASK first; read docs/handoff.md and docs/tasks.md only when STATE.md is missing, stale, or insufficient.
- Use this file only when historical reasons, verification detail, or user feedback chains are needed.
- Read by topic from the index below instead of loading the whole file by default.
- Update this guide and index whenever a new durable theme, release, task cluster, or external-state thread appears.

## Index

### Current Release / Active Work
- `YYYY-MM-DD Topic Title`: short reason to read this entry.

### Product And Decisions
- `YYYY-MM-DD Topic Title`: short reason to read this entry.

### UI / UX
- `YYYY-MM-DD Topic Title`: short reason to read this entry.

### Architecture / API / External State
- `YYYY-MM-DD Topic Title`: short reason to read this entry.

## YYYY-MM-DD

Goal:

Changes:
- 

Verification:
- 

Decisions:
- 

Gaps / Next:
- 
```

## docs/handoff.md

```markdown
---
updated: YYYY-MM-DD
current_task: TASK-001
current_task_file: docs/tasks/TASK-001-example.md
current_release: RELEASE-001
next_action: wait_for_user_acceptance
blocked: false
blocker: ""
acceptance_status: pending
---

# Handoff

Updated: YYYY-MM-DD

## Current State

## Current Release / Phase

## Next Step

## Pending User Input
- 

## Key Files
- 

## Cautions
- 

## Verification Status
- 

## Resume Rule
If `next_action` is `wait_for_user_acceptance`, present the current task's user acceptance table and do not continue implementation unless the user asks for changes.

If `next_action` is `wait_for_owner_approval`, present the pending decision or approval checklist and do not continue the affected work until the user explicitly approves or changes direction.

If `next_action` is `wait_for_release_acceptance`, present the active release/phase acceptance table and do not continue feature work unless the user asks for changes.

If the user asks for current progress or next steps, read this file, docs/roadmap.md, the active release/phase file, docs/tasks.md, and the active TASK file before answering.
```

## docs/api.md

````markdown
---
status: draft
version: "0.1"
updated: YYYY-MM-DD
---

# API Contract

Status: draft

## Status Rules
- draft: initial resource and response shape before or during frontend prototype work.
- validated: mock frontend is usable and fields have been exercised by real page flows.
- stable: backend implementation and frontend integration have passed verification.
- deprecated: no longer preferred, but temporarily retained for compatibility.

## Resource: Item

```json
{
  "id": "item_001",
  "name": "Milk",
  "expireDate": "2026-06-20",
  "quantity": 1,
  "unit": "box",
  "createdAt": "2026-06-06T10:00:00+08:00",
  "updatedAt": "2026-06-06T10:00:00+08:00"
}
```

## GET /items

Purpose:

Request:

Response:

Open questions:
- 
````

## docs/decisions/001-example.md

```markdown
---
id: 001
status: accepted
date: YYYY-MM-DD
---

# 001 Example Decision

Date: YYYY-MM-DD

## Context

## Decision

## Alternatives Considered

## Consequences
```
