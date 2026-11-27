---
name: api-and-event-contracts
description: Define and test HTTP and event contracts with OpenAPI 3.1 and AsyncAPI 3, versioned schemas and contract tests between producers and consumers. Use when adding or changing an endpoint, a Kafka topic, an event schema or an SSE/WebSocket message.
---

# API and event contracts

Contracts live in `docs/contracts/` and change in the same PR as the spec and the code.

- **HTTP:** `docs/contracts/openapi.yaml` (OpenAPI 3.1). For FastAPI, generate it from the app and diff it in CI; never hand-edit generated output.
- **Events and streams:** `docs/contracts/asyncapi.yaml` (AsyncAPI 3.0): channels (Kafka topics, SSE streams), messages, payload schemas, headers (`traceparent`, `idempotency-key`, `schema-version`).
- **Schemas:** JSON Schema, shared by Python (Pydantic) and TypeScript (zod), from one source of truth. Register them in Schema Registry at runtime.

## Evolution rules
- Backward-compatible changes only, within a version: add optional fields, never remove or retype them.
- A breaking change means a new version (`EventV2`, `/v2/...`) plus a migration plan in an ADR.
- Topic names: `<domain>.<entity>.<event>` (for example `events.raw`, `events.enriched`). Keys are chosen for ordering (for example `tenant_id`).
- Every message carries `event_id`, `occurred_at` (RFC 3339 UTC), `schema_version` and the trace context.

## Contract tests
1. **Schema tests:** example payloads validate against the schemas, and invalid ones are rejected.
2. **Producer tests:** what the producer emits validates against the contract.
3. **Consumer tests:** the consumer accepts every contract example, and ignores unknown fields.
4. **Compatibility check in CI:** the new schema is compatible with the previous version.
5. **Drift check:** the generated OpenAPI equals the committed one.

## Checklist
- Spec scenario ↔ contract message ↔ test example all line up.
- Error responses use RFC 9457 Problem Details (`application/problem+json`).
- Idempotency and retry semantics are documented for every write.
