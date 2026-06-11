# Agent Project Continuity

> 让 AI 代理跨会话、跨工具无缝恢复软件项目。Enable AI agents to resume work across sessions and tools without context loss.

[中文](#中文) | [English](#english)

---

## 中文

### 1. 简介

**问题**：你用 Claude Code 写了一半代码，明天换成 Codex 继续——新来的 AI 完全不记得昨天聊了什么、写到哪里、用户做过哪些决定。每次换工具都像从零开始。

**答案**：Agent Project Continuity 是一套**轻量级的项目记忆系统**。它在你项目的 `docs/` 目录下维护一组结构化文档，让任何一个 AI 编程助手在**一分钟内**搞清楚三件事——**我在哪、我在干什么、接下来做什么**。

核心理念很简单：**仓库文档 = 持久记忆，对话窗口 = 临时上下文**。

系统围绕一个核心文件 `docs/STATE.md`（不到 40 行的状态快照）运转。新 AI 来了只需读这一个文件就能定向，不需要先"吃下"整个项目。

### 2. 工作流

软件从想法到上线的完整过程，拆成三个阶段、九步走：

```
规划阶段（一次会话完成）
  Step 1  产品文档  →  聊需求、定 MVP、划版本边界  →  用户拍板
  Step 2  技术栈   →  前端/后端/数据库/部署      →  用户拍板

开发阶段（逐步推进）
  Step 3  页面风格  →  挑一页做"视觉样板"，打磨到满意 → 用户验收
  Step 4  前端开发  →  按样板风格实现全部页面
  Step 5  Mock 数据 →  接假数据，六种状态全覆盖
  Step 6  初步验收  →  用户在假数据下验收页面与交互
  Step 7  后端开发  →  按 API 契约实现后端服务
  Step 8  前后联调  →  假数据切真实 API，端到端跑通

测试阶段
  Step 9  全量测试  →  生成测试用例，用户手动验收 → 准备上线
```

每一步都有明确门禁——上一步验收不通过，不跳下一步。需求中途变化也不会乱：有标准的变更协议（暂停→更新产品文档→用户确认→重新排期）。

后续版本的详细开发任务不会提前拆分——等到用户看到前一版本并验收通过后才开始规划。避免用户看到实际产品后需求变了，早期详细计划全白做。

### 3. 特点

**一文件定向**。新 AI 读取 `STATE.md`（< 40 行）即可知道当前阶段、当前任务、下一步行动。仅在需要更深入上下文时才渐进读取更多文件。相比传统做法（翻 5-10 个文件才能定向），一次读取即可开始工作。

**铁的纪律，不是建议**。
- 已完成的任务不可修改——出问题另开修正任务，保留完整开发历史
- 模糊指令不算数——"继续""看起来不错"不视为验收通过，必须用户明确表态
- 需求变更必须走流程——暂停→更新文档→用户确认→重新排期，不偷偷扩大范围
- 每次收工必须更新快照——下一个接班的 AI 拿到的永远是新鲜状态

**为多工具切换而设计**。无论是 Claude Code ↔ Codex ↔ Cursor 之间切换，还是在同一个工具里开新会话——系统保证下一个 AI 来的时候看到的是一份准确、一致的"工地状态报告"。

**按需生长，不铺张**。纯后端项目不创建 `page-map.md` 和 `ui.md`，CLI 工具不创建前端验收表。系统根据项目类型创建最小必要文档集，不做面子工程。

**分级更新，不扰民**。不是每次交互都写文档——问个"进度到哪了"不触发任何更新，改个小 UI 只记 dev-log，完成任务才触发完整更新链。噪声控制到最低。

### 4. 如何使用

**方式一：Claude Code Skill**

将本仓库放入项目目录，通过 `/agent-project-continuity` 或自然语言触发：

```
# 初始化新项目
"帮我初始化这个项目的延续性文档"

# 之后每次开始工作
"继续开发"
```

**方式二：其他 AI 编程工具（Codex、Cursor 等）**

将 `SKILL.md` 作为系统指令或上下文提供给代理，`references/` 目录作为补充参考。

**方式三：手动使用**

参考 `references/project-memory-templates.md` 获取全部文档模板，按需创建。每次收工前更新 `docs/STATE.md`。

---

## English

### 1. Introduction

**The problem**: You write half your app with Claude Code, then switch to Codex the next day — the new agent remembers nothing. No history, no decisions, no idea where the last agent left off. Every tool switch feels like starting over.

**The answer**: Agent Project Continuity is a **lightweight project memory system**. It maintains a set of structured documents in your project's `docs/` directory so any AI coding agent can answer three questions within **one minute** — *where am I, what am I doing, what's next*.

Core principle: **repository docs = durable memory, chat window = temporary context**.

The system revolves around a single file — `docs/STATE.md`, a sub-40-line snapshot. A new agent orients from this file alone, without needing to ingest the entire project first.

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

**One-file orientation**. A new agent reads `STATE.md` (< 40 lines) and immediately knows the current phase, task, and next action. Additional files load progressively only when the task demands deeper context. Versus the traditional approach of scanning 5-10 files just to orient — one read, then work.

**Rules with teeth, not suggestions**.
- Completed tasks are immutable — corrections open a new task linked to the original, preserving full history
- Vague prompts don't count — "looks good" and "continue" are not acceptance; the user must explicitly confirm
- All requirement changes follow a protocol — pause → document → classify → confirm → reschedule
- State is always fresh — STATE.md and handoff.md are updated before every session end

**Designed for multi-tool workflows**. Claude Code ↔ Codex ↔ Cursor — regardless of which tool starts the next session, the next agent sees an accurate, consistent "construction site status report."

**Grows on demand**. Pure backend projects don't get `page-map.md` or `ui.md`. CLI tools don't get frontend acceptance tables. The system creates the minimal document set for the project type. No ceremonial paperwork.

**Tiered updates, minimal noise**. Not every interaction triggers document writes — a status query produces zero updates, a small UI fix only touches dev-log, task completion triggers the full update chain. Noise is kept to a minimum.

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

Use `references/project-memory-templates.md` for all document templates. Create documents as needed. Update `docs/STATE.md` before ending each work session.
