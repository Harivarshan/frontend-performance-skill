# Frontend pipeline diagnosis

Read the sections that match the slowest part of the current pipeline.

## Install and cache layers

- Time package fetch, extraction, install scripts and native dependencies separately where the logs support it. Compare compatible cache hits and misses. A preinstalled browser or build image can help if the avoided work is larger than the cold image pull and startup it adds.
- Separate dependency state from compiler state when their invalidation rules differ. Dependency keys normally include lockfile, package manager, runtime/ABI, operating system and architecture, and install options. Compiler keys also cover the framework, relevant config, build mode, compiler version and build inputs. Keep restore fallbacks inside a compatible namespace.
- Measure restore transport, decompression, compiler execution, archive creation and upload. An action's cache hit can refer to an exact key, while a fallback also provides files. A restored archive does not tell you how many compiler results were accepted. Look for the resolved key and evidence from BuildKit or the compiler itself.
- Keep archive format and CI transport separate in the analysis. An archive may be compressed again by CI or a registry. Compare the same bytes and file modes, then measure the real build cycle and transfer. Favor streaming when it removes temporary disk I/O. Test cold creation, warm restore, compatibility, malformed state and interrupted output. For a format change, version the key and keep compatibility only where intentional. Preserve a clean fallback and the existing trust boundary. Keep secrets and build credentials out of caches and archives.
- Check branch scope, retention and hit availability before adding a warmer. Measure the cost of the warmer and which later runs actually reuse its output.

## Build and type checking

- Break the tool's build into compilation, types, lint, page or asset generation, source maps and tracing as applicable. Some spans overlap. Attribute time to the phase only when timing boundaries support it.
- For incremental TypeScript, compare the same source and generated files with and without the existing incremental state. Hold Webpack or other bundler state fixed if isolating TypeScript. Compare state seeded from identical source with state inherited from another release to distinguish cache usefulness from invalidation.
- Validate a type-check optimization on generated inputs. A quick standalone check and the framework's build checker may cover different files or diagnostics. Prefer supported tooling and pinned-version evidence before changing compiler internals.
- Check resource failures with actual logs and peak memory where available. A kill signal alone has several causes. Benchmark worker isolation or memory changes against the same inputs and preserve the validation the old build performed.

## Browser tests

- Separate package/browser install, server startup, first route response, execution, retries and report merging. A dev server can log that it started before the first request triggers route compilation. Measure first response and first test start independently.
- Check whether every shard repeats the same setup and cold route build. Balance shards using elapsed test or file durations and validate unchanged test identities, retries, skips and aggregate reporting. More shards can add runner time or expose contention even when they reduce elapsed time.
- Use trace and test results to classify retries. If a shared readiness check times out, measure when the exact condition becomes true and compare a scoped fix with the baseline. Retain the readiness assertion. A longer timeout can reduce unnecessary restarts or prolong a genuine failure. Sum of failed attempt durations across workers measures work, so use shard wall time to evaluate the user's wait.

## Image and release paths

- Map the PR image separately from the release image. Confirm build arguments, base, runtime, platform, credentials and image identity before proposing cache sharing or artifact reuse. A validation image can differ in ways that matter to deployment.
- Time build and push, smoke, scan, cache publication, deploy/readiness and E2E individually. Measure the release path through its actual endpoint, such as verified staging release or healthy environment revision.
- Keep image verification and deployment tied to the same immutable digest when the pipeline supports it. Keep cache publication and artifact promotion consistent with the verification outcomes the repo requires.

References: [GitHub Actions caching](https://docs.github.com/en/actions/reference/workflows-and-actions/dependency-caching), [Next.js build caching](https://nextjs.org/docs/app/guides/ci-build-caching), and [Playwright sharding](https://playwright.dev/docs/test-sharding).
