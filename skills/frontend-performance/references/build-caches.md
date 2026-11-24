# Builds, caches and type checking

## Draw the cache boundaries

An install, image build and framework build can each cache different work. Trace where the data lives and what makes it valid.

| Layer | What it can save | How to verify reuse |
| --- | --- | --- |
| Package manager store | Re-downloading package tarballs or content | Package manager fetch/restore log and network bytes |
| Installed dependency tree or Docker install layer | Installation and lifecycle scripts | Compatible key and skipped install, or a cached BuildKit install vertex |
| BuildKit result cache | Re-executing a build vertex and its dependencies | Per-vertex build logs and imported cache source |
| Compiler or incremental state | Rebuilding modules, rechecking files or redoing other framework work | Tool-specific reuse counters or state-controlled experiment |
| CI cache or registry transport | Making cached contents available on another runner | Restore key or digest, transferred bytes, extraction and publication outcomes |

An application config edit can invalidate compilation without invalidating dependency installation. Put dependency inputs, such as lockfiles, workspace manifests, install configuration, runtime ABI and platform, on their own boundary. Consider compiler version, bundler mode, relevant configuration, generated inputs and environment-sensitive build inputs when deciding compiler compatibility. Hash compatibility inputs rather than guessed file patterns. Keep raw environment secrets out of keys and archives.

When diagnosing Docker builds, inspect the Dockerfile order and build context. If source or unrelated files enter the dependency stage, small app edits can invalidate the install step. Use BuildKit's plain progress logs in the repo's existing build invocation to distinguish importing a result cache, loading layers and executing a command. A builder-local cache mount may disappear with the runner. Verify the backend and driver actually export the state expected on the next runner. [Docker cache optimization](https://docs.docker.com/build/cache/optimize/) · [Cache backends](https://docs.docker.com/build/cache/backends/)

For remote cache design, compare expected transfer and export cost with avoided work. A cache that ships nearly the whole intermediate build can help some workloads and overwhelm others. Separate the dependency and compiler exports when their size, invalidation behavior or retention needs differ. Avoid a warmer if branch scope, incompatible keys or eviction prevent later jobs using it. Check consumer logs and measure the additional runner allocation. Estimate the warmer’s return as the work avoided across verified hits before expiry, minus the warmer’s own work and any extra transfer. Keep that estimate separate from time saved on the PR critical path.

## Read a compiler-cache timeline

1. Record the resolved key or immutable digest, hit type and bytes. Check that the downloaded state is actually available inside the build process or container. Exact-key matching and usable fallback are distinct.
2. Time transfer, extraction, compiler work, archive creation, export and upload where possible. Confirm when phases overlap. Keep registry layer transfer and package installation distinct even if the build tool aggregates them.
3. Look for internal reuse. The outer cache can be complete while an inner tool rejects some state after a configuration, source, generated route or version change. A compatible prefix and a faster run show different facts.
4. Measure whether the next reader can restore the cache after publication. An emitted cache size, an upload status, or a cache-list entry establishes a different milestone from a verified subsequent restore.

For archive changes, compare versions against the same uncompressed file list, bytes and modes. An inner archive may be wrapped or compressed by the transport. A shorter pack step can be offset by longer extraction or slower upload. Streaming can avoid temporary files and extra disk passes. Check actual runner CPU, disk and network pressure. Use distinct format versions and preserve legacy compatibility deliberately. Test the expected cold, warm, legacy, corrupt, truncated and failed-build behavior. Keep failures confined to the declared cache policy and leave a straightforward rollback. Verify both the migration run and a later warm reader.

## Next.js and TypeScript

Read the locked Next.js, Node and TypeScript versions. Separate the app's commands for local development, CI image validation and production build. Current docs can describe bundler defaults or lint behavior that differ from the pinned versions.

- Split build phases into compile and plugins, type checking, lint where applicable, route collection and static generation, source-map work, and trace/finalize. Use native timers or compiler traces when the console is too coarse. Note overlapping work and avoid adding subphase durations to their parent.
- To isolate TypeScript reuse, preserve build inputs and other compiler state. Compare a full candidate cache with a copy missing only the documented TypeScript incremental state. Test a same-source seed and representative cross-revision state separately when retention is the question.
- When testing a faster standalone type check, inspect its loaded config, generated route declarations and guards, excluded files, and failure behavior. Use clean generated output so stale or removed routes cannot accidentally make the check pass. Exercise a meaningful generated-type error in a disposable fixture. Compiler work and correctness can move independently.
- Keep runtime and architecture in the comparison. A memory ceiling or a compiler worker can change process overlap and GC. Inspect actual process exit evidence and peak memory where available before attributing a kill to memory. Measure a worker or memory change with the same workload and checks.

Sources: [Next.js CI build caching](https://nextjs.org/docs/app/guides/ci-build-caching), [Next.js CLI](https://nextjs.org/docs/app/api-reference/cli/next), and [TypeScript incremental mode](https://www.typescriptlang.org/tsconfig/incremental.html).
