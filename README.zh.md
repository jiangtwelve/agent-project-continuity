# Agent Project Continuity

> 轻量级 AI 编程助手项目记忆系统。一个文件恢复上下文。适配 Claude Code、Codex、Cursor 等工具。

[English](./README.md)

---

## 是什么

在不同 AI 编程工具间切换会丢失上下文。Agent Project Continuity 将项目状态维护在仓库的 `.continuity/` 目录下，任何工具来只需读一个快照文件就能接手继续工作，无需重新解释。

```
AGENTS.md                    # 代理操作规则
.continuity/
├── STATE.md                 # 快照：当前阶段、任务、下一步
├── product.md               # 产品范围、MVP 边界
├── architecture.md          # 技术栈、运行和验证命令
├── roadmap.md + releases/   # 版本规划
├── page-map.md + ui.md      # 页面、路由、视觉规则
├── tasks.md + tasks/        # 任务看板和说明书
├── handoff.md               # 会话结束时的交接记录
├── dev-log.md               # 开发日志，按主题索引
├── api.md                   # API 契约
└── decisions/               # 重大决策存档
```

## 工作流

三个阶段，九步递进，每步有明确门禁。

**规划**（一次会话完成）
- Step 1 — 产品文档：需求、MVP 范围、版本边界
- Step 2 — 技术栈：前端、后端、数据库、部署

**开发**
- Step 3 — 页面风格：选定 Design Anchor，打磨到验收通过
- Step 4 — 前端：按样板实现全部页面
- Step 5 — Mock 数据：假数据覆盖六种状态
- Step 6 — 初步验收：假数据下验证页面和交互
- Step 7 — 后端：按 API 契约实现
- Step 8 — 联调：切换真实 API，端到端验证

**测试**
- Step 9 — 全量测试：测试用例、手动验收、上线检查清单

开发中途需求变更走标准协议：暂停、更新产品文档、用户明确确认、重新排期。后续版本的详细任务在前一版本验收通过后才拆分。

## 特性

**单文件恢复。** 读 `STATE.md`（< 40 行）即可定向：当前阶段、任务、下一步、阻塞项。更多上下文按需加载。

**不可变历史。** 已完成任务不可修改，修正通过新任务链接原任务。模糊指令（"继续""看起来不错"）不算验收。

**工具无关。** 适配 Claude Code、Codex、Cursor 等任何能读取 markdown 的工具。

**按需创建。** 后端项目不生成 page-map 和 UI 文档。CLI 工具不生成前端验收表。只创建项目类型实际需要的文件。

**分级写入。** 状态查询不触发更新，小修补只写 dev-log，任务完成才触发完整更新链。

## 使用

**Claude Code。** 将本仓库放入项目目录，通过 `/agent-project-continuity` 触发：

```
"帮我初始化这个项目的延续性文档"
"继续开发"
```

**其他工具。** 将 `SKILL.md` 作为系统指令，保持 `references/` 目录可访问。

**手动。** 使用 `references/project-memory-templates.md` 中的模板。每次收工前更新 `.continuity/STATE.md`。
