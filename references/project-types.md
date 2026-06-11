# Project Type Routing

Use this reference during initialization, repair, or feature expansion to avoid forcing every project through a frontend-first flow.

## Selection

Identify the dominant project type before creating docs or tasks:

- `ui_product`: web app, mobile app, mini program, desktop app, or other user-facing product.
- `backend_api`: API service, backend worker, integration service, or server-side platform.
- `library`: package, SDK, framework extension, or reusable module.
- `cli_tool`: command-line tool, developer utility, automation script, or local workflow tool.
- `infra_ops`: deployment, cloud configuration, IaC, monitoring, auth, storage, queues, or operational automation.
- `data_ai`: data pipeline, analytics, ML/AI workflow, evaluation harness, or model/tool integration.
- `mixed_product`: multiple major surfaces; choose the primary current release surface and record secondary tracks.

Record the selected type in `.continuity/architecture.md` and, when it changes planning, in `.continuity/roadmap.md`.

## Continuity Focus By Type

### UI Product

Use `.continuity/product.md`, `.continuity/page-map.md`, `.continuity/ui.md`, `.continuity/api.md`, `.continuity/roadmap.md`, `.continuity/tasks.md`, task files, `.continuity/dev-log.md`, and `.continuity/handoff.md`.

Use `references/frontend-continuity.md` for Design Anchor and Design Extension work.

### Backend/API

Prioritize:

- service boundaries and runtime commands in `.continuity/architecture.md`
- API contracts in `.continuity/api.md` or `.continuity/api/`
- data model, auth, and integration assumptions
- contract tests, integration tests, and local verification commands
- external state protocol when databases, queues, cloud services, or deployments are involved

Do not require `.continuity/page-map.md` or `.continuity/ui.md` unless a UI surface exists.

### Library/SDK

Prioritize:

- public API surface and supported platforms
- examples and compatibility matrix
- semantic versioning or release policy
- test matrix and backwards compatibility rules
- migration notes for breaking changes

Use owner approval for public API changes that affect downstream users.

### CLI Tool

Prioritize:

- command syntax, flags, config files, and environment variables
- expected stdout/stderr behavior and exit codes
- dry-run, destructive-action, and error-handling rules
- fixture-based tests and manual command verification

User acceptance is usually needed only for user-facing workflow changes or release behavior.

### Infra/Ops

Prioritize:

- environment names and IDs
- affected resources
- permission model
- rollback/recovery plan
- observable verification result
- external state handoff

Use `references/external-state.md` before acting.

### Data/AI

Prioritize:

- input/output schemas
- dataset or fixture provenance
- evaluation criteria
- reproducibility and determinism
- model/tool versions
- privacy and data retention assumptions

Owner approval is needed for evaluation criteria, user-impacting model behavior, or data policy changes.

## Mixed Projects

For mixed projects, avoid one giant universal task. Split tasks by surface when the work crosses UI, backend, data, infra, or release operations. Record dependencies between task files with `depends_on`, `blocked_by`, and `blocks`.
