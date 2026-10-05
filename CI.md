# Check financial inputs in a CI job

The [workflow template](financial-input-audit.yml) runs AhaSignals PIT 0.2.0rc1
on a file in **your** repository. Copy it to
`.github/workflows/financial-input-audit.yml` in your project.

## Prepare the input

The default path is `research/audit-input.json`. Supply a permitted research
snapshot using the [batch input contract](https://ahasignals.com/examples/ahasignals-pit/v0.2.0rc1/BATCH-AUDIT.md).
For a first installation test, use the synthetic example in a scratch repository
and label the result as a software smoke test. Before using the workflow as a
research gate, replace it with your actual feature input. A passing synthetic
fixture does not check your research data.

The template runs manually or on pull requests touching `research/` or the
workflow. Adapt that path filter to your input producer; changes outside those
paths do not trigger it. If your pipeline generates the input in CI, insert that
step before **Check declared financial inputs**, using the same output path.
The template accepts one JSON batch; JSONL requires the documented
`--facts-jsonl` argument and retention of both source files.

## Gate behavior

| Exit | Result | Job behavior |
| --- | --- | --- |
| 0 | PASS | Succeeds within the checked scope |
| 1 | FAIL | Saves report, then fails |
| 2 | ERROR | Saves report when available, then fails |
| 3 | UNKNOWN | Saves report, then fails |
| Any other exit or missing exit | Execution failure | Fails; never defaults to PASS |

The check step temporarily permits a nonzero shell exit so the report can be
retained. The final step restores the exact gate. Do not remove the final step
or add `continue-on-error` to the job.

To require the result before another job, use `needs: financial-input-audit`
and the default success condition. Repository branch-protection requirements
must be configured separately if you want this check to block merges. Skipped
path-filter runs need consideration when configuring a required check.

## Reports and access

`UPLOAD_AUDIT_REPORT` defaults to `"false"`. The report is written to the runner
workspace but is ephemeral when upload is disabled. Set it to `"true"` only if
the report is allowed in that repository's Actions artifacts. Reports include
dataset identifiers, row IDs and evidence references. A public repository must
not receive confidential input, even when report upload is disabled.

When enabled, only `audit-report.json` is uploaded, with a seven-day retention.
No full inputs, environment files or workspace directories are uploaded. Keep
the corresponding original input in your own controlled storage. The default
job summary contains only the fixed numeric exit code; it never prints input
contents or the full report into logs.

## Repeatability and limits

Installation pins the released wheel version and SHA-256 in
[requirements-audit.txt](requirements-audit.txt). Update both intentionally
after reviewing a new release. Actions are also pinned to commits.

The workflow provides local declared-input checks, not source authentication or
backtest certification. Keep the original research cutoff; moving it after a
future disclosure changes the research question. AhaSignals is independent of
GitHub and referenced software platforms. Research and education only.
