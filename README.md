# spec-driven-tdd-agent-skills

Shared [Agent Skills](https://agentskills.io) for **Claude Code** and **OpenAI Codex** (and other compatible agents). They enforce one engineering workflow:

**spec → test → code → docs**: spec-driven development with OpenSpec, TDD, UML and C4 diagrams, standard documentation, contracts, agent testing and observability.

## Skills

| Skill | Use it for |
|---|---|
| [`spec-driven-change`](skills/spec-driven-change/SKILL.md) | OpenSpec proposal, spec delta with Given/When/Then scenarios, design, tasks |
| [`tdd-red-green-refactor`](skills/tdd-red-green-refactor/SKILL.md) | test-first implementation in Python and TypeScript |
| [`uml-and-c4-diagrams`](skills/uml-and-c4-diagrams/SKILL.md) | UML and C4 diagrams as Mermaid, rendered on GitHub |
| [`architecture-docs`](skills/architecture-docs/SKILL.md) | arc42, ADRs (MADR), Diátaxis docs, runbooks |
| [`api-and-event-contracts`](skills/api-and-event-contracts/SKILL.md) | OpenAPI 3.1, AsyncAPI 3, schema evolution, contract tests |
| [`agent-testing`](skills/agent-testing/SKILL.md) | testing AI agents: record/replay, trajectories, evals, red-teaming, budgets |
| [`observability-instrumentation`](skills/observability-instrumentation/SKILL.md) | OpenTelemetry traces, metrics and logs, Kafka propagation, telemetry tests |
| [`performance-and-nfr-testing`](skills/performance-and-nfr-testing/SKILL.md) | load, stress, spike, soak and breakpoint tests (k6, Locust), SLO thresholds, profiling, chaos and fault injection, Lighthouse CI, accessibility, AI perf analysis |
| [`security-hardening`](skills/security-hardening/SKILL.md) | repo rulesets, SAST (CodeQL, Semgrep), Dependabot, SBOM and signing, IaC and container scanning, DAST, STRIDE, OWASP Top 10 |
| [`code-review-mentor`](skills/code-review-mentor/SKILL.md) | senior-engineer review with a rubric and reference solutions |

## Install into a project

Using the [`skills` CLI](https://github.com/vercel-labs/skills), which installs to each agent's own folder (`.claude/skills/` for Claude Code, `.agents/skills/` for Codex):

```bash
# all skills, into Claude Code and Codex, project scope (commit the result)
npx skills add doikenichi/spec-driven-tdd-agent-skills --all -a claude-code -a codex

# a single skill
npx skills add doikenichi/spec-driven-tdd-agent-skills --skill tdd-red-green-refactor -a claude-code -a codex
```

Re-run the same command to update. Commit the installed copies, so CI, cloud agents and teammates all use the same versions.

**Manual install:** copy `skills/<name>/` into `.claude/skills/<name>/` and `.agents/skills/<name>/`.

## Invoking
- **Claude Code:** skills load automatically from their descriptions, or run `/spec-driven-change`.
- **Codex:** skills load automatically, or use `$spec-driven-change` / `/skills`.

## Writing a new skill
Each skill is a folder with a `SKILL.md`. The frontmatter has a `name`, matching the folder, in lowercase-hyphen form, and a `description` that says **what it does and when to use it**. Keep the body under about 150 lines. Put long references in extra files next to it.

Used by [real-time-ai-event-enrichment-platform](https://github.com/doikenichi/real-time-ai-event-enrichment-platform).
