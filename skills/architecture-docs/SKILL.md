---
name: architecture-docs
description: Write and maintain standard project documentation — arc42 architecture docs, ADRs in MADR format, Diátaxis-style READMEs, how-tos and runbooks. Use when a decision is made, a component is added, a README is out of date, or someone asks to "document" something.
---

# Architecture and project documentation

## Structure (Diátaxis)
| Type | Purpose | Location |
|---|---|---|
| Tutorial | learning by doing | `challenges/`, `docs/tutorials/` |
| How-to | solving a specific task | `docs/how-to/` |
| Reference | facts: APIs, config, events | `docs/contracts/`, service READMEs |
| Explanation | why things are the way they are | `docs/architecture/`, `docs/adr/` |

Don't mix types in one page.

## arc42 (`docs/architecture/README.md`)
Keep these sections short and current. Write "TBD" rather than invent content.
1. Introduction and goals: the top 3 quality goals
2. Constraints: cost (free tiers), tech, organizational
3. Context and scope: C4 System Context diagram
4. Solution strategy: key approaches (event-driven, ports and adapters, spec-driven, TDD)
5. Building block view: C4 Container and Component diagrams
6. Runtime view: sequence diagrams for the key flows
7. Deployment view: local compose, kind, free-tier hosts
8. Cross-cutting concepts: observability, security, error handling, testing
9. Architecture decisions: links to ADRs
10. Quality requirements: quality scenarios (latency, availability, cost)
11. Risks and technical debt
12. Glossary

## ADRs (MADR) in `docs/adr/NNNN-title.md`
Write an ADR when a choice is hard to reverse, has real alternatives, or affects more than one component. Sections: Status, Date, Context and problem, Considered options (pros and cons), Decision outcome, Consequences. Number them sequentially. Never edit an accepted ADR's decision; supersede it with a new ADR.

## READMEs (each service)
Cover: what it does (one paragraph), diagram, how to run, how to test, configuration (an env-var table), ports and adapters, links to specs and contracts.

## Runbooks (`docs/runbooks/<alert>.md`)
Cover: alert meaning, impact, first checks (queries and dashboards), mitigation, escalation, follow-up.

## Style
Plain language, short sentences, active voice. Prefer code blocks and tables over paragraphs. Docs change in the same PR as the code.
