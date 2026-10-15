---
name: feature-flow
description: Plan, implement, validate, and hand off a product feature while minimizing agent usage.
---

Use this skill for a new feature or meaningful behavior change.

1. Read `AGENTS.md` and classify the task as Quick, Standard, or Critical.
2. Inspect the relevant code before proposing changes.
3. For Standard or Critical work, create or update one file under `docs/plans/active/` from `docs/plans/template.md`.
4. Define observable acceptance criteria and scope boundaries.
5. Keep one agent as the sole writer. Do not invoke another coding product or spawn subagents unless explicitly requested.
6. Implement the smallest complete change.
7. Run focused deterministic checks, then broader checks only when justified.
8. Review the final diff against the acceptance criteria.
9. Update the plan with results, remaining risk, and the exact next action if work is incomplete.
10. Report the outcome to the user in the user's language.

