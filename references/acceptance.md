# Acceptance And Verification Levels

Use this reference to decide whether a task needs user acceptance, owner approval, or local verification.

## Levels

### User Acceptance

Use `user_acceptance` for changes the user can directly experience or manually validate:

- visible UI, layout, style, copy, page states, and interaction feedback
- navigation, dialogs, forms, filters, tabs, and workflow behavior
- user-facing API behavior or integration behavior
- release/publishing behavior
- externally visible configuration

Present a scoped manual table:

| 序号 | 验收项 | 操作步骤 | 预期结果 | 是否通过 |
| --- | --- | --- | --- | --- |
| 1 |  |  |  | 待用户确认 |

Do not mark the task `done` or start the next task until the user explicitly confirms required acceptance.

### Owner Approval

Use `owner_approval` for durable decisions that may not have a simple manual acceptance action:

- MVP scope or active release finish-line changes
- stable API contracts
- backend technology choices
- major data model changes
- security, privacy, permission, or data retention choices
- public library/CLI interface changes
- release scope changes

Record the decision in the relevant source doc and, when long-lived, in `docs/decisions/`.

### Local Verification

Use `local_verification` for changes that can be verified by the agent without product-owner judgment:

- internal refactors that preserve accepted behavior
- tests, lint, build, CI, or tooling changes
- documentation cleanup that does not change product meaning
- small implementation fixes with no user-visible contract change
- behavior-preserving component extraction after acceptance

Record command output or manual verification result in the active task and `docs/dev-log.md`.

## Vague Replies

Vague replies such as "继续", "下一步", "看起来不错", "looks good", "next", or "continue" do not count as explicit acceptance when `user_acceptance` or `owner_approval` is required.

If the user gives a vague progress prompt while acceptance is pending, present the relevant acceptance or approval checklist and ask for explicit confirmation or requested changes.

## Frontend Acceptance

Frontend prototype acceptance must include both visual quality and scoped interaction behavior. Automated checks, screenshots, and agent review can prepare the prototype for review, but they cannot mark frontend acceptance as passed.

Use levels to avoid blocking on tiny visual churn:

- `Design Anchor`: explicit user acceptance before becoming the UI contract.
- `New page or workflow`: task-scoped user acceptance.
- `Small adjustment within an accepted UI contract`: local verification unless it changes scoped behavior or visible contract.
- `Global UI or workflow change`: explicit user acceptance and relevant doc updates.

## Failed Acceptance

If any acceptance item fails:

1. Keep the task `current`.
2. Record the failed item.
3. Fix the issue.
4. Verify again.
5. Present an updated scoped checklist.

Use `acceptance_status: failed` only while unresolved failed acceptance remains.
