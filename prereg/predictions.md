# Predictions and prior exposure

## What I had already seen when I registered this

Honest pre-registration means saying what was known first.

- The SDK source and the public docs had been read, so these are informed
  predictions, not blind ones.
- The code in this repo was developed against the mock, so the expected results
  below for the mock were already observed. Only the real-API results are unknown.
- **No job has been run on the real API.** One stray request reached
  `api.runpod.ai` on 2026-11-01 while the docs-snippet tool was being developed. It
  used a fake key and a nonexistent endpoint, returned 404, and created nothing. The
  tool now skips hard-coded production URLs and runs behind a dead proxy.

## Predicted SDK findings, runpod 1.12.0

| ID | Prediction | Upstream status on 2026-11-01 |
|---|---|---|
| F-01 | Retries are mounted for `http://` only, so production HTTPS gets none | Issue #590, PR #603 open |
| F-02 | `Job.stream()` never returns after CANCELLED | PR #622 open |
| F-03 | The docs' `except TimeoutError` misses `requests` ReadTimeout from `run_sync` | None found |
| F-04 | `run_sync` and `Job.output` return None on FAILED, dropping the error | None found |
| F-05 | The SDK has no method for the documented `/retry` | None found |
| F-06 | A `Job` caches FAILED, so a retried job reads as still failed | None found |
| F-07 | An empty input envelope gets wrapped a second time | PR #666 open |
| F-08 | `Job.output(timeout=n)` counts polls, not seconds | None found |
| F-09 | Async `Job.output()` default timeout expires immediately | PR #662 open |
| F-10 | `run_sync(timeout=n)` can take about twice n | None found |
| F-11 | A `run_sync` that times out leaves the job running with no id returned | None found |
| F-12 | The async client turns a 404 into `KeyError('status')` | None found |
| F-13 | Async `run` nests an `input` envelope that sync `run` passes through | None found |
| F-14 | `--test_input` never runs a generator handler and reports success | None found |
| F-15 | The local API server returns a bare 500 for a failed generator job | PR #589 closed unmerged |

"None found" means a search on that date, not proof. Search again before filing.

## Predicted docs inconsistencies

| ID | Source | Inconsistency |
|---|---|---|
| DOC-1 | operation-reference, /status, /stream, /cancel Python tabs | Three snippets call `os.getenv` without `import os` |
| DOC-2 | operation-reference | The /runsync note says `wait` does not control retention; the /retry note says up to 5 minutes with `?wait=t` |
| DOC-3 | operation-reference /status vs job-states | /status omits CANCELLED and TIMED_OUT, though /retry needs TIMED_OUT |
| DOC-4 | job-states vs send-requests | TIMED_OUT covers a job that expired in the queue; TTL expiry is documented as a 404 |
| DOC-5 | endpoint-configurations vs job-states | Passing the execution timeout "fails" the job; job-states says TIMED_OUT |
| DOC-6 | operation-reference vs send-requests | curl examples send a bare key; send-requests uses `Bearer` |
| DOC-7 | operation-reference /cancel Python tab | The loop handles `ERROR`, not a documented status, and misses CANCELLED and TIMED_OUT |
| DOC-8 | send-requests vs endpoint-configurations | Execution timeout default is 10 minutes in the policy table, while REST v2 defaults to 5 |
| DOC-9 | operation-reference vs field reports | /health shows `idle` and `running`; Runpod's own guide reports six worker fields |
| DOC-10 | send-requests | The rate-limit table omits `/retry` and `/health` |