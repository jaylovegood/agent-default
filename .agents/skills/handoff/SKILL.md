---
name: handoff
description: Prepare concise durable state so another Codex or Claude Code session can continue without repeating work.
---

Use this skill when the user plans to switch tools, usage limits are near, or work must pause.

1. Identify the single active task and its plan. Create a plan only if the remaining work is non-trivial.
2. Inspect the working tree and distinguish pre-existing changes from changes made for the task.
3. Update the plan with verified completed work, current behavior, decisions, validation results, failed attempts with evidence, and the exact next action.
4. Keep the handoff concise. Do not copy conversation history or raw logs.
5. Do not revert, commit, or discard changes unless the user explicitly asks.
6. Give the user a short continuation prompt naming the plan and the next action.

