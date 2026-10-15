---
name: overnight
description: Run long unattended work while the user is away. Works a bounded queue, explores and experiments in the least invasive workspace (main tree for proposal-only work, a branch for code changes), checkpoints often, and leaves a morning report. Use for design-direction exploration, spike experiments, or any multi-hour task the user explicitly hands off.
---

Invoking this skill is the user's explicit authorization for the grants below. It does not extend to anything else. Reply and report in Korean.

The goal is not to consume tokens. Keep working while meaningful work remains inside the queue and budget; stop safely when it runs out.

## Authorization

Granted by invoking this skill:

- Choose the workspace by task type (see Workspace mode): main tree for proposal-only work, a branch `overnight/<date>/<topic>` for code changes, a worktree (sibling directory `<repo>-overnight-YYYY-MM-DD`) only as the fallback. Report where the work lives.
- On a branch or worktree, make checkpoint commits there only, staging only files you created or changed by explicit path.
- In a worktree, install dependencies only if the kickoff confirmed it; otherwise symlink the main tree's dependency folder (for example `node_modules`).

Never, without a new explicit instruction: commit to the default branch, overwrite or revert user changes, push, delete files outside paths you created, add dependencies to the main tree, modify reference assets, completed spikes, or user changes, publish or send anything externally, install global tools. Subagents stay off unless the kickoff enables them (max 2, read-only).

## Workspace mode

Decide at kickoff and record it in the plan.

- **Proposal-only** (static mocks, docs, reports; no change to source, tests, config, or any existing tracked file): work in the main tree. Create new files only under `docs/ai/design-proposals/<topic>/`, `docs/experiments/`, or `docs/plans/active/`. Do not commit; leave the files untracked and list them in the report.
- **Code changes** (anything touching source, tests, config, or existing tracked files): if `git status` is clean, create branch `overnight/<date>/<topic>` in the main tree and work there, committing only files you changed (explicit paths). If the tree has uncommitted user changes, use a worktree instead (it keeps them out of the branch). Never `git stash`.
- If unsure which applies, use the worktree.
- At the end, leave a branch in the main tree checked out only if the user is told how to return (`git checkout <original-branch>`); prefer switching back to the original branch when the work is committed.

## Kickoff (once, while the user is still present)

Ask in one round, then do not ask again:

1. Task queue in priority order, and the profile (design / experiment / other).
2. Stop limits: wall-clock hours and, for experiments, caps on paid API calls, cost, and downloads (GB).
3. Allowed and forbidden areas beyond the defaults above.
4. Tooling check: can this session take screenshots (browser tool or an already-installed headless browser)? Are permission prompts pre-approved (allowlist or auto-accept)? If prompts would block unattended, say so now; the run stops at the first prompt.
5. Done criteria for each item.

Then set up: the workspace per Workspace mode and `docs/plans/active/overnight-<date>.md` from `docs/plans/template.md`, with the queue, limits, and an empty log. Run the baseline checks listed in `AGENTS.md` (type-check, lint, test, build) and record results so later failures can be attributed.

## Tiers

- **A. Assigned work:** do it, verify, commit.
- **B. Exploration:** experiments, alternatives, proposals, extra tests, research. Allowed only on `overnight/*` branches or under `docs/experiments/`. Before starting write the hypothesis, the measurable success criterion, and a time box. Record failures as carefully as successes.
- **C. Out of scope:** record under "wanted to do, did not" in the report. Do not do it.

## Loop

1. Pick the next queue item. If the queue is empty, consider Tier B ideas only if they are tied to the user's stated goal and have a clear criterion.
2. Work. Re-read the plan file after any context compaction before continuing.
3. Verify with the narrowest relevant checks, then the full check set from `AGENTS.md`. Real results only.
4. Green: checkpoint commit (small, descriptive). Red and not fixable in two attempts: revert to the last green commit, log the failure with evidence, move on.
5. Append to the plan log: what was done, evidence, decision, next action. Keep it terse; no raw logs.
6. Questions you would normally ask: pick the safest default or skip the item, and add it to "decisions needed" in the report. Never block waiting for an answer.

Do not end the turn before a stop condition.

## Stop conditions

Stop when any holds: queue done and no Tier B idea with a clear criterion remains; the same problem failed twice in a row with no new idea; a limit from kickoff is reached; the tooling needed (screenshots, permissions) is unavailable and cannot be worked around. On stop, leave the branch/worktree on a green commit (proposal-only: files in place) and write the report.

## Profile: design exploration

Use with `ui-design`, with these overrides because no user is present:

- Run Phase 1 (audit) fully: screenshots of every page and key state, token inventory, inconsistencies.
- Replace Phase 2 negotiation with divergence: produce 3-4 clearly different directions, each with a token sketch (palette, type, radius, density, tone). Write `docs/ai/DESIGN.md` as `status: draft` only; never `agreed`.
- Replace Phase 3 with trial application: one branch per direction, applied to the 2-3 most important screens (the primary working surface and the entry or overview pages) using design tokens in the project's stylesheet. No behavior changes, no changes to core rendering or output logic, no new dependencies.
- Self-critique each round against a fixed rubric: contrast, visible focus, hit-target size, narrow width, legibility of controls over busy or custom-drawn areas, consistency across screens. Only defects you actually saw in a screenshot count; "looks good" is not evidence. Fix the worst defects, rescreenshot, log what changed and why. 2-3 rounds per direction, stop earlier if gains are small.
- After the directions converge, optionally try one hybrid combining the best parts.
- Do not widen to more screens or more directions just because budget remains.

## Profile: experiment (spikes)

- Work in a new folder under `spikes/` in the chosen workspace (spikes are code: use a branch or worktree); do not edit completed spikes.
- Per candidate (model, API, library): hypothesis, metric, fixed test inputs, time box, then run and record numbers plus qualitative notes. Use the same inputs for every candidate.
- Respect cost, call, and download caps. Stop a candidate that exceeds its time box.
- Do not commit model weights, API keys, or large outputs. Note license uncertainty in the report.
- End with a comparison table and a recommendation.

## Morning report

Write `docs/plans/active/overnight-<date>-report.md` (Korean, short enough to read in five minutes) and also give a brief summary in chat:

- 3-line summary
- Completed items with verification results; workspace mode used, worktree path or branch, commit list (or untracked file list)
- Experiments or design directions: hypothesis/character, evidence (screenshots or numbers, with file paths), self-critique, adopt-or-not recommendation
- Ranked proposals with value vs. cost
- Decisions needed from the user
- Wanted to do but did not (Tier C) and open problems, including any failed attempts
- How to adopt (merge a branch, flip `DESIGN.md` to `agreed`, continue with `ui-design` Phase 3) and how to discard (delete the new files, or remove the branch/worktree)
