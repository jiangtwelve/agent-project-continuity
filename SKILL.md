---
name: agent-project-continuity
description: Bootstrap, repair, compact, migrate, or extend durable project memory for long-running agent software projects. Use when a repository needs AGENTS.md, structured docs, task files, handoff state, lifecycle planning, cross-agent resume rules, feature placement in an existing lifecycle, or cleanup of bloated/inconsistent continuity docs. Do not use for ordinary implementation once the repository's own AGENTS.md, handoff, and task docs are sufficient.
---

# Agent Project Continuity

Use this skill to make a software project resumable by capable agents across sessions and tools. Treat repository documents as durable memory and chat as temporary working context.

This skill is mainly for bootstrapping, repairing, compacting, migrating, or extending a repository's continuity system. After `AGENTS.md` and the project docs are established, ordinary coding should follow those repository documents directly.

## Mode Router

Choose the lightest mode that fits the user request, then load only the referenced files needed for that mode.

- `Initialize`: create the first continuity system. Read this file, inspect the repo, then use `references/project-memory-templates.md`.
- `Repair`: fix missing, stale, contradictory, or unusable continuity docs. Read the conflicting docs, current code state, and the relevant reference files below.
- `Resume`: recover from a short continuation prompt only when project docs are insufficient by themselves. Read `AGENTS.md`, `docs/handoff.md`, `docs/tasks.md`, and the active task.
- `Status`: report phase, task, blocker, verification, acceptance state, and next action. Do not update docs unless state is contradictory or stale.
- `Feature Expansion`: place a new feature into an established lifecycle. Read existing memory and `references/lifecycle-and-feature-expansion.md`.
- `Compact Memory`: reduce bloated docs. Read `docs/handoff.md`, `docs/tasks.md`, active tasks, recent `docs/dev-log.md`, and `references/task-and-handoff-system.md`.
- `Migration`: adapt continuity docs for another agent surface or repository shape. Read entrypoint rules in `references/project-memory-templates.md`.

## Reference Map

Load references progressively:

- `references/project-memory-templates.md`: concrete templates for initialization, repair, and migration.
- `references/project-types.md`: choose the right continuity flow for UI apps, backend/API services, libraries, CLI tools, infra, data projects, or mixed products.
- `references/lifecycle-and-feature-expansion.md`: lifecycle planning, release/phase finish lines, and new feature classification.
- `references/task-and-handoff-system.md`: task board, task files, handoff, dev-log, resume, and compacting rules.
- `references/frontend-continuity.md`: code-as-design frontend flow, Design Anchor, Design Extension, mock/service rules, and UI continuity.
- `references/acceptance.md`: user acceptance, owner approval, and local verification levels.
- `references/external-state.md`: databases, cloud environments, deployments, auth providers, storage, and other external state.

## Core Invariants

Keep one source of truth for each concern:

- `AGENTS.md`: operating rules for agents in this repository.
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

Create only the minimal set needed for the current project and request. Do not create ceremonial docs.

## Document Format

Use Markdown for project memory unless the repository has a stronger local convention. Use YAML frontmatter for machine-readable state in task, handoff, API, UI, release, and decision documents.

Write human-facing documentation in the user's primary working language. Keep file paths, YAML keys, enum values, code identifiers, and API names stable and preferably English for machine readability.

Critical state must be explicit and easy to parse:

- current lifecycle phase or release
- release/phase status and exit criteria
- current task, task status, dependencies, and next action
- blocker and unblock action when blocked
- acceptance or approval state
- API/UI contract status when relevant
- verification status and known gaps

If prose and structured metadata conflict, treat the structured metadata as the state to resolve, then update prose or metadata so they match.

## Setup Workflow

For initialization, design, or major repair:

1. Inspect repository shape and existing instruction files.
2. Create or update `AGENTS.md` first, including setup/design continuation rules so the user can say "继续设计", "下一个问题", or "继续".
3. Determine project type with `references/project-types.md`.
4. Confirm product direction, lifecycle/finish line, and implementation-affecting technical choices before generating detailed tasks.
5. Create only the docs required for the selected project type and current lifecycle.
6. Use `references/project-memory-templates.md` for concrete file templates.
7. Record enough handoff state for short resume prompts such as "continue development", "继续开发", "continue yesterday's task", or "继续昨天的任务".

Use the current Codex session's internal planning/progress tools for the setup phase. Do not create extra persistent setup-progress files unless the user asks.

## Resume And Status

When resuming work:

1. Read `AGENTS.md` if present.
2. Read `docs/handoff.md`.
3. Read `docs/tasks.md`.
4. If `handoff.current_task` exists, read that task file first.
5. If no current task is identified, select the first task in `Current`; if none exists, select the first `Ready` task.
6. Follow `handoff.next_action`.
7. If blocked and the blocker cannot be resolved locally, ask only for the missing decision or resource.

When the user asks for status, progress, current phase, next step, or release readiness, read project state before answering. Include the current release/phase, current task, blocker if any, acceptance/verification state, finish-line progress, and exact next action.

Use stable `next_action` values:

- `continue_implementation`
- `wait_for_user_acceptance`
- `wait_for_owner_approval`
- `wait_for_release_acceptance`
- `ask_product_question`
- `fix_verification_failure`
- `start_next_ready_task`
- `verify_external_state`
- `blocked`

If `next_action` is missing or ambiguous, infer the safest next action from task status and handoff notes, then update `docs/handoff.md`.

## Planning Rules

During product discussion and early planning, ask one key decision question at a time. Prefer 2-3 concrete options with one recommended default when useful, and keep a free-form answer path open.

Before task planning is complete, the project must have an explicit lifecycle plan. The lifecycle can use versions, phases, milestones, or another scheme chosen with the user.

New features discovered during development must be classified before implementation:

- current release/phase only when necessary for agreed exit criteria or to unblock current work
- later release/phase when valuable but not required for the current finish line
- backlog when optional, exploratory, or not yet planned

Do not silently expand the active release/phase. If a feature changes the active finish line, ask for user confirmation and update roadmap, release, task, and handoff state.

## Task And Completion Rules

Use `docs/tasks.md` as the board and `docs/tasks/TASK-xxx.md` for atomic task detail. See `references/task-and-handoff-system.md`.

Stable task metadata statuses are `ready`, `current`, `blocked`, and `done`. Track acceptance separately with `acceptance_status: not_required | pending | accepted | failed`.

Do not start a new task unless the current task is `done`, explicitly `blocked`, or the user explicitly asks to switch. The next task must have an existing task file before implementation starts, or be created during documented task planning.

Task completion requires implementation, relevant verification, required documentation updates, and any required acceptance or approval. If documentation cannot be updated, keep the task `current` or `blocked` and record the reason.

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
- If scope expands, create or update a task instead of silently doing unrelated work.
- Route future backend or external-service replacement through service/adapters rather than direct mock imports.
- Use `references/external-state.md` before changing databases, cloud environments, deployments, auth providers, storage, or other external state.

Before ending meaningful work:

1. Run relevant build, test, lint, preview, or manual verification.
2. Update docs whose source of truth changed.
3. Check active release/phase exit criteria.
4. Update `docs/handoff.md` with current state, next step, key files, cautions, and a stable `next_action`.

If verification cannot be completed, record exactly what was not verified and why.
