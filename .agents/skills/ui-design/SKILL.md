---
name: ui-design
description: Shape and apply the app's visual design system through a collaborative direction phase, then a token-first rollout. Use for UI redesign, visual polish, or design consistency work, not for feature logic.
---

Use this skill for UI/visual work: redesign, consistency cleanup, new screens that must match the app's look. The design direction is decided together with the user and recorded in `docs/ai/DESIGN.md`. That file is the single source of truth for visual decisions.

## Scope

- Allowed: stylesheets, component markup and class names, UI layout, design tokens, accessibility of controls.
- Off limits unless the user explicitly extends scope: behavior and state logic, data handling, core rendering, and export or output logic. If `AGENTS.md` names files or modules that must not change during restyling, treat them as off limits too.
- Do not add dependencies (UI kits, icon packs, fonts) without asking first.
- Follow the work-mode rules in `AGENTS.md`. A redesign across several components is Standard: keep one plan under `docs/plans/active/`.

## Phase 0: Detect state

1. Read `AGENTS.md`, then check whether `docs/ai/DESIGN.md` exists.
2. Missing or marked `status: draft` → run Phase 1. Marked `status: agreed` → skip to Phase 3 and treat it as binding.

## Phase 1: Audit (read-only)

1. Run the app (use the dev command from `AGENTS.md`) and capture screenshots of every page and key state (empty, filled, error, hover/focus, narrow width). Use whatever browser tooling the current agent has; if none, ask the user for screenshots.
2. Read the project's stylesheets and UI component files. Inventory: colors, font sizes, spacing values, radii, shadows, and repeated or one-off patterns (buttons, panels, inputs, toolbars).
3. Report to the user, concisely: what the app currently feels like, the main inconsistencies, and which screens matter most (primary working surfaces vs. entry or overview pages).

## Phase 2: Shape the direction with the user

The user has no fixed direction yet. Do not invent one silently.

1. Ask about the product's character with a few concrete questions (use the question tool if available): primary users and context, desired tone (dense tool vs. friendly vs. editorial), light/dark preference, any reference apps or screenshots they like or dislike. Ask at most 3-4 questions per round.
2. Propose 2-3 distinct directions. Each: one-paragraph description, a small token sketch (palette, type, radius, density), and which existing screen it would change most. Prefer a quick visual mock (a static HTML file in the scratchpad, or a trial on one real screen) over prose when possible.
3. Iterate on feedback. When the user picks a direction, write `docs/ai/DESIGN.md` with `status: agreed` containing:
   - Character and principles (a few lines, including what to avoid)
   - Tokens: color roles (surface, text, accent, border, danger, focus), type scale, spacing scale, radii, shadows, as CSS custom properties
   - Component rules: buttons (variants and states), panels, form fields, toolbars, tabs, modals, empty states
   - Layout rules: page widths, editor/side-panel proportions, responsive behavior
   - Accessibility floor: contrast, visible focus, hit-target size, keyboard operability
   - Decisions log: dated one-line entries for later changes
4. Keep DESIGN.md short enough to read in one pass. Update it when the user changes a decision; do not let code and document drift.

## Phase 3: Roll out, token-first

1. Introduce tokens (for example CSS variables) in the project's central stylesheet or theme file; replace hardcoded values with tokens.
2. Consolidate repeated patterns into shared classes/components before touching individual screens.
3. Apply screen by screen, most-used first. After each screen, take before/after screenshots and show the user. Wait for approval before the next screen if the change is visible and large.
4. Do not "improve" behavior while restyling. If a UX problem needs a logic change, note it in the plan and ask.
5. Keep controls that sit over or beside custom-drawn content (media, canvases, previews) legible against that background, and never alter how the content itself is rendered.

## Phase 4: Verify and hand off

1. Run the type-check, lint, test, and build commands listed in `AGENTS.md`. Report real results only.
2. Check keyboard focus, contrast, and narrow-width layout on every touched screen.
3. Review the diff for stray hardcoded colors, duplicated styles, and accidental logic edits.
4. Update the plan and DESIGN.md decisions log; list remaining screens and the exact next action if unfinished.
5. Report to the user in the user's language: what changed, what was verified, and open design questions.
