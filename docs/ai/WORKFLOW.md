# Shared Agent Workflow

Use this workflow with either Codex or Claude Code. The current tool is the worker; the other tool is merely an optional future reviewer. No phase belongs permanently to either product.

## 1. Select a work mode

### Quick

Use for a clear, low-risk change with a small surface area.

1. Inspect the relevant files.
2. Implement the smallest complete change.
3. Run focused validation.
4. Review the diff and report the result.

Do not create a plan, spawn a subagent, or request a second-model review unless new risk appears.

### Standard

Use for a normal feature, a multi-file bug fix, or a change likely to be resumed in another tool.

1. Create or update one plan under `docs/plans/active/`.
2. Confirm scope, constraints, and acceptance criteria.
3. Let one agent own all edits.
4. Run deterministic validation.
5. Optionally hand the plan and diff to another agent for a read-only review.
6. Let the writer evaluate findings, apply valid fixes, and rerun validation.

### Critical

Use for security boundaries, authentication, authorization, payments, destructive data changes, production migrations, or similarly high-risk work.

1. Write a detailed plan with rollback and validation steps.
2. Obtain a read-only plan review from a separate agent when practical.
3. Assign one writer.
4. Validate with tests and relevant static or security checks.
5. Obtain a read-only final diff review.
6. Resolve findings and record remaining risk.

## 2. Switch tools without repeating work

When moving from Codex to Claude Code or the reverse:

1. Keep the current working tree; do not copy chat transcripts.
2. Update the active plan with completed work, evidence, decisions, failures, and the exact next action.
3. Start the next tool in the same repository.
4. Ask it to read `AGENTS.md`, the active plan, and the current diff before doing new investigation.

Suggested continuation prompt:

```text
Continue the current task using the repository state and the active plan.
Read AGENTS.md, docs/plans/active/<plan>.md, and the current diff first.
Do not redo completed investigation unless you find conflicting evidence.
Reply to me in Korean.
```

## 3. Use a separate reviewer economically

The reviewer should receive the acceptance criteria, active plan, and diff rather than the writer's full conversation.

Suggested review prompt:

```text
Perform a read-only review of the current diff against the active plan.
Do not edit files. Report only evidence-backed correctness bugs, regressions,
security issues, and missing tests. Include file locations and omit style-only advice.
Reply to me in Korean.
```

## 4. Delegation threshold

Use a subagent only when all of these are true:

- the work is independently scoped;
- it benefits from isolated context or parallel execution;
- its expected result can be summarized concisely;
- the coordination cost is lower than doing it directly.

Good examples: trace one subsystem, test a competing bug hypothesis, or review a diff. Poor examples: edit one file, run one search, format code, or implement sequential dependent steps.

