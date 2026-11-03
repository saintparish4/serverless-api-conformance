# Pre-registered pass criteria

## Docs claims

Each claim in `docs_claims.yaml` gets exactly one result, from the real API:

- **pass**: every linked scenario passed on the real target.
- **contradicted**: a linked step with a `finding` reproduced on the real target.
- **differs from provisional**: the docs were silent and my guess was wrong. Not a
  docs failure. The mock is corrected and the observed behavior is recorded.
- **not run** or **not testable here**: stated as such, never counted as a pass.

A claim that passes on the mock only is not a pass, because the mock was written
from the same docs.

## Mock versus real

Allowed differences are listed in `tolerance.yaml` and nowhere else. Timing is not
compared, except where a scenario expects a status at a stated time. For each other
difference, one of two things must happen, in writing, in `results/mock-vs-real.md`:

1. The mock is fixed, and the entry cites the commit and the observation.
2. It is published as a surprise, with the reason it was not fixed.

`amendments.md` records any change to these files after this commit, with its date
and reason. Nothing here is edited silently.

## Faults

Latency, connection reset, timeout after the server accepted the request, 429 with
and without `Retry-After`, 5xx, partial body, duplicate delivery, slow streaming.
For each: retries, backoff, the exception surfaced, its message, and jobs created.

## Quality

Negative controls 10 of 10 caught, on an unedited baseline that passes. Mutation
survivors each justified in the write-up. mypy strict and ruff clean. CI green on a
clean clone. A/A shows no differences on both targets.

## Real-API stop rules

Stop and write down what happened if any of these occurs:

- Billing shows more than the budget in `budget.yaml`.
- A job stays `IN_QUEUE` or `IN_PROGRESS` longer than 15 minutes.
- Any response other than a 200-range or the error codes the scenario expects
  occurs on more than 3 consecutive scenarios.
- Runpod's status page reports an incident affecting Serverless. Results from
  during an incident are marked and rerun.