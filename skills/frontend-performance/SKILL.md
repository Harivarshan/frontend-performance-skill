---
name: frontend-performance
description: Diagnose and improve frontend build, CI, and release times using job logs, cache evidence, and controlled benchmarks. Use for slow bundling, TypeScript checks, Docker builds, Playwright or other browser tests in CI, cache restore or export, and deployment pipelines. Focus on engineering feedback time through CI and releases.
license: MIT
---

# Frontend Performance

Find what controls the wait, then test a change that removes enough work to matter. Start with the user's chosen outcome, such as time to a merge decision, time to a verified deployment, runner usage, or variability.

1. **Map the real path.** Read the workflow at the measured commit, including reusable workflows, scripts, job dependencies, conditions and required checks. Inspect recent normal runs. Use job start/end times to find the work that finished last and delayed the result. Track queueing and the sum of runner minutes separately. For parallel shards, the last shard and its reporting job usually determine completion.
2. **Measure inside the slow job.** Use step timestamps, tool logs and existing reports before adding instrumentation. Separate setup and downloads from execution. Identify the resolved cache key, bytes moved, and proof the underlying tool reused work. Keep failures and reruns visible alongside successful timing samples. Say when a phase is inferred from log markers or lacks evidence.
3. **Pick one hypothesis.** Name the work it removes, where the saving should appear, what might offset it, and the smallest discriminating test. For slow browser lanes, image builds, caching, type checking or release stages, use [the diagnosis guide](references/pipeline-diagnosis.md).
4. **Compare fairly.** Use [the benchmark guide](references/benchmarking.md) when testing a change. Freeze workload and validation, pair equivalent runs where practical, and consider cache state and runner differences. Check that the tool executed and that both arms produced equivalent results. Collect enough repeat observations to distinguish the expected saving from ordinary variation.
5. **Report and validate at the level the user cares about.** Show before and after durations, units, sample size, spread, failures or retries, and links to evidence. Label a controlled result, an observation from ordinary CI, and an estimate as such. Explain whether the improvement shortens the critical path or saves work off it. If the user requests a rollout, verify the actual restore, build, checks and outcome in normal CI and the applicable release environment.

Keep type, test, coverage, image smoke, and security checks equivalent when optimizing. If a distinct check would replace one that runs inside a build, prove equivalent inputs and failure behavior first, including generated route types where applicable. Treat local runs as useful diagnostics and validate CI-specific claims on CI. Use the repo's existing access, policy and rollout boundaries when taking action.
