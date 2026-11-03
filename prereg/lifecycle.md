# Pre-registered job lifecycle

Status names come from the docs' job-states page. `EXPIRED` is a model state only:
a client sees it as a 404, because the docs describe expiry as deletion.

```text
        ┐─────────────┬─────────────────── RETRY ────────────────────────────┐
        ▼             │                                                      │
   ┌──────────┐       │         ┌─────────────┐              ┌───────────┐   │
   │ IN_QUEUE │─START─⌢────────▶│ IN_PROGRESS │─── FINISH ──▶│ COMPLETED │   │
   └────┬─────┘       │         └──┬───┬───┬──┘              └───────────┘   │
        │ CANCEL      │            │   │   │                                 │
        │             │            │   │   │                                 │
        │        ┌────┴───┐        │   │   │           ┌───────────┐         │
        │        │ FAILED │◀─ FAIL ┘   │   └─TIME_OUT─▶│ TIMED_OUT ├─ RETRY ─┘
        │        └────────┘            │               └───────────┘
        │                              │
  ┌─────┬─────┐                        │
  │ CANCELLED ┤◀── CANCEL ─────────────┘
  └───────────┘

                                                        ┌─────────┐
                every state except EXPIRED ── EXPIRE ──▶│ EXPIRED │
                                                        └─────────┘
```

| From | Event | To | Source |
|---|---|---|---|
| IN_QUEUE | START | IN_PROGRESS | job-states |
| IN_QUEUE | CANCEL | CANCELLED | operation-reference /cancel |
| IN_QUEUE | EXPIRE | EXPIRED | endpoint-configurations, Job TTL |
| IN_PROGRESS | FINISH | COMPLETED | job-states |
| IN_PROGRESS | FAIL | FAILED | job-states |
| IN_PROGRESS | TIME_OUT | TIMED_OUT | job-states |
| IN_PROGRESS | CANCEL | CANCELLED | operation-reference /cancel |
| IN_PROGRESS | EXPIRE | EXPIRED | send-requests, TTL warning |
| COMPLETED | EXPIRE | EXPIRED | result retention |
| FAILED | RETRY | IN_QUEUE | operation-reference /retry |
| FAILED | EXPIRE | EXPIRED | result retention |
| TIMED_OUT | RETRY | IN_QUEUE | operation-reference /retry |
| TIMED_OUT | EXPIRE | EXPIRED | result retention |
| CANCELLED | EXPIRE | EXPIRED | assumed: no page states retention for cancelled jobs |

Every other (state, event) pair is illegal.

## Questions the docs leave open

Each is a `provisional: true` step, resolved only by the real API.

| Question | Mock's guess | Scenario |
|---|---|---|
| What does cancel on a finished job return? | 400 | LC-03 |
| What does retry on a non-retryable job return? | 400 | LC-05, LC-06, LC-07 |
| Does TTL expiring in the queue give a 404 (send-requests) or TIMED_OUT (job-states)? | 404 | LC-10 |
| Does passing executionTimeout give TIMED_OUT (job-states) or FAILED (endpoint-configurations)? | TIMED_OUT | LC-11 |
| What does an over-limit body return? | 413 | LIM-02, LIM-04 |
| Are out-of-range policy values rejected or clamped? | rejected, 400 | LIM-05, LIM-06, LIM-07 |
| Is an out-of-range `wait` rejected or clamped? | rejected, 400 | LIM-08 |
| Does `?ttl=` work on `/run`? | yes | LIM-11 |
| Does `?wait` extend sync retention? | no | RET-05 |
| Is a bare key without `Bearer` accepted? | yes | OP-11 |
| Is an unknown endpoint a 404? | yes | OP-09 |
| Is a body with no `input` rejected? | yes, 400 | OP-10 |