# Lifecycle And Feature Expansion

Use this reference when initializing roadmap/release docs or placing a new feature into an established project.

## Lifecycle Rules

Every long-running project needs an explicit lifecycle plan before implementation task planning is complete. The scheme is project-specific:

- versions such as `v0.1`, `v0.2`, `v1.0`
- phases such as Phase 1, Phase 2, Phase 3
- milestones such as prototype, beta, launch, operations
- a custom scheme chosen with the user

Create `docs/roadmap.md` for the overview. Create files under `docs/releases/` for active and near-term releases/phases.

Each release/phase should define:

- name, status, target audience, and purpose
- included scope
- explicitly excluded scope
- required quality bar and verification environment
- exit criteria
- release-level user acceptance or owner approval steps
- linked tasks that are required, optional, or deferred

## Planning Conversation

During setup, ask one lifecycle decision question at a time. Confirm at least the first meaningful release/phase and its finish line before creating the first full implementation backlog.

Do not impose a fixed version scheme. Recommend a default only after seeing project type, MVP risk, and user goal.

## Deferred Planning For Later Releases

Plan later releases at finish-line granularity only: purpose, included scope, excluded scope, exit criteria. Do not create detailed task files for release N+1 while release N is in development.

Start detailed task breakdown for release N+1 only after release N passes release-level acceptance. If the user requests early detail for a later release, record it in that release file's scope notes instead of creating task files.

## Feature Expansion

When the repository already has continuity docs and the user adds a feature:

1. Read `AGENTS.md`, `docs/handoff.md`, `docs/tasks.md`, `docs/roadmap.md`, the active release/phase file, and docs relevant to the feature surface.
2. Ask only feature-scoped questions needed to classify and implement the feature.
3. Assess whether it affects current work, later page design, UI contract, API contract, backend behavior, data model, navigation, accepted user flows, external state, or release exit criteria.
4. Place the feature by dependency and product priority.
5. Update only affected documents.
6. Create atomic task files before referencing new task IDs.
7. Update `docs/handoff.md` with the appropriate next action.

## Placement Rules

- Current release/phase: only when necessary for agreed exit criteria or to unblock current work.
- Later release/phase: valuable, planned, but not required for the current finish line.
- Backlog: optional, exploratory, low-priority, or not yet planned.
- Blocking dependency: insert before or alongside affected tasks and mark blocked tasks with `blocked_by`.

Always tell the user which release/phase the feature is assigned to and why. If the feature changes the active release/phase goal or exit criteria, ask for confirmation before implementation.

## Finish-Line Guard

At the end of each meaningful task, compare completed work against the active release/phase exit criteria. If the target is met, proactively report that the release/phase is ready for acceptance or approval and avoid adding optional feature work until the user chooses the next direction.
