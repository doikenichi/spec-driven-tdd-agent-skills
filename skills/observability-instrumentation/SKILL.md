---
name: observability-instrumentation
description: Instrument Python and TypeScript services with OpenTelemetry traces, metrics and structured logs (including trace context through Kafka headers and GenAI spans for agents), and test the telemetry. Use when adding a service, endpoint, consumer, agent or alert, or when debugging production behaviour.
---

# Observability instrumentation

## Three signals, one context
- **Traces:** OpenTelemetry SDK, with auto-instrumentation where it exists (FastAPI, httpx, Node HTTP). Add manual spans for domain steps.
- **Metrics:** RED for every API and consumer (rate, errors, duration histogram). Plus consumer lag, queue depth, DLQ count, and for LLMs: latency, tokens in and out, cost, retries, fallbacks.
- **Logs:** structured JSON (structlog in Python, pino in TypeScript). Every line carries `trace_id`, `span_id`, `service` and the event id. No PII and no secrets.

## Propagation across Kafka
- The producer injects W3C `traceparent` and `tracestate` into the message headers.
- The consumer extracts them, and starts a span linked to (or a child of) the producer span.
- Name spans `<topic> publish` and `<topic> process`, following the OTel messaging semantic conventions.

## Agents
Follow the OTel GenAI semantic conventions: a span per LLM call (model, tokens, finish reason) and per tool call (tool name, outcome). Export to Langfuse as well as Grafana Tempo.

## Configuration
Use standard env vars only: `OTEL_SERVICE_NAME`, `OTEL_EXPORTER_OTLP_ENDPOINT`, `OTEL_RESOURCE_ATTRIBUTES`. Locally, export to the `grafana/otel-lgtm` container.

## Testing telemetry (TDD applies)
- Use an in-memory span exporter and metric reader in tests.
- Assert: span names, parent/child links across the Kafka boundary, key attributes, error status on failure, and metric increments.
- Assert log lines include the trace ids.

## Alerts and SLOs
- An SLO per user-facing flow (for example 99% of events enriched in under 5 s).
- Alert on burn rate, not raw thresholds. Every alert links to a runbook in `docs/runbooks/`.
- Dashboards and alert rules are code in `infra/observability/`.
