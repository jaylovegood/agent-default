---
name: review-diff
description: Perform a read-only, evidence-backed review of a working-tree or branch diff against stated acceptance criteria.
---

Use this skill when reviewing changes made by another agent or developer.

1. Remain read-only. Do not modify files.
2. Read `AGENTS.md`, the applicable active plan, and the diff.
3. Check correctness, regressions, security boundaries, data loss, error handling, concurrency, compatibility, and missing tests.
4. Ignore style preferences unless they create a concrete defect or violate an explicit project rule.
5. Verify each finding against the surrounding code before reporting it.
6. Report findings by severity with file locations, impact, evidence, and a concise remediation direction.
7. If no material findings exist, say so and list any validation gaps or residual uncertainty.

