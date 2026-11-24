# Diagnose the pipeline before changing it

## Define the outcome and the cohort

Start with a question that names the finish line. “Can we make the last required check finish sooner?” and “Can we reduce container build cost?” can have different answers.

| Measure | Start → finish | Use it for |
| --- | --- | --- |
| Revision feedback time | First CI trigger for that revision → first passing set of required checks | Developer wait, including queueing, failed attempts and reruns |
| Successful attempt elapsed | First executed job starts → last required result on that attempt | Comparing successful work and inter-job gaps |
| Release elapsed | Release trigger → chosen endpoint, such as healthy revision or release verification | Delivery time with the real gates |
| Runner allocation | Sum of individual job durations | Resource consumption across parallel jobs, before applying billing rules |
| Phase time | Documented start marker → end marker | Finding work to remove within a job |

Write down the cohort before calculating a trend. Match workflow, event, target branch or environment, validation workload, and relevant configuration. Treat a release build, a PR image, and a cache warmer as three distinct workloads. Preserve successes, failures, cancellations and reruns in the inventory. Report the sample size and range alongside the median. A few smooth runs cannot establish tail reliability.

## Reconstruct the actual job path

1. Resolve the revision checked out by the workflow. In some systems, a pull request job checks out a synthetic merge tree. The displayed PR head and the tree that actually built can differ.
2. Read the workflow, reused jobs and actions, conditions, required-check configuration, build commands, and final gate at that revision. A green job name alone does not show which conditional steps executed.
3. Line up job timestamps. Follow the dependencies into the finish line and identify the predecessor that actually held each join. Include setup, inter-job delay, and the report or final gate. When two lanes overlap, shortening the earlier finisher can save runner time without shortening feedback.
4. Measure one ordinary run and a slower representative run at the step level. Search for timings, cache outcome, retries and failed steps. Use application or tool logs for boundaries that share a CI step. Mark intervals inferred from console messages as approximate.
5. Explain earlier completion where supported. For example, “the browser report waits for four shards, and shard 4 finished 42 seconds after the image job” ties a timing fact to the gate. A faster image step matters to overall wait only where its path controls completion.

## Collect evidence from GitHub Actions

In a checkout with read access, fill in a repository, run ID and attempt number. The attempt-specific endpoint lets you inspect jobs belonging to that attempt. Use workflow and check-suite history to reconstruct retained successes and the finish line for a partial rerun.

```sh
repo='OWNER/REPOSITORY'
run_id='RUN_ID'
attempt=1

gh api -X GET "repos/$repo/actions/runs/$run_id/attempts/$attempt/jobs" \
  -f per_page=100 --paginate \
  --jq '.jobs[] | [.id, .name, .conclusion, .started_at, .completed_at] | @tsv'
```

To read timestamps inside one identified job:

```sh
job_id='JOB_ID'
gh api "repos/$repo/actions/jobs/$job_id" \
  --jq '.steps[] | [.name, .conclusion, .started_at, .completed_at] | @tsv'
```

Check null or missing timestamps and keep skipped steps out of duration calculations. Prefer a narrow read of the implicated job's log or report over dumping an entire workflow. Record the source run, attempt, job and artifact URLs for reproducibility. Avoid including credentials, private URLs, environment values or raw logs in shared reports. [GitHub Actions jobs API](https://docs.github.com/en/rest/actions/workflow-jobs)

## Record what the evidence supports

| Claim | Evidence that would support it |
| --- | --- |
| “This cache was available” | Registry or CI cache lookup for the actual key and scope |
| “The build restored the archive” | Resolved key or digest plus successful download and extraction |
| “The compiler reused work” | Compiler diagnostics, cached BuildKit vertices or a matched comparison that isolates the state |
| “This changed the PR wait” | Comparable first-trigger-to-green or successful-attempt data plus the dependency path to the required result |
| “The new path shipped” | A normal run on the relevant revision and environment, with executed steps and outcome |

When evidence stops at availability, report availability. When a job log combines phases, report the combined duration and identify what instrumentation would distinguish them. Separate what you observed from why you think it happened.
