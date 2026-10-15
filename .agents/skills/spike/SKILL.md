---
name: spike
description: Safely design, implement, run, analyze, and report a disposable feasibility experiment in a new isolated folder under `spikes/`. Use for prototypes, library or API evaluations, technical unknowns, and competing approaches; do not use for production implementation or changes to a completed spike.
---

Use this skill when the question is best answered by running a bounded experiment rather than changing the production app. Reply and report in Korean unless the user asks otherwise.

## Boundaries

- Work in one new, uniquely named `spikes/<topic>/` folder. Treat existing spike folders as read-only references unless the user explicitly asks to continue one.
- Do not edit production source, production tests or configuration, root dependency manifests and lockfiles, reference assets, or unrelated files. Promotion into production is a separate task requiring explicit user direction.
- Inspect relevant production code and existing spikes before building, but copy only the minimum needed. Prefer adapters, fixtures, or mock data over importing assumptions into production code.
- Keep the spike independently runnable. Put spike-only dependencies and lockfiles inside its folder; never add them to the root project. Follow repository approval rules before installing dependencies.
- Never commit secrets, credentials, model weights, downloaded binaries, private inputs, or large generated outputs. Use environment variables for secrets and provide only a safe `.env.example` when useful.
- Before sending user data to an external service, downloading a large artifact, or using a paid API, state what leaves the machine and obtain any missing approval, cost cap, call cap, or download cap.
- Do not commit, push, publish, or modify external systems unless the user separately authorizes it.

## Experiment contract

Before implementation, write a short experiment contract in `spikes/<topic>/README.md`:

- the decision or unknown the spike will resolve;
- a falsifiable hypothesis;
- observable success and failure criteria;
- fixed inputs or fixtures and relevant baselines;
- scope exclusions, time box, and resource or cost limits;
- exact setup and run commands.

If candidates are being compared, use the same inputs and metrics for each. Prefer the smallest representative input set that can disprove the hypothesis.

## Build and run

1. Record the new folder as the task's write path and confirm it does not already contain user work.
2. Build the smallest harness that can produce evidence. Add instrumentation for timings, sizes, errors, or output quality when those measurements affect the decision.
3. Run the cheapest deterministic check first, then execute the real experiment yourself. For UI or integration spikes, exercise the relevant browser or external-tool scenario when available; do not infer runtime behavior from compilation alone.
4. Inspect the actual outputs. Save only small, useful evidence needed to reproduce or compare the result.
5. Use failures as results: preserve the error, conditions, and a minimal reproduction instead of hiding or broadly working around it.
6. Iterate only when the next change tests a new explanation or materially improves the evidence. Stop when the criteria are answered, the time or cost limit is reached, or two consecutive attempts fail without a new hypothesis.

Do not claim a scenario passed unless it was run successfully. Label unrun checks, estimates, and inferences explicitly.

## Results and feedback

Maintain `spikes/<topic>/RESULTS.md` while working. Keep it concise and decision-oriented:

- environment and exact commands;
- inputs, measurements, and qualitative observations;
- expected versus actual results;
- failed attempts and evidence;
- limitations, privacy or license concerns, and remaining unknowns;
- verdict: adopt, adopt conditionally, reject, or run a named follow-up experiment.

When alternatives were evaluated, include a compact comparison table and explain the tradeoff behind the recommendation. Separate facts observed in the run from interpretation.

Before handoff, rerun the narrowest relevant checks, review `git diff` and `git status` for spillover, and verify that another agent can reproduce the experiment from the README. Report what was implemented, what was actually run, the evidence-backed verdict, and the safest next step. A successful spike answers the decision question; its prototype code does not need production polish.
