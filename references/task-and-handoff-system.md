# Task And Handoff System

Use this reference for task boards, task files, resume state, dev-log maintenance, and memory compaction.

## State Layering

Three layers carry resume state, from cheapest to richest:

1. `.continuity/STATE.md` — snapshot. Under 40 lines. A fresh agent orients from this file alone.
2. `.continuity/handoff.md` — latest actionable detail: cautions, key files, pending input, verification state.
3. `.continuity/dev-log.md` — history with a topic index. Read by topic, never whole-file by default.

STATE.md and handoff.md frontmatter must agree on `current_task`, `next_action`, `blocked`, and acceptance state. Update them together at every task status change, phase transition, user confirmation, and session end. If they conflict, treat the most recently updated file as the state to resolve, then repair both.

## Task Board

Use `.continuity/tasks.md` as a status board, not the full development document. Keep implementation detail in `.continuity/tasks/TASK-xxx.md`.

Recommended display sections:

- `Current`
- `Ready`
- `Blocked`
- `Backlog`
- `Done`

If useful, a display subsection such as `Current - Acceptance Pending` may be used, but structured task status remains `current`.

## Task Metadata

Use stable structured values:

- `status`: `ready | current | blocked | done`
- `acceptance_status`: `not_required | pending | accepted | failed`
- `next_action`: `continue_implementation | wait_for_user_acceptance | wait_for_owner_approval | wait_for_release_acceptance | ask_product_question | fix_verification_failure | start_next_ready_task | verify_external_state | blocked`

Prefer `requires_user_acceptance` for the user-visible acceptance flag. Do not also use `acceptance_required` for the same meaning.

Useful task fields:

- `id`
- `title`
- `type`
- `release` or `phase`
- `release_required`
- `release_exit_criteria`
- `module`
- `priority`
- `depends_on`
- `blocked_by`
- `blocks`
- `corrects` (correction task linking to original done task — see Immutable Task Rule in SKILL.md)
- `requires_user_acceptance`
- `acceptance_status`
- `verification_status`
- `updated`

## Atomic Task Rules

An atomic task should:

- fit in one focused session when possible
- have clear scope and non-scope
- include enough context for a new agent to execute without redesigning
- define acceptance or approval requirements
- define verification
- avoid line-by-line code instructions unless the implementation is fragile

Split or create a follow-up task when work crosses multiple major subsystems, spans multiple meaningful sessions, changes the active release goal, or accumulates repeated feedback outside the original scope.

## Transitions

- `Ready`: enough product, design, API, technical, and dependency context to start.
- `Current`: actively being implemented, waiting for scoped acceptance, or awaiting final docs updates.
- `Blocked`: cannot continue without user input, missing dependency, permission, or external state change.
- `Done`: implementation, verification, required docs updates, and required acceptance or approval are complete.

Do not start a new task unless the current task is `done`, explicitly `blocked`, or the user explicitly asks to switch. The next task must have an existing task file before implementation starts, unless creating it is the current planning action.

## Documentation Update Matrix

- Status query or resume answer: do not update docs unless docs contradict each other or current code state.
- Small bug fix, small UI adjustment, or local verification note: update `.continuity/dev-log.md`; update `.continuity/handoff.md` or the active task only if next action, blocker, acceptance status, or task scope changed.
- Task completion: update `.continuity/tasks.md`, the active task file, `.continuity/dev-log.md`, and `.continuity/handoff.md`.
- Release/phase progress change: also update `.continuity/roadmap.md` and the active `.continuity/releases/` file.
- Product, architecture, page map, UI contract, API contract, or decision change: update the corresponding source document.

If required documentation cannot be updated, keep the task `current` or `blocked` and record the reason.

## Handoff

Keep `.continuity/handoff.md` focused on latest actionable state:

- current release/phase
- current task
- next action
- blocker and unblock action
- acceptance or approval state
- verification state
- key files
- cautions

Put operation history and detailed reasoning in `.continuity/dev-log.md`.

## Dev Log

Create `.continuity/dev-log.md` with a reading guide and topic index during initialization. Update the index whenever a new durable theme, release, task cluster, or external-state thread appears.

The dev log should answer:

- what changed
- why it changed
- how it was verified
- what gaps remain
- where to read more

Do not force future agents to load the whole log by default.

## Compacting

When docs are bloated:

1. Preserve current structured state first (`.continuity/STATE.md` and `.continuity/handoff.md` frontmatter).
2. Keep `.continuity/STATE.md` under 40 lines and `.continuity/handoff.md` short and actionable.
3. Keep active task files focused on current scope, latest verification, acceptance state, and remaining work.
4. Move history into `.continuity/dev-log.md` summaries and index entries.
5. Archive or summarize old task files when they no longer help active development.
6. Resolve metadata/prose conflicts while compacting.
