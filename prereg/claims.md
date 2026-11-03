# Pre-registered claims

Registered in this commit, before any run against the real API. Tested versions:
`runpod` 1.12.0 from PyPI, Python 3.12, public docs as read on 2026-11-01. Every
result describes those versions on the dates tested and nothing else.

I do not know how Runpod works internally. The mock is built from public docs and
recorded traffic. It models observed behavior and is not a statement about the
service's design.

## Claim 1: the mock matches the real service on the documented operations

Scope: `run`, `runsync`, `status`, `stream`, `cancel`, `retry`, `health` on a
queue-based endpoint.

**Falsified if**, after the corrections allowed by `pass_criteria.md`, any scenario
in `scenarios.md` differs between mock and real outside `tolerance.yaml` and the
difference is neither fixed nor published as a named surprise.

## Claim 2: the SDK behaves as documented under these faults

Scope: the faults in `pass_criteria.md`, with default SDK settings.

**Falsified if** any fault-matrix row shows behavior that contradicts a documented
statement. Each contradiction is a finding, and this claim is expected to be
partly false. See `predictions.md`.

## Claim 3: the suite can detect a wrong mock

**Falsified if** any negative control survives, or two A/A runs report a difference.

## Claim 4: the job count reconciles

**Falsified if** the submissions in my ledger, the executions in the worker logs,
and the console's request count cannot be reconciled marker by marker. A gap is
published as a finding, not explained away.

## What this project does not claim

No statement about Runpod's internals, reliability, performance, or any SDK version
other than the pinned one. No statement about GPU workers: the real target is one
CPU endpoint.