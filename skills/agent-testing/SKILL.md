---
name: agent-testing
description: Test LLM-based AI agents at every layer — tool unit tests, scripted-LLM loop tests, record/replay, structured-output validation, trajectory assertions, threshold-based evals, red-team and prompt-injection tests, and cost/latency budgets. Use when building, changing or reviewing any agent, tool, prompt or MCP server.
---

# Testing AI agents

Agents are non-deterministic, so test the deterministic parts exactly, and the model-dependent parts statistically.

## The pyramid (fast and cheap → slow and realistic)
1. **Tools:** each tool is a typed function, unit-tested with no LLM.
2. **Agent loop with a scripted LLM:** a fake provider returns scripted turns (tool call → result → answer). Assert retries, tool-error handling, max-steps limit, timeouts and fallbacks.
3. **Record/replay:** real responses are recorded once to fixtures (`tests/fixtures/llm/`) and replayed in CI through `RecordedProvider`. Re-record deliberately, never automatically.
4. **Structured output:** validate every agent output against a Pydantic or zod schema. Property-based tests for parsers.
5. **Trajectories:** assert *how* the answer was reached: which tools, in what order, with what arguments, in how many steps.
   ```python
   assert result.tool_calls == ["lookup_customer", "classify_event"]
   assert result.steps <= 4
   ```
6. **Evals:** a golden dataset (`evals/datasets/*.jsonl`) with scorers. Rule-based scorers first, then LLM-as-judge with an explicit rubric. Assert a pass-rate threshold over N runs (for example ≥ 0.9 over 5), not exact text.
7. **Red-team and safety:** prompt injection hidden in event payloads, data-exfiltration attempts, tool misuse, jailbreaks. The guardrails must block them, and the tests prove it.
8. **Budgets:** tokens, cost and latency per event. Exceeding the budget fails the test.
9. **MCP servers:** contract tests for the tool list, input schemas, error shapes and timeouts.
10. **Telemetry:** in-memory OpenTelemetry exporter. Assert GenAI spans (model, tokens, tool calls) are emitted.

## In CI
- Every commit: layers 1–5 and 8–10, using recorded responses only. Free and deterministic.
- Every PR: a small eval suite with a baseline gate. Fail if the score drops beyond a tolerance.
- Nightly or manual: the full eval suite and red-team run against a live provider.

## Tools
pytest (core), promptfoo or DeepEval (evals and red-teaming), Langfuse (datasets, traces, scores). Check current versions before use.

## Rules
- Never put real secrets or personal data in fixtures.
- Keep prompts in versioned files. A prompt change is a code change, and triggers the evals.
- When a bug is found in production, add the failing case to the eval dataset first (TDD for agents).
