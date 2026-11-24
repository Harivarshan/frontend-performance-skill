# Benchmark a frontend pipeline change

Write down the metric and the decision threshold before running an expensive comparison. Choose a meaningful threshold for the project's wait, cost or reliability goal and its observed variation.

- Record exact source, dependency lock and tool versions, build inputs, runner class and architecture, and expected validation outputs. For cache tests, identify the seed and record which parts start cold, reuse dependencies, or reuse compiler state. Treat seed creation and required transfer as part of the operational cost where relevant.
- Compare one changed variable. Reuse a frozen cache seed or immutable artifact so that the first arm's output cannot improve the second. Use independent workspaces and explicit cache namespaces. Match runner resources and counterbalance order when practical. Same-host sequential runs reduce machine variation but can still differ because of file caches, thermal load or lingering processes. Separate cold and warm claims.
- Preserve test identities, check outcomes and relevant build outputs. Record platform, actual restore status, step time, total elapsed time, retry behavior, and any expected trade-off such as archive size or runner allocation. Reject unmatched comparisons and report failed or incomplete attempts with the reason they were excluded from the latency sample.
- Calculate savings as baseline elapsed time minus candidate elapsed time. For paired samples, summarize the individual differences and show their range. Use a median and range for small samples. Repeated historical observations on varying commits can describe what developers see in practice. Reserve causal claims for a comparison that isolates the change.

When measuring developer wait, also track elapsed time from the first CI trigger for a given revision to the first passing set of required checks. Include queueing, failed attempts and reruns for that revision. Report revisions that never passed or were superseded alongside the completed times. Keep this measure separate from successful-attempt latency.

### Worked example with synthetic data

After setup, assume image validation and browser verification start together. The baseline image job takes 7m30s, the browser lane finishes at 8m40s including its report, and a final gate takes 15s. Complete CI takes 8m55s. Reducing the image job to 6m45s saves 45s of one runner's work. Complete CI stays at 8m55s if the other timings and dependencies stay the same.

Suppose three paired experiments show image-job savings of 18s, 42s and 51s. Report a 42s median and an 18–51s range. Include equivalent check outcomes and state that the browser lane controlled total feedback time in this example. In another run, the image job can control the wait if it finishes last.

### Results to hand back

Use the level of detail the user needs. Include the target and critical path, the phase that changed, before and after timings with sample size and range, what was held fixed, correctness evidence, and the smallest relevant follow-up. A normal CI or deployment run verifies the real route. A separate matched comparison measures the performance effect.
