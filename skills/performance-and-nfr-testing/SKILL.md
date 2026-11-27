---
name: performance-and-nfr-testing
description: Design, run and gate non-functional tests — load, stress, spike, soak and breakpoint tests with k6 (or Locust for Python/LLM paths), SLO-based thresholds, profiling, resilience and fault injection (Toxiproxy, Chaos Mesh), web performance budgets (Lighthouse CI) and accessibility (axe-core) — and analyse results with AI. Use when adding or changing an endpoint, consumer, query, agent, page or infrastructure that could affect latency, throughput, reliability, cost or accessibility.
---

# Performance and non-functional testing

Non-functional requirements are requirements. Write them as spec scenarios with numbers, test them automatically, and gate CI on them.

## 1. Start from a quality scenario (spec-first)
Add measurable NFRs to the OpenSpec change and to arc42 §10:
```markdown
#### Scenario: ingest stays fast under expected load
- **GIVEN** 200 requests per second of valid events for 5 minutes
- **WHEN** clients POST to /events
- **THEN** p95 latency is below 150 ms and p99 below 400 ms
- **AND** the error rate is below 0.1%, and consumer lag recovers within 30 s
```
Derive the numbers from SLOs (see `observability-instrumentation`). Without a number, it isn't testable.

## 2. Test types (one script, different profiles)
| Type | Question | Shape |
|---|---|---|
| Smoke | Does it work under minimal load? | 1–2 VUs, 30 s; runs on every PR |
| Average load | Does it meet SLOs at normal traffic? | ramp to the expected rate, hold |
| Stress | How does it behave above normal? | 1.5–2× expected |
| Spike | Does it survive sudden bursts? | jump to N× for a short time, then drop |
| Soak | Do memory, connections or lag drift over time? | expected load for 30–60 min or more |
| Breakpoint | Where does it break, and how? | ramp until the thresholds fail |

Prefer **open models** (`constant-arrival-rate`, `ramping-arrival-rate`) for APIs, so a slow system doesn't silently reduce the load.

## 3. Tools (free, industry standard)
- **k6** (Grafana, open source) is the default. Scripts are TypeScript in `tests/perf/`, using thresholds as pass/fail, scenarios and checks. Generate a starting script from OpenAPI where it helps.
- **Locust** (Python) for Python-heavy or LLM-agent paths, and when custom Python clients are easier.
- **Kafka throughput:** `kafka-producer-perf-test` and `kafka-consumer-perf-test`, or a k6 Kafka extension. Measure end-to-end latency and consumer lag, not just produce rate.
- **Profiling:** Pyroscope (continuous profiling in Grafana), py-spy (Python), `node --cpu-prof` (Node.js).
- **Resilience:** Toxiproxy (latency, timeouts, dropped connections between services), Chaos Mesh or LitmusChaos on kind (pod kill, network faults).
- **Web:** Lighthouse CI with performance budgets (LCP, INP, CLS) on preview deployments.
- **Accessibility:** axe-core with Playwright (`@axe-core/playwright`), WCAG 2.2 AA.

Check current versions and APIs before using any of them.

## 4. Running safely
- Run load, stress, spike, soak and breakpoint tests **only against local or disposable environments** (Compose, kind, the CI environment).
- **Never** stress free-tier hosts (Vercel, Aiven, Render, Koyeb, Oracle). Against them, run smoke tests and synthetic checks only, within their fair-use terms.
- Use realistic data, and the recorded LLM provider unless the test is specifically about the LLM.
- Warm up first. Pin versions and the environment, so runs are comparable.

## 5. Observability and results
- Stream k6 metrics to Prometheus (remote write) and view them next to the service RED metrics, traces and profiles in Grafana.
- Keep a baseline (`tests/perf/baselines/*.json`). Compare each run with the baseline; a regression beyond the tolerance fails the build.
- Record capacity findings and trade-offs in an ADR (for example "ingest-api saturates at N rps due to X").

## 6. In CI (CircleCI)
- Every PR: smoke tests with thresholds, and the axe accessibility checks.
- Merges to main, or nightly: average load and spike tests against the disposable environment, compared with the baseline.
- Weekly or manual: soak, breakpoint and chaos runs.

## 7. AI on top
- **k6 MCP server** (`k6 x mcp` or `mcp-k6`, from Grafana) lets Claude Code or Codex validate and run scripts and browse the k6 docs. `k6 x agent` installs Grafana's k6 skills.
- **Perf-analyst agent:** reads the k6 summary, the baseline and Grafana data (through the Grafana MCP server), then writes a regression report: what changed, the likely cause (with trace and profile links) and a suggested fix. It posts the report on the PR.
- Test the agent with `agent-testing`: recorded result fixtures with known regressions, scored for correct root cause.

## Checklist
- [ ] The NFR is written as a scenario with numbers
- [ ] The script has thresholds that match the scenario
- [ ] The baseline is updated intentionally, with the reason in the PR
- [ ] Results and dashboards are linked in the PR
- [ ] No load against free-tier hosts
