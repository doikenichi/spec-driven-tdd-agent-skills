---
name: spec-driven-change
description: Plan any feature, fix or behaviour change spec-first with OpenSpec — proposal, spec delta with Given/When/Then scenarios, design notes and a task list — before writing tests or code. Use when starting a change, when asked "what should we build", or when a request has no written acceptance criteria.
---

# Spec-driven change

The spec is the source of truth. Code and tests are derived from it and verified against it.

## When to use
- Before implementing any new behaviour, endpoint, event, UI change or agent capability.
- When a request is vague: turn it into a spec delta first, then confirm it.

## Workflow (OpenSpec layout)
1. **Check the current truth:** read `openspec/specs/<capability>/spec.md` for the affected capability. If the repo has no `openspec/` folder, tell the user to run `openspec init` (see https://github.com/Fission-AI/OpenSpec) and don't invent the layout.
2. **Create the change** in `openspec/changes/<change-id>/`, using a short kebab-case verb phrase as the id (`add-event-ingest-endpoint`):
   - `proposal.md`: **Why** (1–3 sentences), **What changes** (bullets), **Impact** (affected specs, services, contracts)
   - `specs/<capability>/spec.md`: the delta, using `## ADDED Requirements`, `## MODIFIED Requirements` or `## REMOVED Requirements`
   - `design.md` (only if it's non-trivial): options, the decision, a diagram (see `uml-and-c4-diagrams`), risks
   - `tasks.md`: a numbered checklist, test tasks first
3. **Write requirements and scenarios:**
   ```markdown
   ### Requirement: Accept valid events
   The ingest API SHALL accept a valid event and publish it exactly once.

   #### Scenario: valid event is accepted
   - **GIVEN** a well-formed event with a new idempotency key
   - **WHEN** a client POSTs it to /events
   - **THEN** the API responds 202 with the event id
   - **AND** one message is published to the `events.raw` topic
   ```
   Use SHALL/MUST for normative statements. Every requirement needs at least one scenario, and every scenario must be testable.
4. **Keep it small:** one change should take 10–20 minutes to implement. Split larger changes.
5. **Validate:** run `openspec validate <change-id> --strict` if the CLI is available.
6. **Hand off to TDD:** each scenario becomes at least one test (see `tdd-red-green-refactor`). Name the tests after the scenarios.
7. **Archive after merge:** run `openspec archive <change-id>`, so the delta merges into `openspec/specs/`.

## Quality bar
- No implementation details in requirements: describe behaviour, not code.
- Cover the error cases: invalid input, duplicates, timeouts, downstream failures.
- Contracts (OpenAPI, AsyncAPI) change in the same change as the spec (see `api-and-event-contracts`).
