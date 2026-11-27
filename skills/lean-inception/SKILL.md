---
name: lean-inception
description: Plan and evolve a product with Paulo Caroli's Lean Inception — product vision, is/is not/does/does not, goals, personas, user journeys, feature brainstorming, technical/UX/business review (effort, value, uncertainty), the Feature Sequencer with its wave rules, and the MVP Canvas — and keep these artifacts as the source of the roadmap. Use when deciding what to build next, adding or changing a feature, planning an MVP or increment, or checking that work still serves the product vision.
---

# Lean Inception

Lean Inception (Paulo Caroli) aligns people on **what to build first**, the MVP, and in which order. The artifacts live in the repo (for example `docs/product/lean-inception.md`) and **drive the roadmap**. Technical milestones exist to deliver features, not the other way round.

## Artifacts, in workshop order
1. **Product vision:** the elevator pitch. *For* [target customer] *who* [need], the [product] *is a* [category] *that* [key benefit]. *Unlike* [alternative], *our product* [differentiator].
2. **Is / Is not / Does / Does not:** four short lists that set the boundaries. They're especially useful for constraints such as privacy.
3. **Product goals:** 3–5 outcomes the product must achieve, with a measurable signal for each.
4. **Personas:** a name, a profile, behaviours, needs and pains. Map which goals matter to which persona.
5. **User journeys:** step-by-step scenarios for each persona, in context (time, place, trigger).
6. **Feature brainstorming:** features as short cards, each linked to a goal and a persona.
7. **Technical, UX and business review:** rate each feature for **effort** (E, 1–3), **business value** ($, 1–3) and **UX value** (♥, 1–3). Rate **uncertainty** from technical understanding × business/usage understanding: green (low), yellow (medium), red (high).
8. **Features in journeys:** place the cards on the journey steps, which shows the features each journey needs.
9. **Feature Sequencer:** order the features into lines (waves), following the rules below.
10. **MVP Canvas:** for the first increment: proposal (what we want to learn), segmented personas, journeys, features, expected result, metrics to validate it, and cost and schedule.

## Feature Sequencer rules (per line or wave)
1. At most **3 features**.
2. At most **1 red** feature (high uncertainty).
3. At least **1 green** feature (low uncertainty).
4. Total effort **≤ 5 E**.
5. Total business value **≥ 4 $**.

The first lines that complete a useful journey form the **MVP**. Later lines are increments, and each one gets its own MVP Canvas when it starts.

## Feature card format (keep it in markdown)
```markdown
| ID | Feature | Goal | Persona | E | $ | ♥ | Uncertainty | Wave |
|---|---|---|---|---|---|---|---|---|
| F3 | Deduplicate detections across cameras | G1 fewer, trustworthy alerts | Operator | 2 | 3 | 1 | yellow | 2 |
```

## Using it day to day
- **Planning:** pick the next work from the **current wave**. Every challenge, or set of challenges, names the feature (F-id) it advances. Technical milestones on the roadmap are scheduled because a feature needs them.
- **Spec-first:** each feature becomes one or more OpenSpec changes. The journey step becomes the scenario context (see `spec-driven-change`).
- **Review the plan:** when a wave completes, update the MVP Canvas metrics (did we learn what we wanted?), re-rate uncertainty, and re-sequence if needed. Record big changes in an ADR.
- **Guard the boundaries:** reject or park features that contradict "Is not / Does not" (for example identifying people in a privacy-first product).
- **Explain to stakeholders:** the vision, the sequencer and the MVP Canvas are the one-page story for clients and interviews.
