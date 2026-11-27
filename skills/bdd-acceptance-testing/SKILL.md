---
name: bdd-acceptance-testing
description: Write and run BDD functional and regression tests at the end-to-end level with Gherkin feature files — turning spec Given/When/Then scenarios into executable features, declarative business-language steps, playwright-bdd for web e2e, pytest-bdd for API and event-flow e2e, tags for smoke and regression suites, and feature files as living documentation. Use when adding or changing user-visible behaviour, writing an e2e or regression test, or when a spec scenario needs an acceptance check.
---

# BDD acceptance testing (e2e, functional and regression)

BDD is used **at the e2e level** for functional and regression testing. Unit, contract and integration tests stay plain pytest/vitest (see `tdd-red-green-refactor`); don't write Gherkin for a pure function.

## 1. From spec to feature file
- Every OpenSpec scenario that describes **user-visible or cross-service behaviour** gets a Gherkin scenario with the **same name**, in a `.feature` file named after the capability (`features/incident-alerts.feature`).
- The spec stays the source of truth; the feature file is its executable copy. If they disagree, fix the spec first, then the feature.
- One feature per file, one behaviour per scenario. Use `Background` only for setup every scenario shares, `Scenario Outline` + `Examples` for the same rule with different data, and `Rule` to group scenarios by business rule.

## 2. Write declarative steps
- Write steps in the **domain's language** (what), never UI mechanics (how):
  - Good: `When a person is detected by two cameras within 5 seconds` / `Then Maya receives one alert`
  - Bad: `When I click "#btn-submit"` / `Then the div ".alert" has 1 child`
- Given = state, When = one action or event, Then = an observable outcome. One `When` per scenario.
- Keep scenarios under ~7 steps. Steps are reusable sentences, so keep a small, consistent vocabulary (a glossary in `docs/architecture/` helps).
- Hide mechanics in step definitions, which call page objects (web), API clients (HTTP) or test producers/consumers (Kafka). Locators use roles and accessible names (`getByRole`), which also checks accessibility.

## 3. Tools
| Layer | Tool | Notes |
| --- | --- | --- |
| Web e2e (TypeScript) | **playwright-bdd** (MIT) | converts features into native Playwright tests: fixtures, parallelism, traces, HTML report and retries all work. Run `bddgen` then `playwright test`. Prefer it over a separate Cucumber.js runner. |
| API and event-flow e2e (Python) | **pytest-bdd** (MIT) | runs inside pytest, so fixtures, Testcontainers, markers and pytest-asyncio work. Use `target_fixture` instead of a shared context object. |
| Alternative | Cucumber.js / behave | only if a project already uses them |

Check the current versions before adding them, and pin them.

## 4. Suites and tags
- `@smoke`: a few critical journeys, run on every PR and after deploys (also against preview URLs).
- `@regression`: the full functional suite, run on main and nightly; every fixed bug adds a scenario tagged with its issue (`@bug-123`) before the fix.
- `@wip` scenarios are excluded in CI; `@slow`, `@chaos` mark scenarios needing fault injection.
- Map tags to journeys and features (`@J1`, `@F4`) so reports show which feature a failure affects.

## 5. Event-driven and async scenarios
- Eventual consistency: `Then` steps poll with an SLO timeout (never sleep), for example "within 3 seconds".
- Failure modes are scenarios too: duplicate, delayed, out-of-order and poison events (see `distributed-event-testing`), injected through the simulator or a fault proxy in `Given`/`When` steps.
- Propagate a correlation id per scenario and attach traces and logs to the report on failure.

## 6. Environments and data
- Run against a disposable stack (compose or Testcontainers), seeded per scenario; scenarios must be independent and runnable in any order and in parallel.
- No real people's data and no real footage in fixtures (privacy by design).
- LLM steps use the recorded provider, so scenarios are deterministic.

## 7. Living documentation and CI
- Publish the HTML or Cucumber JSON/messages report as a CI artifact; the feature files are the readable behaviour docs.
- Flaky scenarios are quarantined with a tag and an issue, never silently retried forever.
- In TDD: write the failing scenario first (outer loop), then drive the code with unit tests (inner loop) until it passes.

## Review checklist
- [ ] Each user-visible spec scenario has a matching Gherkin scenario
- [ ] Steps are declarative and in domain language; no selectors or URLs in feature files
- [ ] One `When` per scenario; outcomes are observable
- [ ] Async outcomes poll with a timeout; no sleeps
- [ ] Tags for smoke/regression and feature/journey ids
- [ ] Independent, parallel-safe scenarios on a disposable environment
