# Design a benchmark that can change the decision

## Choose the experiment before seeing the results

State the hypothesis in terms of work removed. “Changing the cache codec should reduce archive handling and transfer” is testable. “Modernizing CI should save minutes” leaves too many causal possibilities.

Set one primary metric and practical decision threshold. Use wall time for developer wait, the maximum shard path for parallel tests, allocation for resource cost, and a spread or reliability measure where outliers matter. Add correctness and operational guardrails, such as equivalent tests, unchanged maximum memory, and no additional retry or transfer cost. Choose thresholds in context. A five-second saving on an unstable ten-minute job warrants more evidence than the same saving in a repeatable local loop.

| Hypothesis | Control | Candidate | Record |
| --- | --- | --- | --- |
| Smaller inner archives reduce build time | Existing codec, frozen decoded contents | New codec, same contents and modes | Pack, extract, upload, download, total cycle, bytes |
| Dependency retention survives source/config churn | Cold dependencies and compiler | Same cold compiler, compatible restored dependencies | Reuse proof, time and seed/export cost |
| TypeScript incremental state removes checking work | Fixed compiler cache with only TypeScript state absent | Identical state with TypeScript data present | Build/type phases, generated inputs, diagnostics |
| Browser readiness fix prevents full test restarts | Existing scoped condition | Candidate scoped condition, same tests and workers | First-attempt readiness, retries, final outcome, slowest shard |
| Release build cache shortens deployment | Current builder or isolated cold state | Candidate cache path on equivalent inputs | Exact image checks, build and cache costs, verified release elapsed |

## Control what can move the result

- Freeze source, lockfile, tool versions, runner resources, build arguments, validation and the relevant cache seed. Set up the test so the reset between arms leaves build outputs and caches in the intended state.
- Vary one thing. Give arms isolated workspaces, output tags and cache namespaces. Pin an immutable seed so one arm's output does not warm the other. On one host, account for OS page cache, process leftovers and host load. When order can bias the result, alternate the order or counterbalance it across pairs.
- Use same-host container or local probes to isolate a mechanism. Compare Linux architecture, memory, CPU and network before extrapolating from a laptop or an emulator to a hosted runner. Validate actual CI transfer separately if the probe avoids CI's cache backend.
- Pair comparable hosted observations and record actual runner class and resource differences. If hardware, inputs or cache state diverge materially, show those results as separate observations. Keep failed benchmark attempts and reasons visible.
- Make correctness observable. Verify the checked-out tree, actual cache key/audited seed, test identities and outcomes, and the produced image or assets. Define which properties must match. Byte-for-byte equality can be inappropriate for outputs with build IDs or timestamps, so check the required behavior and artifact identity within each arm.
- Size the sample from variation and expected benefit. Repeated pairs help reveal order effects and outliers. Small samples support a median and range, with a narrower claim than a stable percentile or reliability estimate.

## Interpret the results

Calculate each paired saving as baseline elapsed minus candidate elapsed. Report the median and range of paired differences, and name what the interval includes. When the metric is end-to-end, include setup and transfer the real path will pay. For a narrower diagnostic, explicitly state the excluded costs.

For developer wait, follow a revision from its first CI trigger to its first passing set of required checks, including failures and reruns. Choose an observation window before measuring. Report how many revisions passed by that deadline, how many never passed in the window, and how many were superseded. Compute paired latency only for matching completed observations and show the remaining outcomes alongside it. Set an acceptable passing fraction or retry rate in advance so faster surviving attempts cannot mask worse reliability.

- **Adopt** when the measured effect clears the useful threshold with equivalent validation and acceptable small-sample uncertainty.
- **Refine or repeat** when traces support the mechanism but runner, cache, correctness or sample differences make the total ambiguous. Add the smallest control that resolves the ambiguity.
- **Keep the baseline** when the candidate removes little net work, shifts the cost elsewhere, or weakens correctness or reliability. State which hypothesis the experiment actually tested.

## Verify after rollout

Where authorized, observe a real run on the new revision. A changed cache format may first read a legacy cache or run cold and write the new state. Find a later normal run that actually reads the new state before claiming warm reuse. Compare multiple ordinary runs before attributing a sustained trend. Keep checkout identity, queueing, retries and validation visible on branches or environments where the path differs.
