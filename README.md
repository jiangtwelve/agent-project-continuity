# Agent Project Continuity

> Enable AI agents to resume work across sessions and tools without context loss.

[🇨🇳 中文版](./README.zh.md)

---

### 1. Introduction

**The problem**: You write half your app with Claude Code, then switch to Codex the next day — the new agent remembers nothing. No history, no decisions, no idea where the last agent left off. Every tool switch feels like starting over.

**The answer**: Agent Project Continuity is a **lightweight project memory system**. It maintains a set of structured documents in your project's `.continuity/` directory so any AI coding agent can answer three questions within **one minute** — *where am I, what am I doing, what's next*.

Core principle: **repository docs = durable memory, chat window = temporary context**.

The system revolves around a single file — `.continuity/STATE.md`, a sub-40-line snapshot. A new agent orients from this file alone, without needing to ingest the entire project first.

```
AGENTS.md                    ← Agent rules at root (auto-detected by tools)
.continuity/                 ← All memory files isolated from project docs
├── STATE.md                 ← Snapshot: where am I, what am I doing, what's next
├── product.md               ← Product definition: what, for whom, boundaries
├── architecture.md          ← Tech stack: tools, run/verify commands
├── roadmap.md + releases/   ← Version plan: releases, scope, exit criteria
├── page-map.md + ui.md      ← Page map + visual contract
├── tasks.md + tasks/        ← Task board + per-task specification
├── handoff.md               ← Handoff note: cautions for the next agent
├── dev-log.md               ← Dev log: topic-indexed, pick what to read
├── api.md                   ← API contract between frontend and backend
└── decisions/               ← Archived decisions: "why did we choose this?"
```

### 2. Workflow

A full software lifecycle, broken into three phases and nine steps:

```
Planning Phase (complete in one session)
  Step 1  Product Doc  →  Requirements, MVP scope, version boundaries  →  User confirms
  Step 2  Tech Stack   →  Frontend, backend, database, deployment      →  User confirms

Development Phase (step-by-step)
  Step 3  Page Style   →  Build one "Design Anchor" page, iterate to approval
  Step 4  Frontend     →  Implement all pages following the anchor style
  Step 5  Mock Data    →  Wire up mock services covering all six states
  Step 6  Acceptance   →  User validates pages and interactions with mock data
  Step 7  Backend      →  Implement backend against API contracts
  Step 8  Integration  →  Replace mocks with real APIs, verify end-to-end

Testing Phase
  Step 9  Full Testing →  Generate test cases, user manual acceptance → Deploy ready
```

Each step has an explicit gate — the previous step's criteria must pass before proceeding. When requirements change mid-development, a standardized change protocol kicks in: pause → update product doc → user confirms → reschedule. No silent scope creep.

Detailed task plans for later versions are deferred until the previous version passes user acceptance — avoiding wasted effort when user needs inevitably shift after seeing working software.

### 3. Features

**One-file orientation**. A new agent reads `STATE.md` (< 40 lines) and immediately knows the current phase, task, and next action. Additional files load progressively only when the task demands deeper context. Three files orients a fresh agent, versus the traditional 5-10.

**Rules with teeth, not suggestions**.
- Completed tasks are immutable — corrections open a new task linked to the original, preserving full history
- Vague prompts don't count — "looks good" and "continue" are not acceptance; the user must explicitly confirm
- All requirement changes follow a protocol — pause → document → classify → confirm → reschedule
- State is always fresh — STATE.md and handoff.md are updated before every session end

**Designed for multi-tool workflows**. Claude Code ↔ Codex ↔ Cursor — regardless of which tool starts the next session, the next agent sees an accurate, consistent "construction site status report."

**Grows on demand**. Pure backend projects don't get `page-map.md` or `ui.md`. CLI tools don't get frontend acceptance tables. The system creates the minimal document set for the project type. No ceremonial paperwork.

**Tiered updates, minimal noise**. Not every interaction triggers document writes — a status query produces zero updates, a small UI fix only touches dev-log, task completion triggers the full update chain.

### 4. How to Use

**Option 1: Claude Code Skill**

Place this repository in your project directory. Trigger via `/agent-project-continuity` or natural language:

```
# Initialize a new project
"Initialize the continuity docs for this project"

# Every time you resume work
"Continue development"
```

**Option 2: Other AI Coding Tools (Codex, Cursor, etc.)**

Provide `SKILL.md` as system instructions or context to the agent, with the `references/` directory as supplementary material.

**Option 3: Manual**

Use `references/project-memory-templates.md` for all document templates. Create documents as needed. Update `.continuity/STATE.md` before ending each work session.
