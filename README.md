# Frontend Performance

An [Agent Skill](https://agentskills.io/specification) and field guide for finding out why frontend builds, CI, and deployments take as long as they do. It teaches an agent to trace the workflow that ran, research what the installed tools actually support, and design a comparison that tells you whether a change is worth keeping.

A job can finish a minute sooner while developers wait just as long. The tests might run in parallel with that job, and the slowest browser shard can still hold the final check. Likewise, a downloaded compiler cache does not tell you how much work the compiler reused. The guide explains how to trace both cases to the evidence.

## What you can investigate

| If you see… | Start here |
| --- | --- |
| “CI takes 20 minutes,” with several jobs and reruns | [Pipeline diagnosis](skills/frontend-performance/references/pipeline-diagnosis.md) defines the wait, reads an exact workflow attempt, and traces the required result. |
| Repeated installs, large compiler caches, or slow Next.js and TypeScript builds | [Builds, caches and type checking](skills/frontend-performance/references/build-caches.md) explains which work each cache can remove and how to prove reuse. |
| Playwright stalls, slow shards, or a release waiting on health checks | [Browser tests and release verification](skills/frontend-performance/references/browser-and-release.md) traces server startup, retries, reporting, image verification, and deployment. |
| A candidate change looks promising on one run | [Benchmarking](skills/frontend-performance/references/benchmarking.md) shows how to match inputs, pair runs, compare costs, and preserve validation. |
| An optimization depends on a tool's behavior or default | [Current tooling](skills/frontend-performance/references/current-tooling.md) links to primary documentation and identifies version-dependent behavior to verify. |
| You want to see the reasoning on concrete data | [Worked examples](skills/frontend-performance/references/worked-cases.md) tracks four synthetic cases from observation to decision. |

## What the skill returns

The [skill](skills/frontend-performance/SKILL.md) starts from a target such as time to green or time to a verified deployment. It returns a timeline with evidence links, the phase most worth investigating, and a recommended comparison or a verified result. It distinguishes observations on different commits from controlled measurements and estimates. It also distinguishes an archive being available, being restored, and the compiler being able to use it.

The packaged skill loads as text. It uses the project’s available tools and read access when investigating real runs. The sample commands and estimates in the guide are optional. All worked examples use synthetic data.

## Install in Codex

Download and unzip [frontend-performance.zip](https://github.com/Harivarshan/frontend-performance-skill/releases/latest/download/frontend-performance.zip). Copy the extracted `frontend-performance` folder into your project’s `.agents/skills/` directory. To make it available across projects, use `~/.agents/skills/`. Start a new Codex session in your project. For another Agent Skills client, use its documented skill location. [Codex skill locations](https://developers.openai.com/api/reference/cli)

Try one of these requests:

> Use $frontend-performance to research why this PR took longer than usual. Compare its successful attempt with recent runs, trace the required check that finished last, and include the cost of any rerun.

> Use $frontend-performance to design a same-source benchmark for our compiler cache. Measure restore, extraction, compile, archive, and upload. Preserve type, browser, and image verification.

> Use $frontend-performance to explain why our staging build got faster while the full release stayed flat. Link the actual build, scan, readiness, and E2E timings.

## License

[MIT](LICENSE)
