# Frontend Continuity

Use this reference for UI products or any task that changes frontend experience.

## Code-As-Design Flow

For product UI work, follow this order unless the project type or user direction says otherwise:

1. Product direction.
2. Lifecycle/release plan and first finish line.
3. Technical selection.
4. Confirmed page map.
5. Confirmed page style direction.
6. Static frontend prototype implemented in the real frontend stack.
7. Preview fixtures for static page data, separate from markup/templates.
8. Visual and interaction iteration in code.
9. Mock service layer covering meaningful states.
10. Component extraction only after repeated patterns are clear and acceptance allows it.
11. API contract revision based on what validated frontend actually uses.
12. Backend implementation.
13. Frontend service replacement and integration verification.

For WeChat Mini Program projects, account for small screens, scroll behavior, safe areas, native component constraints, and WeChat Developer Tools verification when available.

## Page Map

Before building the Design Anchor or any static frontend prototype, create or update `docs/page-map.md` and ask the user to confirm it. Include pages, route/path, purpose, required features, primary interactions, states, data dependencies, navigation relationships, and Design Anchor choice.

Confirm states that affect page structure, navigation, user trust, data entry, permissions, onboarding, or core workflow outcomes. Low-risk display-only states can use sensible defaults recorded in `docs/page-map.md` or `docs/ui.md`.

## Style Direction

Before implementing the Design Anchor or first static frontend page, ask the user to confirm page style direction. Cover tone, density, visual restraint or expressiveness, color preferences, component feel, and explicit dislikes.

When available, prefer the `frontend-design` skill for page aesthetics, layout quality, component polish, and avoiding generic frontend output. If unavailable, continue with normal frontend development and record the fallback when it affects design quality or verification.

## Design Anchor And Extension

- `Design Anchor`: first core page or flow that establishes visual and interaction direction.
- `Design Extension`: later pages or flows that extend the accepted visual direction.

After the user accepts a Design Anchor, update `docs/ui.md` with colors, typography, spacing, component patterns, density, form patterns, interaction behavior, copy tone, anchor page path, and explicit "do not" rules where useful.

For every Design Extension:

1. Read `docs/ui.md`.
2. Inspect the accepted anchor page source.
3. Inspect existing shared components.
4. Reuse existing base components before creating new ones.
5. Extend local UI patterns only when needed.
6. Update `docs/ui.md` when a reusable pattern is added.

Do not introduce a new global visual direction, palette, typography system, card/list/button/form style, or motion language in a Design Extension without user confirmation.

## Mock And Service Rules

Static prototypes may use lightweight preview fixtures before a full mock service exists. Preview fixtures demonstrate visual design and page states. Mock services simulate future backend behavior and user operations.

Do not place business data directly inside WXML, JSX, HTML, or template markup except trivial labels or static copy. Keep representative data in fixtures, constants, or service adapters.

Pages should depend on service functions or adapters, not raw mock files. Backend integration should primarily replace or reconfigure adapters rather than rewrite page behavior.

Mock data should be deterministic unless the task explicitly needs randomized data. Cover representative normal, empty, loading, error, edge, permission, and multi-state scenarios when relevant.

When the user asks to see or compare a UI state, use the project's preview surface when feasible: local preview, browser screenshot, simulator, platform preview, or exact local URL.

## Component Extraction

Do not extract a general component library before frontend direction is accepted.

Extract after acceptance when:

- the same UI pattern appears in two or more places
- a base control is needed for continuity
- backend integration or future pages would otherwise duplicate visual behavior

Component extraction must preserve accepted appearance unless the user approves a visual change. If extraction is outside task scope, create a follow-up task.
