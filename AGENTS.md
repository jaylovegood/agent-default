# Project Agent Guide

This repository is designed to be used interchangeably with Codex and Claude Code. Treat both tools as equivalent workers. Do not assume that either tool owns a particular phase or is always available.

## Communication

- The user will usually write in Korean. Understand Korean requests directly and reply in Korean unless the user asks for another language.
- Keep code, identifiers, commit messages, and repository documentation in the language already established by the project.
- Keep agent-facing instructions in concise English to reduce ambiguity and context overhead.

## Source of truth

- This file contains the shared, always-on instructions for every coding agent.
- `CLAUDE.md` imports this file and must not duplicate shared rules.
- For a complex or resumable task, use one plan under `docs/plans/active/` as the durable handoff record.
- Read only the documents relevant to the current task. Do not preload the entire `docs/` tree.
- If another agent already started the task, continue from the existing plan and working tree. Do not redo completed investigation without a concrete reason.

## Work modes

Choose the least expensive mode that safely fits the task. See `docs/ai/WORKFLOW.md` when a task needs planning, cross-agent review, or handoff.

- **Quick:** Default for bounded, low-risk work. One agent investigates, edits, and verifies. No subagents and no cross-tool review.
- **Standard:** Use for normal multi-file features or meaningful behavior changes. One agent owns all edits; a later agent may review the plan or diff.
- **Critical:** Use only for high-risk work such as authentication, authorization, payments, destructive data changes, security boundaries, or major migrations. Require plan review and final diff review by a separate agent when practical.

The user may switch between Codex and Claude Code at any time. Never call or automate the other product's CLI. Leave a concise plan or handoff artifact instead.

## Budget and delegation

- Work directly by default. Do not spawn subagents unless the user explicitly asks for delegation or the active plan explicitly requires it.
- Never use subagents for a simple lookup, a single-file edit, formatting, or a task that must be performed sequentially.
- When delegation is requested, use at most two concurrent subagents.
- Prefer subagents for independent, read-heavy work such as exploration, hypothesis testing, or review.
- Keep subagents read-only unless their file ownership is explicit and non-overlapping.
- Do not let subagents spawn more subagents.
- Do not ask multiple agents to repeat the same investigation unless independent verification is the stated goal.
- Prefer deterministic tools such as tests, linters, type checkers, and static analysis over additional model review.

## Concurrent sessions

- Unrelated tasks may run in parallel in different agent sessions. When the user says another session runs in parallel, create a dedicated `git worktree` and branch for this task (sibling directory named after the task), work only there, and report the path. Ask before installing dependencies in it, and do not merge or remove it without user confirmation. A session that was not told about parallel work keeps using the main working tree.
- In a shared working tree, each task lists its write paths in its plan. Do not edit another task's files and do not revert changes you did not make.
- Do not run `git stash`, `checkout`, `reset`, or `commit`, or install dependencies, in a shared tree without user confirmation. Only one task at a time may modify `package.json` or the lockfile.
- If a repo-wide check fails in files outside your write paths, report it as unrelated instead of fixing it.

## Planning and handoff

- Small changes do not need a plan file.
- Create a plan from `docs/plans/template.md` when work is ambiguous, spans several components, is expected to survive a tool switch, or carries meaningful risk.
- Keep the plan current while working. Record verified facts, decisions, completed steps, remaining work, and validation results.
- For non-trivial work, update the active plan after each verified milestone so an unexpected tool switch does not lose important reasoning.
- A plan is a coordination artifact, not a transcript. Do not paste raw chat, long logs, or speculative notes into it.
- Deferred work goes in `docs/backlog.md`, not in an active plan. Read it only when the user asks or the task touches a listed topic. When work starts, remove the entry and create a plan.
- Before handing work to another agent, leave the working tree in a comprehensible state and update the plan with the exact next action.
- Do not create commits unless the user requests them or the project workflow explicitly requires them.

## Implementation

- Inspect relevant code before editing and follow existing patterns.
- Make the smallest defensible change that satisfies the request.
- Keep unrelated files and user changes untouched.
- Do not add dependencies, change public interfaces, or perform destructive migrations without calling out the need first.
- One agent owns the edits for a task at a time. Review agents report findings and do not modify code unless explicitly reassigned as the writer.

## Verification

- Define completion in observable terms before implementing a non-trivial change.
- Run the narrowest relevant checks first, then broader checks when justified.
- Do not claim a command passed unless it was actually run successfully.
- Review the final diff for regressions, accidental edits, secrets, and missing tests.
- Report what changed, what was verified, and any remaining uncertainty.

## Project-specific section

Fill this section in once per project, keeping it short. If a bullet does not apply yet, leave it out rather than guessing.

- Stack and layout: language, framework, entry points, and where source, tests, and reference assets live.
- Commands: how to run, type-check, lint, test, and build, plus the supported runtime version.
- Architecture rules: invariants and shared definitions that every change must preserve.
- Generated output (dependency folders, build output, coverage) is read-only unless a task explicitly changes it. Treat reference assets and completed experiments the same way.
- Completion criteria: which checks and manual scenarios must be recorded before a change counts as done.

