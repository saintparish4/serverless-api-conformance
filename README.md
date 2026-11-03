# serverless-api-conformance

A small conformance suite and mock service for one GPU-serverless queue API:
Runpod's queue endpoints (`run`, `runsync`, `status`, `stream`, `cancel`,
`retry`, `health`), used to audit the official Python SDK, `runpod` 1.12.0.
The point is the evidence around it: claims and pass criteria registered before
any run ([prereg/claims.md](prereg/claims.md)), a mock built from public docs
and recorded traffic, one scenario format run against both the mock and the
real API, and fault injection with a published fault-vs-behavior matrix.

I do not know how Runpod works internally. The mock models observed behavior,
not the service's design, and every result describes the versions and dates
tested.

## Build and test

Needs Python 3.12 and [uv](https://docs.astral.sh/uv/). Docker comes in later,
with the mock stack.

```bash
uv sync --frozen
uv run python -c "import runpod; print(runpod.__version__)"   # 1.12.0
```

There are no tests yet. They arrive with the mock.

## Results so far

None. Only the pre-registration is committed, and no job has been run against
the real API. Registered so far:

| What | Count | File |
|---|---|---|
| Claims, each with what falsifies it | 4 | [prereg/claims.md](prereg/claims.md) |
| Legal lifecycle transitions (all others illegal) | 14 | [prereg/lifecycle.md](prereg/lifecycle.md) |
| Documented claims under test | 26 | [prereg/docs_claims.yaml](prereg/docs_claims.yaml) |
| Predicted SDK findings / docs inconsistencies | 15 / 10 | [prereg/predictions.md](prereg/predictions.md) |
| Real-API budget, estimated billed seconds | 600 | [prereg/budget.yaml](prereg/budget.yaml) |

The predictions were made after reading the SDK source and the docs, so they
are informed, not blind. [prereg/predictions.md](prereg/predictions.md) says
what was known before registering.

## Status

- [x] Claims, lifecycle, docs claims, pass criteria and budget pre-registered
- [ ] Raw material: SDK history, frozen docs, scrubbed captures, local worker
- [ ] Mock service with an in-memory job store and a configurable clock
- [ ] A scenario for every documented claim, and the scenario list registered
- [ ] Hypothesis stateful tests and race tests of the lifecycle
- [ ] Fault injection and the fault-vs-behavior matrix
- [ ] Real-API comparison and external job-count reconciliation
- [ ] Negative controls, mutation testing and CI
- [ ] Upstream issues and PRs, with outcomes tracked
- [ ] External validation and write-up

## Scope

One API and one SDK. No pod management, no billing features, no new SDK. The
real target is one CPU-only endpoint (max 1 worker, scale to zero) under a
10 USD prepaid balance with automatic payments off. Changes to anything in
`prereg/` after registration are logged in
[prereg/amendments.md](prereg/amendments.md).

## Data

Docs claims are tested against dated snapshots of public pages on
docs.runpod.io, not the live pages. Recorded traffic is scrubbed of keys and
endpoint ids before it is written. API keys are read only from the environment
and are never committed.

## License

MIT. See [LICENSE](LICENSE).
