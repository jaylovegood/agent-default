---
name: debug-flow
description: Diagnose and fix a bug through reproduction, competing hypotheses, focused evidence, and regression validation.
---

Use this skill for a bug whose cause is not already established.

1. Restate the observed behavior and the expected behavior.
2. Reproduce the problem or identify the strongest available evidence.
3. Trace the narrowest relevant execution path.
4. List at most three plausible hypotheses, ranked by evidence.
5. Test the cheapest discriminating hypothesis first.
6. Do not change code until one hypothesis has adequate evidence, unless a reversible probe is the fastest test.
7. Make the smallest root-cause fix and add or update a regression test when practical.
8. Run the focused regression check and inspect the diff.
9. If unresolved after two materially different attempts, update an active plan with findings, failed attempts, and the next experiment instead of repeating work.

