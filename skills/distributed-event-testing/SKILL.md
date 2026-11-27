---
name: distributed-event-testing
description: Test distributed, event-driven systems end to end — event flows and state transitions, eventual consistency, duplicate, delayed, missing, out-of-order and poison events, retries and backoff, idempotency, DLQs, consumer rebalances, sagas and compensation, downstream validation and reconciliation, and tracing with correlation IDs. Use when building or changing producers, consumers, topics, workflows across services, or when preparing to explain how to test such systems.
---

# Testing distributed, event-driven systems

## 1. Model the flow first
- Map the event flow: producers → topics → consumers → stores → downstream consumers. Draw it as a sequence diagram (see `uml-and-c4-diagrams`) and record it in AsyncAPI (see `api-and-event-contracts`).
- For each entity, write the **state machine** (`stateDiagram-v2`): the allowed transitions, and the invariants that must always hold (for example "an event is enriched at most once", "the enriched count never exceeds the ingested count").
- Write the delivery guarantees per hop: at-most-once, at-least-once, or effectively-once (idempotent consumer, or Kafka transactions).

Each failure mode below becomes a spec scenario (Given/When/Then) before it becomes a test.

## 2. Test pyramid for event-driven systems
| Level | What it proves | How |
|---|---|---|
| Unit | Handlers are pure and idempotent; the state machine rejects illegal transitions | fakes; property-based tests (Hypothesis, fast-check) |
| Contract | Producers and consumers agree on schemas and headers | schema validation, compatibility checks, Pact message pacts |
| Component | One service with its real broker and store | Testcontainers (Kafka, DynamoDB through the emulator) |
| Integration and e2e | The whole workflow across services | Compose or kind; trace-based assertions |
| Resilience | It holds up under faults | Toxiproxy, Chaos Mesh, fault-injecting test producers |

## 3. Failure modes to test (each one is a scenario)
- **Duplicate events:** publish the same event twice, or replay after a consumer crash before commit. Assert one effect (idempotency key, or upsert with a version check).
- **Delayed events:** inject latency and assert that timeouts, SLOs and eventual convergence hold.
- **Missing events:** drop a message. Assert detection (gap or sequence check, reconciliation) and recovery.
- **Out-of-order events:** reorder events for the same key. Assert that version or sequence checks reject stale updates (no last-writer-wins bugs). Know where ordering is guaranteed: within a Kafka partition, for the same key.
- **Poison and invalid events:** assert they go to the DLQ with the reason in headers, without blocking the partition, and can be redriven.
- **Retries:** transient downstream failures. Assert exponential backoff with jitter, a maximum number of attempts, and no retry storms. Retries plus idempotency must give no duplicate effects.
- **Consumer crash and rebalance:** kill a consumer mid-batch. Assert no loss (commit after processing) and no duplicate effects (idempotency).
- **Partial failure across services (sagas):** a step fails after earlier steps succeeded. Assert compensation, or the outbox pattern, keeps the system consistent.
- **Backpressure:** a slow consumer. Assert bounded memory, rising lag that recovers, and no dropped events.

## 4. Eventual consistency in tests
- Never `sleep(5)`. **Poll for the expected state with a timeout and interval** (an await-until helper, `tenacity`, or `vi.waitFor`). Fail with the last observed state.
- Assert the invariants continuously, not only the final state.
- Assert convergence time against the SLO, which links to the NFR scenarios (see `performance-and-nfr-testing`).
- Make tests deterministic: unique ids per test, isolated topics or consumer groups, and controlled clocks where possible.

## 5. Downstream validation
- Validate the downstream data as well as the HTTP response: query the store and the downstream topic.
- **Reconciliation:** compare counts and checksums between ingested, enriched and stored data (per time window). Run it as a test and as a scheduled job with an alert.
- **Data contracts:** downstream consumers' expectations are checked in CI.

## 6. Observability as a test tool
- Every event carries a **correlation id** (`event_id`) and W3C `traceparent` in the Kafka headers. Every log line and span carries them.
- **Trace-based tests:** assert the expected spans exist across services (with an in-memory exporter, or a query to Tempo), giving the path, timings and errors.
- Debug by following one correlation id across logs, traces and metrics. Write the steps into runbooks (see `observability-instrumentation`).
- Metrics that reveal distributed bugs: consumer lag, DLQ rate, retry count, duplicate-detected count, and the reconciliation mismatch count.

## 7. Explaining it (interviews and clients)
Structure the answer as: **model → levels → failure modes → consistency → downstream validation → observability → automation in CI**, then give a concrete example from real work, naming the bug that was found and how the test caught it.
