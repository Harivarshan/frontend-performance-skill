# Worked examples

The data in these examples is synthetic. Use the reasoning and evidence requirements to investigate your own repository.

## 1. A faster cache did not shorten the PR wait

An image job used to take 8m00s and now takes 7m10s. The browser lane, including the merged report, finishes at 8m40s in both versions. Both run concurrently and a final result job takes 15s. The run finishes at 8m55s in either case. The image change saves 50s of runner allocation in this example. The browser lane still controls when the required result is available.

Inside the image job, suppose the candidate archives state 24s faster and its output is 30% smaller. Use a frozen input to measure both pack and extract, then count transfer in the hosted build cycle. If the compiler phase also sped up on a different runner or cache seed, keep the total as an ordinary CI observation. To show the codec's contribution, use repeated pairs with equivalent source, seed and validation. To show developers wait less, follow the final gate and report both revision-to-green and successful-attempt times.

## 2. A slow browser job has two separate problems

A job takes 7m40s: 35s of install, 16s before the server announces readiness, 65s until its first usable route response, 6s until the first test, 5m24s of suite execution and 14s to finish reporting. A trace shows two failed attempts at a shared menu assertion. Each attempt ran navigation and fixtures before its assertion expired.

Treat first-route compilation and repeated test work as separate hypotheses. Check what triggered compilation and whether each shard repeated it. Record when the menu appeared relative to the assertion, and test a targeted readiness change first if retries dominate the tail. Report first-attempt outcomes and shard wall. With parallel workers, the failed attempts' durations cannot simply be subtracted from total elapsed time.

If a runner image might remove 25s of install, include the cold pull and compare across equivalent cache states. Even a zero-second install could remove only 35s from this job. Use the phase breakdown to put the bigger intervention first.

## 3. A restored archive tells you little about a slow build

Two releases both report “cache restored.” The first ran its type checker in 18s and the second in 58s. The second has a changed route tree and a different runner. This is evidence of more elapsed time in type checking, with several possible causes.

Measure the same revision in fresh processes or controlled builds. Compare the full cache with a copy containing the identical bundler state and no TypeScript incremental state. Verify generated input sets and diagnostics. If same-source state helps, compare a realistic cross-revision seed. This distinguishes a working incremental cache whose entries do not survive a typical change from a broken restore or an incompatible cache key.

## 4. A faster build meets a slower release

A release image job improves from 12m30s to 10m30s. The health gate increases from 40s to 60s. A staging E2E retry adds 100s to the path. In this simple serial example, those two changes consume the full two-minute image saving. Full release elapsed stays flat.

Keep image and endpoint measures side by side. Find the actual artifact, smoke/scan evidence and release attempt that produced the result. Check ordinary runs with the new path for reliability. A release job can be faster and valuable while another part of the verified deployment still controls the wait.
