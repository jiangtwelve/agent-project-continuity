---
name: agent-project-continuity
description: Use when starting a software project from 0 to 1, resuming development after a session or agent switch (codex/claude code/cursor), checking project status ("当前进行到哪一步", "继续开发"), adding features to an established project, or when continuity docs (STATE.md, handoff, tasks) are missing, stale, contradictory, or bloated.
---

# Agent Project Continuity

让 AI 代理能够跨会话、跨工具无缝恢复软件项目的工作。

## Quick Orientation

When resuming work on an existing project, read these files **in this order** and stop as soon as you can answer "where am I, what am I doing, what's next":

1. **`docs/STATE.md`** — lightweight snapshot (current phase, current task, next action, key decisions). If this file exists and is fresh, you have enough context to start.
2. **`docs/handoff.md`** — latest handoff note if STATE.md is missing or stale.
3. **`docs/tasks.md`** — task board for current/backlog/done overview.
4. **Active task file** — use the exact path in `current_task_file`; never guess task filenames.
5. **`AGENTS.md`** — operating rules when no project-specific instructions exist yet.

If `docs/STATE.md` exists, a new agent can orient within **one file read**. Read additional files only when the task requires deeper context.

## Development Lifecycle

A software project progresses through three phases. Steps within each phase are sequential; do not skip ahead.

### Phase 1: Planning (规划阶段)

> 目标：明确做什么、用什么做。理想情况下在一次会话中完成。

**Step 1 — Product Document (产品文档)**

- Discuss product requirements with the user through one-question-at-a-time dialogue.
- Generate `docs/product.md` covering target users, core scenarios, MVP goal, main pages, priority workflows, required data, and explicit out-of-scope items.
- Confirm version/phase boundaries: what goes into v1.0 vs v2.0 vs backlog.
- **Gate**: User must explicitly confirm the product document. Vague replies ("继续", "looks good") do not count.

**Step 2 — Tech Stack (技术栈)**

- Confirm frontend framework, backend stack, database, auth, deployment, test strategy.
- Record in `docs/architecture.md`.
- **Gate**: User must explicitly confirm tech choices.

### Phase 2: Development (开发阶段)

> 目标：按计划逐步实现产品。需要清晰的开发计划和开发记录。

**Step 3 — Page Style (页面风格)**

- Select one representative page as the **Design Anchor**.
- Implement the anchor page and iterate with the user until accepted.
- Record visual direction in `docs/ui.md`: colors, typography, spacing, components, interaction patterns, copy tone, and explicit "do not" rules.
- All subsequent pages follow this established direction (Design Extension).
- **Gate**: User must accept the Design Anchor before proceeding.

**Step 4 — Frontend Full Development (前端全量开发)**

- Implement all MVP pages following the Design Anchor/Extension pattern.
- Each page gets a TASK file with clear acceptance criteria.
- Create `docs/page-map.md` before building pages.
- **Gate**: All frontend pages implemented and locally previewable.

**Step 5 — Mock Data Integration (接入 Mock 数据)**

- Create mock service layer covering normal, empty, loading, error, edge, and permission states.
- Pages depend on service functions/adapters, not raw mock files.
- Mock data is deterministic unless explicitly randomized.
- **Gate**: All pages functional with mock data.

**Step 6 — Preliminary Acceptance (初步验收)**

- Present acceptance tables for each page: visual quality + interaction behavior.
- User validates page layout, interactions, states, and navigation under mock data.
- Record acceptance results in task files.
- **Gate**: User confirms pages and interactions are acceptable.

**Step 7 — Backend Development (后端开发)**

- Implement backend services following API contracts in `docs/api.md`.
- Each backend task gets a TASK file with verification criteria.
- Use `references/external-state.md` for database, cloud, deployment changes.
- **Gate**: Backend services implemented and locally verifiable.

**Step 8 — Frontend-Backend Integration (前后端联调)**

- Replace mock service layer with real API calls.
- Verify all user flows work end-to-end.
- Fix integration issues and update API contracts if needed.
- **Gate**: Full user flows working with real backend.

### Phase 3: Testing (全量测试阶段)

> 目标：全面验收，准备上线。

**Step 9 — Full Testing (全量测试)**

- Generate explicit test cases for user manual acceptance.
- Present test case table covering: functional flows, edge cases, error handling, performance, cross-device/platform.
- User executes manual acceptance testing.
- Record test results and fix failures.
- Prepare deployment checklist.
- **Gate**: All test cases pass, deployment ready.

### Later Releases — Deferred Planning (后续版本延迟规划)

Plan later releases (v2.0+) at **finish-line granularity only**: purpose, included scope, excluded scope, exit criteria. Do not break them into detailed tasks while an earlier release is in development.

Detailed task breakdown for release N+1 starts **only after release N passes release-level acceptance**. Requirements always shift once the user sees a working release; early detailed planning wastes effort and pollutes the task board with stale tasks.

## Lightweight State System

### STATE.md

`docs/STATE.md` is the **first file a new agent reads**. It must be:
- **Short**: under 40 lines. No prose paragraphs.
- **Structured**: YAML frontmatter + bullet points only.
- **Current**: updated at every meaningful state change.
- **Sufficient**: enough to answer "where am I, what am I doing, what's next" without reading other files.

Update STATE.md:
- After every task status change
- After every phase transition
- After every user confirmation
- Before ending a work session

See `references/project-memory-templates.md` for the STATE.md template.

### Handoff.md

`docs/handoff.md` is the **detailed handoff** for the next agent. It contains:
- Current state narrative
- Pending user input
- Key files and cautions
- Resume rules

STATE.md is the quick snapshot; handoff.md is the full context. A new agent reads STATE.md first, then handoff.md only when more detail is needed.

## Immutable Task Rule

**Completed tasks (`status: done`) are immutable.** Do not delete or modify them.

When a completed task is later found to be problematic:
1. Create a **new correction task** (e.g., TASK-015: "Fix issue in TASK-008").
2. Link it to the original task via `corrects: TASK-008` in frontmatter.
3. The original task remains `done` with its original content.
4. The new task describes what needs to change and why.

This preserves an accurate development history and prevents confusion when switching agents.

## Change Protocol

When requirements change during development:

1. **Pause**: Stop current implementation if affected.
2. **Document**: Update `docs/product.md` with the changed requirement.
3. **Classify**: Determine if the change affects:
   - Current release/phase (blocks exit criteria)
   - Later release/phase (valuable but not blocking)
   - Backlog (optional/exploratory)
4. **Confirm**: User must explicitly approve the product document update.
5. **Plan**: Update development plan — insert into current tasks or schedule for later.
6. **Update**: Refresh STATE.md, handoff.md, and affected task files.
7. **Resume**: Continue implementation with updated plan.

Never silently expand scope. Every change must be documented and confirmed.

## Core Invariants

Keep one source of truth for each concern:

- `AGENTS.md`: operating rules for agents in this repository.
- `docs/STATE.md`: lightweight current state snapshot.
- `docs/product.md`: product intent, MVP, user flows, and boundaries.
- `docs/architecture.md`: selected stack, system shape, integration boundaries, and run/verification commands.
- `docs/page-map.md`: pages, routes, navigation, states, data dependencies, and Design Anchor choice when UI exists.
- `docs/roadmap.md`: lifecycle plan, release/phase structure, major goals, and finish lines.
- `docs/releases/`: per-release or per-phase goals, included scope, excluded scope, exit criteria, and acceptance rules.
- `docs/tasks.md`: task index and status board.
- `docs/tasks/TASK-xxx.md`: atomic implementation instructions and status.
- `docs/dev-log.md`: durable history, with a reading guide and topic index near the top.
- `docs/handoff.md`: latest resume note for the next agent.
- `docs/decisions/`: long-lived product, architecture, API, workflow, or external-state decisions.
- `docs/api.md` or `docs/api/`: API contract drafts and stable contracts.
- `docs/ui.md`: visual and interaction rules when the running frontend is a design source.

Create only the minimal set needed for the current phase. Do not create ceremonial docs.

## Document Format

Use Markdown for project memory unless the repository has a stronger local convention. Use YAML frontmatter for machine-readable state in task, handoff, API, UI, release, and decision documents.

Write human-facing documentation in the user's primary working language. Keep file paths, YAML keys, enum values, code identifiers, and API names stable and preferably English for machine readability.

Critical state must be explicit and easy to parse:

- current development phase (planning / development / testing)
- current lifecycle phase or release
- current task, task status, dependencies, and next action
- blocker and unblock action when blocked
- acceptance or approval state
- API/UI contract status when relevant
- verification status and known gaps

If prose and structured metadata conflict, treat the structured metadata as the state to resolve, then update prose or metadata so they match.

## Reference Map

Load references progressively:

- `references/project-memory-templates.md`: concrete templates for initialization, repair, and migration. Includes STATE.md template.
- `references/project-types.md`: choose the right continuity flow for UI apps, backend/API services, libraries, CLI tools, infra, data projects, or mixed products.
- `references/lifecycle-and-feature-expansion.md`: lifecycle planning, release/phase finish lines, and new feature classification.
- `references/task-and-handoff-system.md`: task board, task files, handoff, dev-log, resume, and compacting rules. Includes immutable task rule.
- `references/frontend-continuity.md`: code-as-design frontend flow, Design Anchor, Design Extension, mock/service rules, and UI continuity.
- `references/acceptance.md`: user acceptance, owner approval, and local verification levels.
- `references/external-state.md`: databases, cloud environments, deployments, auth providers, storage, and other external state.

## Setup Workflow

For initialization of a new project:

1. Inspect repository shape and existing instruction files.
2. Create `AGENTS.md` using templates from `references/project-memory-templates.md`.
3. Create `docs/STATE.md` (initial state: Phase 1, Step 1).
4. Determine project type with `references/project-types.md`.
5. Begin Step 1 (Product Document) — discuss requirements one question at a time.
6. After user confirms product doc, proceed to Step 2 (Tech Stack).
7. After user confirms tech stack, create minimal doc set for the project type.
8. Update STATE.md to reflect planning phase completion.
9. Record enough handoff state for short resume prompts.

Use the current session's internal planning/progress tools for the setup phase. Do not create extra persistent setup-progress files unless the user asks.

## Resume Protocol

When a new agent encounters an existing project:

1. **Read `docs/STATE.md`** (if exists). This gives: current phase, current step, current task, next action, key decisions.
2. If STATE.md is missing or stale, read `docs/handoff.md` + `docs/tasks.md`.
3. If `current_task` exists, read that task file.
4. Follow `next_action` from STATE.md or handoff.md.
5. If blocked, ask only for the missing decision or resource.

When the user says "continue development", "继续开发", "继续昨天的任务", or similar:
- Follow the resume protocol above.
- Report: current phase, current step, current task, and exact next action.
- Then execute the next action.

When the user asks for status ("当前进行到哪一步", "进度汇报", "where are we"):
- Read STATE.md first.
- Report: development phase, lifecycle step, current task, blockers, acceptance state, and next action.

## Acceptance Levels

Do not force every change through the same user acceptance gate. Use `references/acceptance.md` to choose:

- `user_acceptance`: user-visible UI, workflows, release behavior, externally visible integration behavior.
- `owner_approval`: long-lived product, API, architecture, data-model, security, or release-scope decisions.
- `local_verification`: internal refactors, tests, maintenance, and behavior-preserving implementation changes.

Vague replies such as "继续", "下一步", "看起来不错", "looks good", or "continue" do not count as acceptance when explicit acceptance is required.

## Execution Rules

During implementation:

- Read existing patterns before adding new structures.
- Keep changes inside the active task scope.
- Prefer small, verifiable increments.
- Add abstractions only after real repetition or replacement needs appear.
- Record long-lived decisions in `docs/decisions/`.
- If scope expands, follow the Change Protocol — do not silently do unrelated work.
- Route future backend or external-service replacement through service/adapters rather than direct mock imports.
- Use `references/external-state.md` before changing databases, cloud environments, deployments, auth providers, storage, or other external state.

Before ending meaningful work:

1. Run relevant build, test, lint, preview, or manual verification.
2. Update docs whose source of truth changed.
3. Update `docs/STATE.md` with current phase, step, task, and next action.
4. Update `docs/handoff.md` with detailed state, key files, cautions, and a stable `next_action`.
5. Check active release/phase exit criteria.

If verification cannot be completed, record exactly what was not verified and why.

## Stable next_action Values

- `continue_implementation`
- `wait_for_user_acceptance`
- `wait_for_owner_approval`
- `wait_for_release_acceptance`
- `ask_product_question`
- `fix_verification_failure`
- `start_next_ready_task`
- `verify_external_state`
- `blocked`

If `next_action` is missing or ambiguous, infer the safest next action from task status and handoff notes, then update `docs/STATE.md` and `docs/handoff.md`.

## Mode Router (Advanced)

For operations beyond the linear lifecycle, choose the lightest mode that fits:

- `Repair`: fix missing, stale, contradictory, or unusable continuity docs. Read the conflicting docs, current code state, and the relevant reference files.
- `Feature Expansion`: place a new feature into an established lifecycle. Read existing memory and `references/lifecycle-and-feature-expansion.md`.
- `Compact Memory`: reduce bloated docs. Read `docs/handoff.md`, `docs/tasks.md`, active tasks, recent `docs/dev-log.md`, and `references/task-and-handoff-system.md`.
- `Migration`: adapt continuity docs for another agent surface or repository shape. Read entrypoint rules in `references/project-memory-templates.md`.

Use these modes when the user's request does not fit the linear lifecycle flow.
