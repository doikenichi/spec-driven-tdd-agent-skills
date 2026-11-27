---
name: tdd-red-green-refactor
description: Implement code test-first with the red-green-refactor cycle in Python (pytest, pytest-asyncio) or TypeScript (vitest). Use whenever writing or changing production code, fixing a bug, or turning spec scenarios into working behaviour.
---

# TDD: red → green → refactor

## Rules
1. **No production code without a failing test that demands it.**
2. **Red:** write one small test from one spec scenario. Run it and confirm it fails for the *right reason* (an assertion, not an import error).
3. **Green:** write the simplest code that makes it pass. Hard-coding is fine if the next test will force the generalisation.
4. **Refactor:** with all tests green, improve names, remove duplication and apply a pattern if one emerges. Run the tests again.
5. **Commit** at every green step for meaningful progress: `day-NNN: <behaviour>`.
6. Repeat for the next scenario. Edge cases and error paths get their own tests.

## Test design
- Name tests after behaviour and scenario: `test_valid_event_is_accepted_and_published_once`.
- Arrange / Act / Assert, one behaviour per test.
- Test through public interfaces (ports), not private details.
- Use fakes for ports (in-memory repositories, a fake producer, a scripted LLM). Mocks only at the edges.
- Async Python: `pytest-asyncio`; never sleep to wait, await events or use timeouts (`asyncio.timeout`).
- TypeScript: vitest with fake timers where needed; pass an `AbortSignal` so cancellation is testable.
- Property-based tests (Hypothesis, fast-check) for parsers, validators and serializers.

## Choosing the test level
| Level | Use for | Tools |
|---|---|---|
| Unit | domain logic, adapters with fakes | pytest, vitest, Moto |
| Contract | event schemas, OpenAPI/AsyncAPI | schema validation, consumer-driven contracts |
| Integration | real Kafka, AWS emulators, databases | Testcontainers, AWS Emulator, Floci |
| E2E | user journeys across the stack | Playwright |

Start at the lowest level that can prove the scenario. Add a higher-level test only for wiring that lower levels can't cover.

## When helping a learner
Don't write the green step for them unless asked. Offer the next failing test, or a hint, instead.
