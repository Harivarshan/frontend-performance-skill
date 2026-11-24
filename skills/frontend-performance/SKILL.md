---
name: frontend-performance
description: Research and diagnose slow frontend builds, CI checks, and releases. Use for build and deployment timelines, dependency and compiler caches, TypeScript work, Docker and BuildKit, browser startup and retries, or controlled comparisons of pipeline changes.
license: MIT
---

# Frontend Performance

Turn a report of slow CI or releases into a sourced explanation, a ranked set of options and, when requested, a verified improvement. This skill covers engineering feedback time from local builds through release verification.

1. **Define the wait.** Identify the trigger and finish line. Developers may care about a revision's first passing required checks, a release's final verification, runner time, or slow outliers. These can move in different directions.
2. **Build an evidence trail.** Read workflows and scripts at the revision that actually ran. Collect a small cohort of normal runs plus a representative slow or failed attempt. Record actual step times, job dependencies, runner details and validation outcomes. Use [Pipeline diagnosis](references/pipeline-diagnosis.md) for the inventory and timing method.
3. **Trace the work inside the slow path.** Follow the input through the step, tool and output. A CI cache restore can leave the compiler doing nearly all its work. An HTTP readiness probe can trigger compilation before Playwright starts. Separate confirmed behavior from plausible explanations. Use [Builds, caches and types](references/build-caches.md) or [Browser tests and releases](references/browser-and-release.md).
4. **Research options against the installed version.** Read the lockfile and runner logs first. Prefer the project's source, official versioned docs and release notes, upstream source at the pinned version, and reproducible experiments. Start with [Current tooling](references/current-tooling.md), then verify any version- or service-dependent behavior before applying it. Distinguish a documented capability from evidence that it helps this repository.
5. **Design the smallest comparison that can change the decision.** State which work the intervention should remove and where the saving would appear. Select a metric and a meaningful threshold before running the comparison. Use [Benchmarking](references/benchmarking.md) for cache-state controls, paired runs, correctness checks and rollout validation. [Worked cases](references/worked-cases.md) show how to reason through ambiguous results.
6. **Hand back the decision.** Give the critical path, measured bottleneck, alternatives and trade-offs, evidence links, and the result's limits. Label controlled measurements, historical observations and estimates. If implementation or rollout is in scope, verify normal CI or the applicable release run on the actual revision.

Keep the same effective validation when optimizing. A standalone check can replace an in-build check only when it covers equivalent inputs and failure behavior, including generated files where applicable. Compare equivalent artifacts and verify required tests, coverage, smoke and scans as the project defines them. Keep diagnostic work read-only when the user asks for research. Follow the project's authorization and rollout boundaries when running comparisons or changing workflows.
