# Agent Project Continuity

> A lightweight project memory system for AI coding agents. One file to resume. Works across Claude Code, Codex, Cursor, and other tools.

[中文](./README.zh.md)

---

## What

Switching between AI coding tools loses context. Agent Project Continuity keeps project state in a `.continuity/` directory inside the repository, so any agent picks up where the last one left off — read a single snapshot file, then start working. No re-explaining.

```
AGENTS.md                    # agent operating rules
.continuity/
├── STATE.md                 # snapshot: current phase, task, next action
├── product.md               # product scope, MVP boundaries
├── architecture.md          # tech stack, run and verify commands
├── roadmap.md + releases/   # version plan
├── page-map.md + ui.md      # pages, routes, visual rules
├── tasks.md + tasks/        # task board and specifications
├── handoff.md               # session-end note for the next agent
├── dev-log.md               # development log, indexed by topic
├── api.md                   # API contracts
└── decisions/               # archived decisions
```

## Workflow

Three phases, nine sequential steps. Each step gates on the previous one.

**Planning** (one session)
- Step 1 — Product doc: requirements, MVP scope, version boundaries
- Step 2 — Tech stack: frontend, backend, database, deployment

**Development**
- Step 3 — Page style: build a Design Anchor page, iterate to approval
- Step 4 — Frontend: implement all pages following the anchor
- Step 5 — Mock data: wire up mock services covering six states
- Step 6 — Preliminary acceptance: user validates under mock data
- Step 7 — Backend: implement against API contracts
- Step 8 — Integration: swap mocks for real APIs, verify end-to-end

**Testing**
- Step 9 — Full testing: test case table, manual acceptance, deploy checklist

Requirement changes mid-development follow a protocol: pause, update product doc, get explicit user confirmation, reschedule. Tasks for later versions stay at finish-line granularity until the current version passes acceptance.

## Features

**One-file resume.** Agents read `STATE.md` (< 40 lines) to orient: current phase, task, next action, blockers. Additional files load on demand.

**Immutable history.** Completed tasks are never modified. Corrections open a new task linked to the original. Vague prompts ("looks good", "continue") are not acceptance.

**Tool-agnostic.** Works across Claude Code, Codex, Cursor — any agent that can read markdown files.

**Minimal by default.** Backend projects skip page-map and UI docs. CLI tools skip frontend acceptance. The system creates only what the project type needs.

**Tiered writes.** Status queries produce no updates. Small fixes only touch the dev log. Task completion triggers the full update chain.

## Usage

**Claude Code.** Place this repo in the project, trigger with `/agent-project-continuity`:

```
"Initialize the continuity docs for this project"
"Continue development"
```

**Other agents.** Provide `SKILL.md` as system instructions. Keep `references/` available.

**Manual.** Use templates in `references/project-memory-templates.md`. Update `.continuity/STATE.md` before each session end.
