# Browser tests and release verification

## Find the slow part of browser CI

The job usually includes package install, browser setup, a server, test execution and a report. Measuring only the browser test command can hide setup that every shard pays.

1. Separate the runner/container start, module install, browser binary or system package setup, server-ready log, first usable route response, first test, last test, report upload and report merge. With a development server, the first HTTP request Playwright uses to check readiness can trigger compilation.
2. Read the existing report or trace for retries, fixture setup, navigation and the exact assertion that waited. A timed-out readiness condition can lead to one or more full attempts repeating navigation and fixtures. Measure the timestamp for the condition where possible. Test a targeted remedy with the same assertions. Compare time to readiness, first-attempt success and the slowest shard path.
3. Inspect the shard and worker configuration. More shards may shorten test execution while multiplying package installs and first-route builds. More workers on one runner can increase CPU, memory or browser contention. Count test identities and parameters, compare outcomes and use per-test or per-file duration to assess skew. Equal counts can still leave a slow shard.
4. For a prebuilt runner or browser image, count a real pull and startup on the intended runner. Check versions of installed libraries, browser binaries and the test package, supported platform, headed/headless requirements, and report persistence. A compatible image can save dependency work without reducing the time inside a test. Compare ordinary cached and cold cases.

For a bundler or server-startup change, keep the exact tests and environment contract, then compare startup, slowest-shard wall time, retries and final report. A prebuilt application artifact can avoid per-shard compilation if it exercises equivalent behavior. Include that artifact's build and delivery job in the CI path, along with any queueing it introduces. Keep the existing smoke or deployment checks until equivalence is shown.

Sources: [Playwright webServer](https://playwright.dev/docs/test-webserver), [sharding](https://playwright.dev/docs/test-sharding), [retries](https://playwright.dev/docs/test-retries), and [Docker](https://playwright.dev/docs/docker).

## Determine whether CI and release builds are equivalent

Inspect the source SHA, build target, platform, base image, build arguments, relevant environment mode, asset generation, dependency stages and runtime startup. A PR validation image with review-specific configuration may exercise the source without being an artifact that can be safely promoted. If proposing build-once promotion, prove artifact equivalence and verify provenance and deployment identity first.

Segment release time into source validation, build and push, artifact smoke, vulnerability scan, cache publication, deployment, runtime readiness, and release E2E. Use timestamps from the job that gates the release and account for any overlaps. For example, a faster build can be masked by a cache upload on the same path, slower service readiness, or a live test retry after rollout. Measure readiness from the actual environment gate. Where supported, smoke, scan and deploy the same digest.

Adopt one change through the normal release path. Watch a representative staging release on the intended revision and verify the new step executed where expected. If the change affects only the shared PR action, a staging deploy can confirm the release remained healthy, and a later PR run verifies the changed cache path. Inspect preproduction and production separately when the release reaches them. Versions promoted to one environment do not prove the new path ran in the others.
