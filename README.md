# Agent Project Continuity

> 让 AI 代理能够跨会话、跨工具无缝恢复软件项目的工作。
>
> Enable AI agents to seamlessly resume software project work across sessions and tools.

[中文](#中文文档) | [English](#english)

---

## 中文文档

### 这是什么？

**Agent Project Continuity** 是一个为 AI 代理（如 Claude Code、Codex、Cursor 等）设计的**项目记忆系统技能（Skill）**。

它解决了一个核心问题：当你关闭聊天窗口或切换到另一个 AI 工具后，下一个代理如何快速理解项目现状、接手未完成的工作，而不是从零开始？

答案是：**将仓库文档作为持久记忆，将对话作为临时工作上下文。**

### 核心理念

```
┌─────────────────────────────────────────────┐
│              仓库即记忆                       │
│                                             │
│  AGENTS.md ──── 代理操作规则                  │
│  docs/       ──── 项目状态文档                │
│    ├── STATE.md         轻量快照（新代理入口）  │
│    ├── product.md      产品意图               │
│    ├── architecture.md 技术架构               │
│    ├── roadmap.md      生命周期规划            │
│    ├── tasks.md        任务看板               │
│    ├── handoff.md      交接详情               │
│    └── ...                                  │
│                                             │
│  新代理 = 读 STATE.md（一次性定向）             │
│  对话   = 临时上下文（会话结束即丢失）          │
│  文档   = 持久记忆（跨会话保留）               │
└─────────────────────────────────────────────┘
```

### 适用场景

| 场景 | 说明 |
|------|------|
| **初始化** | 为新项目或已有项目创建第一套延续性文档系统 |
| **修复** | 修复缺失、过时、矛盾或不可用的延续性文档 |
| **恢复** | 从简短的继续指令中恢复工作（如"继续开发"、"继续昨天的任务"） |
| **状态查询** | 汇报当前阶段、任务、阻塞项、验收状态和下一步行动 |
| **功能扩展** | 将新功能放入已建立的生命周期中，防止范围蔓延 |
| **压缩记忆** | 精简臃肿的文档，保留核心状态 |
| **迁移** | 将延续性文档适配到另一个代理工具或仓库结构 |

### 文档结构

```text
AGENTS.md                        # 代理操作规则（入口文件）
CLAUDE.md                        # Claude Code 专用入口（可选）
docs/
├── STATE.md                     # 轻量快照（新代理第一个读的文件，<40 行）
├── product.md                   # 产品意图、MVP、用户流程、边界
├── architecture.md              # 技术栈、系统形态、运行/验证命令
├── page-map.md                  # 页面、路由、导航、状态、数据依赖
├── roadmap.md                   # 生命周期规划、发布/阶段结构
├── releases/
│   └── RELEASE-001-xxx.md       # 每个发布/阶段的目标、范围、退出条件
├── ui.md                        # 视觉和交互规则（UI 产品）
├── api.md                       # API 契约
├── tasks.md                     # 任务看板和索引
├── tasks/
│   └── TASK-001-xxx.md          # 原子任务详情
├── dev-log.md                   # 持久开发历史（带主题索引）
├── handoff.md                   # 最新交接详情（STATE.md 不足时补充）
└── decisions/
    └── 001-xxx.md               # 长期决策记录
```

> 不是所有项目都需要全部文件。系统会根据项目类型自动路由，只创建当前需要的文档。

### 七大工作模式

#### 1. 初始化（Initialize）

为项目从零搭建延续性文档系统。流程：

1. 检查仓库现状和已有指令文件
2. 创建或更新 `AGENTS.md`（代理入口）
3. 确定项目类型（UI 产品 / 后端 API / 库 / CLI / 基础设施 / 数据 AI / 混合）
4. 与用户确认产品方向、生命周期、技术选型
5. 按需生成最小文档集
6. 记录交接状态，支持"继续开发"等短指令恢复

#### 2. 修复（Repair）

修复延续性文档中的问题：
- 缺失的关键文件
- 文档与代码状态不一致
- 元数据与正文描述矛盾
- 交接信息过时

#### 3. 恢复（Resume）

当用户说"继续开发"、"continue development"、"继续昨天的任务"时，新代理通过**渐进式读取**快速定向：

1. 读取 `docs/STATE.md`（快照，一次性定向——如果新鲜且一致，到此为止）
2. 读取当前任务文件（如果 `current_task_file` 存在）
3. 仅当 STATE.md 缺失/过时/不足时才读 `docs/handoff.md`
4. 仅当无法识别当前任务时才读 `docs/tasks.md`
5. 按 `next_action` 执行下一步

最短读取集：**AGENTS.md + STATE.md + 活动 TASK 文件**。

#### 4. 状态查询（Status）

当用户问"当前进行到哪一步"、"进度汇报"、"what is next"时：

1. 先读 `docs/STATE.md`——如果新鲜，直接从中回答（开发阶段、step、当前任务、阻塞项、验收状态、next_action）
2. 仅当 STATE.md 缺失/过时/问题需要更多细节时才读 `docs/handoff.md`
3. 仅当需要发布级进度或任务级细节时才读 `docs/roadmap.md`、`docs/tasks.md` 等

#### 5. 功能扩展（Feature Expansion）

将新功能放入已建立的生命周期，防止范围蔓延：

- **当前发布/阶段**：仅当它是退出条件所必需的，或能解除当前阻塞
- **后续发布/阶段**：有价值但不影响当前完成线。注意：后续发布的详细任务拆分在前一发布**验收通过后**才进行——过早规划会因用户看到可用版本后需求变化而浪费精力
- **积压（Backlog）**：可选的、探索性的、尚未规划的

#### 6. 压缩记忆（Compact Memory）

当文档变得臃肿时：
- 保留当前结构化状态
- 将历史移入 `dev-log.md` 摘要
- 归档不再活跃的任务文件
- 解决元数据冲突

#### 7. 迁移（Migration）

将延续性文档适配到不同的代理工具或仓库结构。

### 项目类型路由

不同类型的项目关注不同的文档重点：

| 项目类型 | 重点关注 |
|---------|---------|
| **UI 产品** | product, page-map, ui, api, Design Anchor 流程 |
| **后端/API** | 服务边界, API 契约, 数据模型, 集成测试 |
| **库/SDK** | 公共 API, 兼容性矩阵, 语义化版本, 迁移说明 |
| **CLI 工具** | 命令语法, 退出码, dry-run 规则, fixture 测试 |
| **基础设施/运维** | 环境 ID, 权限模型, 回滚计划, 可观测验证 |
| **数据/AI** | 输入输出 schema, 评估标准, 可复现性, 数据隐私 |
| **混合产品** | 按表面拆分任务，记录跨表面依赖 |

### 任务系统

#### 任务看板

```markdown
## Current      ← 正在执行
## Ready        ← 可开始
## Blocked      ← 被阻塞
## Backlog      ← 待规划
## Done         ← 已完成
```

#### 任务状态流转

```
Ready → Current → Done
           ↓
        Blocked → Ready / Current
```

#### 稳定的 `next_action` 值

| 值 | 含义 |
|---|------|
| `continue_implementation` | 继续实现 |
| `wait_for_user_acceptance` | 等待用户验收 |
| `wait_for_owner_approval` | 等待所有者审批 |
| `wait_for_release_acceptance` | 等待发布验收 |
| `ask_product_question` | 需要产品决策 |
| `fix_verification_failure` | 修复验证失败 |
| `start_next_ready_task` | 开始下一个就绪任务 |
| `verify_external_state` | 验证外部状态 |
| `blocked` | 被阻塞 |

### 验收层级

不是所有改动都需要同一级别的验收：

| 层级 | 适用场景 | 要求 |
|------|---------|------|
| **用户验收** | 可见 UI、交互、工作流、发布行为 | 用户显式确认验收表格 |
| **所有者审批** | 产品/API/架构/安全/数据模型的长期决策 | 记录决策到对应文档 |
| **本地验证** | 内部重构、测试、工具、保持行为的改动 | 记录命令输出或验证结果 |

> ⚠️ "继续"、"下一步"、"看起来不错"、"looks good" 等模糊回复**不算**验收确认。

### 如何使用

#### 方式一：作为 Claude Code Skill 使用

1. 将本仓库克隆到你的项目中，或将 `SKILL.md` 和 `references/` 目录放入你的项目根目录
2. 在 Claude Code 中通过 `/agent-project-continuity` 或自然语言触发技能
3. 告诉它你要做什么，例如：
   - "初始化这个项目的延续性文档"
   - "修复 docs/handoff.md，它和当前代码不一致"
   - "继续开发"

#### 方式二：作为 Codex / 其他代理的指令

1. 将 `SKILL.md` 的内容作为系统指令或上下文提供给代理
2. 将 `references/` 目录中的参考文件作为补充资料
3. 代理将按照技能中定义的规则工作

#### 方式三：手动使用

1. 阅读 `references/project-memory-templates.md` 获取所有文档模板
2. 按需创建文档，根据 `references/project-types.md` 选择适合你项目类型的文档集
3. 维护 `docs/STATE.md`（每次工作结束必更新）和 `docs/handoff.md` 作为交接记录

#### 快速开始示例

```
# 1. 在项目中初始化延续性系统
用户：帮我初始化这个项目的延续性文档

# 2. 系统会引导你完成：
#    - 确认产品方向
#    - 确定项目类型
#    - 规划生命周期和发布计划
#    - 创建最小文档集

# 3. 之后每次开始工作时：
用户：继续开发

# 4. 代理会自动读取 STATE.md 快速定向
#    定位当前任务并按 next_action 执行
```

### 外部状态协议

当涉及数据库、云环境、部署、认证提供商、存储等仓库外状态时，遵循：

1. **操作前**：记录环境、操作、影响资源、预期结果、回滚路径
2. **操作中**：使用机器可读输出，避免破坏性操作
3. **操作后**：验证并记录可观测状态

### 前端延续性

UI 产品遵循 Code-as-Design 流程：

```
产品方向 → 生命周期规划 → 技术选型 → 页面地图 → 风格方向
    → 静态原型（Design Anchor）→ 用户验收
    → 扩展页面（Design Extension）→ Mock 服务层
    → 组件提取 → API 契约修订 → 后端实现 → 集成
```

### 贡献与许可

欢迎提交 Issue 和 Pull Request。

---

## English

### What Is This?

**Agent Project Continuity** is a **skill** designed for AI agents (such as Claude Code, Codex, Cursor, etc.) that creates a **project memory system** within your repository.

It solves one core problem: when you close a chat window or switch to another AI tool, how does the next agent quickly understand the project state and pick up where the last one left off — instead of starting from scratch?

The answer: **treat repository documents as durable memory, and chat as temporary working context.**

### Core Concept

```
┌──────────────────────────────────────────────┐
│           Repository As Memory                │
│                                              │
│  AGENTS.md ──── Agent operating rules         │
│  docs/       ──── Project state documents     │
│    ├── STATE.md         Snapshot (new agent   │
│    │                    entry — read first)   │
│    ├── product.md      Product intent         │
│    ├── architecture.md Technical architecture │
│    ├── roadmap.md      Lifecycle plan         │
│    ├── tasks.md        Task board             │
│    ├── handoff.md      Handoff detail         │
│    └── ...                                   │
│                                              │
│  New     = Read STATE.md (orient in one file) │
│  agent                                       │
│  Chat    = Temporary context (lost/session)  │
│  Docs    = Durable memory (persists across    │
│            sessions)                         │
└──────────────────────────────────────────────┘
```

### Use Cases

| Mode | Description |
|------|-------------|
| **Initialize** | Create the first continuity document system for a new or existing project |
| **Repair** | Fix missing, stale, contradictory, or unusable continuity docs |
| **Resume** | Recover work from short prompts like "continue development" or "continue yesterday's task" |
| **Status** | Report current phase, task, blockers, acceptance state, and next action |
| **Feature Expansion** | Place new features into an established lifecycle, preventing scope creep |
| **Compact Memory** | Reduce bloated docs while preserving core state |
| **Migration** | Adapt continuity docs to another agent surface or repository structure |

### Document Structure

```text
AGENTS.md                        # Agent operating rules (entry point)
CLAUDE.md                        # Claude Code entry point (optional)
docs/
├── STATE.md                     # Lightweight snapshot (new agent reads first, <40 lines)
├── product.md                   # Product intent, MVP, user flows, boundaries
├── architecture.md              # Tech stack, system shape, run/verify commands
├── page-map.md                  # Pages, routes, navigation, states, dependencies
├── roadmap.md                   # Lifecycle plan, release/phase structure
├── releases/
│   └── RELEASE-001-xxx.md       # Per-release goals, scope, exit criteria
├── ui.md                        # Visual and interaction rules (UI products)
├── api.md                       # API contracts
├── tasks.md                     # Task board and index
├── tasks/
│   └── TASK-001-xxx.md          # Atomic task details
├── dev-log.md                   # Durable development history (with topic index)
├── handoff.md                   # Latest handoff detail (supplement when STATE.md insufficient)
└── decisions/
    └── 001-xxx.md               # Long-lived decision records
```

> Not every project needs all files. The system routes by project type and creates only the minimum required documents.

### Seven Operating Modes

#### 1. Initialize

Bootstrap the continuity system from scratch:

1. Inspect repository shape and existing instruction files
2. Create or update `AGENTS.md` (agent entry point)
3. Determine project type (UI product / Backend API / Library / CLI / Infra / Data-AI / Mixed)
4. Confirm product direction, lifecycle, and technical choices with the user
5. Generate the minimal required document set
6. Record handoff state to support short resume prompts

#### 2. Repair

Fix issues in continuity docs:
- Missing critical files
- Documents inconsistent with code state
- Metadata contradicting prose
- Stale handoff information

#### 3. Resume

When the user says "continue development", "继续开发", "continue yesterday's task", a new agent orients through progressive reading:

1. Read `docs/STATE.md` (snapshot — orient in one file if fresh and consistent)
2. Read the active task file (if `current_task_file` exists)
3. Read `docs/handoff.md` only if STATE.md is missing, stale, or insufficient
4. Read `docs/tasks.md` only if no current task identified
5. Follow `next_action`

Minimal read set: **AGENTS.md + STATE.md + active TASK file**.

#### 4. Status

When the user asks "where are we", "what is next", "progress report":

1. Read `docs/STATE.md` first — if fresh, answer directly (phase, step, current task, blocker, acceptance state, next_action)
2. Read `docs/handoff.md` only if STATE.md is missing, stale, or the question needs more detail
3. Read `docs/roadmap.md`, `docs/tasks.md`, etc. only for release-level progress or task-level detail

#### 5. Feature Expansion

Place new features into an established lifecycle to prevent scope creep:

- **Current release/phase**: only when necessary for exit criteria or to unblock current work
- **Later release/phase**: valuable but not required for the current finish line. Note: detailed task breakdown for a later release starts only after the previous release passes acceptance — early planning wastes effort when user needs shift after seeing working software
- **Backlog**: optional, exploratory, or not yet planned

#### 6. Compact Memory

When documents become bloated:
- Preserve current structured state
- Move history into `dev-log.md` summaries
- Archive inactive task files
- Resolve metadata conflicts

#### 7. Migration

Adapt continuity docs for a different agent tool or repository structure.

### Project Type Routing

Different project types focus on different documents:

| Project Type | Focus Areas |
|-------------|-------------|
| **UI Product** | product, page-map, ui, api, Design Anchor flow |
| **Backend/API** | Service boundaries, API contracts, data models, integration tests |
| **Library/SDK** | Public API, compatibility matrix, semantic versioning, migration notes |
| **CLI Tool** | Command syntax, exit codes, dry-run rules, fixture tests |
| **Infra/Ops** | Environment IDs, permission models, rollback plans, observable verification |
| **Data/AI** | I/O schemas, evaluation criteria, reproducibility, data privacy |
| **Mixed Product** | Split tasks by surface, record cross-surface dependencies |

### Task System

#### Task Board

```markdown
## Current      ← actively being worked on
## Ready        ← ready to start
## Blocked      ← waiting on something
## Backlog      ← to be planned
## Done         ← completed
```

#### Task State Transitions

```
Ready → Current → Done
           ↓
        Blocked → Ready / Current
```

#### Stable `next_action` Values

| Value | Meaning |
|-------|---------|
| `continue_implementation` | Continue implementing |
| `wait_for_user_acceptance` | Waiting for user acceptance |
| `wait_for_owner_approval` | Waiting for owner approval |
| `wait_for_release_acceptance` | Waiting for release-level acceptance |
| `ask_product_question` | Product decision needed |
| `fix_verification_failure` | Fix verification failures |
| `start_next_ready_task` | Start the next ready task |
| `verify_external_state` | Verify external state |
| `blocked` | Blocked |

### Acceptance Levels

Not every change needs the same level of validation:

| Level | When to Use | Requirement |
|-------|------------|-------------|
| **User Acceptance** | Visible UI, interactions, workflows, release behavior | User explicitly confirms acceptance table |
| **Owner Approval** | Long-lived product/API/architecture/security/data-model decisions | Record decision in source docs |
| **Local Verification** | Internal refactors, tests, tooling, behavior-preserving changes | Record command output or verification result |

> ⚠️ Vague replies like "continue", "next", "looks good", "继续", "下一步" do **not** count as acceptance.

### How to Use

#### Option 1: As a Claude Code Skill

1. Clone this repository into your project, or place `SKILL.md` and `references/` in your project root
2. Trigger the skill in Claude Code via `/agent-project-continuity` or natural language
3. Tell it what you want to do, for example:
   - "Initialize the continuity docs for this project"
   - "Repair docs/handoff.md, it's inconsistent with the current code"
   - "Continue development"

#### Option 2: As Instructions for Codex / Other Agents

1. Provide the content of `SKILL.md` as system instructions or context to the agent
2. Make the `references/` directory available as supplementary material
3. The agent will follow the rules defined in the skill

#### Option 3: Manual Use

1. Read `references/project-memory-templates.md` for all document templates
2. Create documents as needed, choosing the right set for your project type via `references/project-types.md`
3. Maintain `docs/STATE.md` (update at every session end) and `docs/handoff.md` as the handoff record at the end of each work session

#### Quick Start Example

```
# 1. Initialize the continuity system in your project
User: Initialize the continuity docs for this project

# 2. The system guides you through:
#    - Confirming product direction
#    - Determining project type
#    - Planning lifecycle and releases
#    - Creating the minimal document set

# 3. Every time you start a new session:
User: Continue development

# 4. The agent reads STATE.md to orient in one file,
#    locates the current task, and follows next_action
```

### External State Protocol

When working with databases, cloud environments, deployments, auth providers, storage, or other state outside the repository:

1. **Before acting**: Record environment, operation, affected resources, expected result, rollback path
2. **During work**: Use machine-readable output, avoid destructive operations
3. **After acting**: Verify and record observable state

### Frontend Continuity

UI products follow the Code-as-Design flow:

```
Product Direction → Lifecycle Plan → Tech Selection → Page Map → Style Direction
    → Static Prototype (Design Anchor) → User Acceptance
    → Extension Pages (Design Extension) → Mock Service Layer
    → Component Extraction → API Contract Revision → Backend → Integration
```

### Contributing

Issues and Pull Requests are welcome.
