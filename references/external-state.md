# External State Protocol

Use this reference before changing databases, cloud environments, deployments, push notification configuration, storage buckets, auth providers, queues, third-party integrations, or other state outside the repository.

## Before Acting

Record:

- environment name or ID
- account/project/tenant if relevant
- intended operation
- affected resources
- expected observable result
- rollback or recovery path when the operation is risky
- required credentials, permissions, or approvals

If the environment cannot be identified, use `next_action: verify_external_state` or `blocked` rather than guessing.

## During Work

Prefer commands and APIs that return machine-readable output when available. Avoid destructive operations unless the user explicitly requested them or approved them.

For deployments, record build artifact, version, function/service name, route, region, and release target when available.

For databases or storage, record counts, schema/collection/table names, affected IDs, and any migration assumptions.

For auth providers, notification providers, and third-party integrations, record configuration keys by name, not secret values.

## After Acting

Verify and record observable state:

- deployed version or timestamp
- function/service list or status
- database/storage counts or sample query result
- config value presence
- integration test result
- remaining gap or manual verification needed

Keep only latest actionable external state in `docs/handoff.md`. Put operation history in `docs/dev-log.md`.

Use `next_action: verify_external_state` when the next agent must inspect external state before coding.

## Documentation Targets

- `docs/architecture.md`: durable environment shape and commands.
- `docs/decisions/`: long-lived provider, region, deployment, auth, data retention, or migration decisions.
- `docs/dev-log.md`: operation history and verification detail.
- `docs/handoff.md`: latest state, cautions, and next action.
- Active task: scoped implementation and verification status.
