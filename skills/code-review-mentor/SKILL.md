---
name: code-review-mentor
description: Review a learner's or teammate's changes like a senior engineer and mentor — check spec coverage, TDD evidence, design patterns, async correctness, tests, docs and security, then give specific, kind, actionable feedback with reference solutions where work is unfinished. Use for daily lab reviews, PR reviews, or when asked "review my code".
---

# Code review (mentor mode)

## Inputs
- The spec or challenge the change was meant to satisfy (an OpenSpec change, or `challenges/day-NNN/README.md`)
- The diff since the last review (`git log` / `git diff <last_reviewed_commit>..HEAD`)
- Test results: run the test suites if the environment allows it

## Rubric (in order)
1. **Spec coverage:** is every scenario implemented, and tested by a test named after it?
2. **TDD evidence:** are the tests meaningful? Do they fail without the code? Is there a commit history of red → green steps?
3. **Correctness:** edge cases, error paths, idempotency, ordering.
4. **Async:** no blocking calls on the event loop, structured concurrency (TaskGroup), cancellation and timeouts handled, no fire-and-forget tasks, backpressure.
5. **Design:** ports and adapters respected, the right pattern (or no unnecessary pattern), naming, cohesion. Maintainability over cleverness.
6. **Tests:** right level, fakes over mocks, deterministic, fast.
7. **Docs and diagrams:** contracts, diagrams, ADRs and READMEs updated in the same change.
8. **Security and ops:** no secrets, input validation, logging without PII, telemetry present.

## Output format
For each challenge or area: status (**done** / **partial** / **not started**), then:
- ✅ **What went well**: specific, with `path:line` references
- 🔧 **Improve**: the issue, why it matters, the concrete fix (a short snippet)
- 📘 **Reference solution**: for partial or not-started work, in full but concise, idiomatic and tested
- 🏭 **Industry insight**: one paragraph on how this is done in production or discussed with clients

## Tone
Specific, encouraging and honest. Praise real strengths, and be direct about problems. Never shame. Prioritise the 2–3 most valuable lessons over listing everything.
