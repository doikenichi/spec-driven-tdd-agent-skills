---
name: human-centered-ux
description: Design and review user interfaces, alerts and interactions with Don Norman's human-centered design principles from The Design of Everyday Things — discoverability, affordances, signifiers, constraints, mapping, feedback, conceptual models, the gulfs of execution and evaluation, and designing for error (slips and mistakes) — together with accessibility. Use for any UI, dashboard, notification, form, CLI output or API error message, and when reviewing UX.
---

# Human-centered UX (Don Norman)

Two qualities matter most: **discoverability** (can people work out what actions are possible, and how?) and **understanding** (do they know what it all means?). The principles below serve those two. When people struggle, the design is at fault, not the user.

## The principles, and how to apply them
| Principle | Ask | In practice |
|---|---|---|
| **Affordances** | What actions does this element allow? | Controls that act are real buttons and links (semantic HTML), not styled divs |
| **Signifiers** | How do people *perceive* what to do? | Clear labels, icons *with* text, "Acknowledge" rather than an ambiguous ✓; status in words, not colour alone |
| **Constraints** | What prevents wrong actions? | Disable actions that aren't allowed in the current state; typed inputs; validation before submit; privacy constraints built in (no identity data to show at all) |
| **Mapping** | Do controls and information match the real world? | Order cameras by physical layout or zones; time flows left → right or top → bottom; group controls with what they affect |
| **Feedback** | Does every action get an immediate, informative response? | Instant visual acknowledgement (under about 100 ms), progress for longer work, and a live-connection indicator; no silent failures |
| **Conceptual model** | Can people build a correct mental model? | One consistent vocabulary from the glossary (detection → incident → review), with the system's state visible |

## Gulfs of execution and evaluation
- **Execution** ("how do I do what I want?"): make the next action obvious, with few steps and sensible defaults.
- **Evaluation** ("did it work, and what state am I in?"): show the result and the new state clearly.

Walk through Norman's **seven stages of action** for each key journey: goal → plan → specify → perform → perceive → interpret → compare. Find where users would get stuck.

## Designing for error
- **Slips** (the right goal, done wrong: a mis-click) are best handled with **undo** rather than confirmation dialogs. Keep destructive actions away from frequent ones.
- **Mistakes** (the wrong goal, from a wrong mental model) are best handled by a clearer conceptual model and better feedback. Explain *why* the system did something (for example "severity: alert, because a person appeared at night and the door opened").
- Error messages say what happened, why, and what to do next, in plain language without blame. APIs return RFC 9457 problem details with an actionable `detail`.

## Alerts and notifications (alarm fatigue is a UX failure)
Fewer, explained, actionable alerts beat many raw ones. Every alert shows what happened, where, when, how sure the system is, why it matters (the severity reason) and what to do. People can tell the system "this was a false alarm", and the system learns from it.

## Accessibility is part of the design
WCAG 2.2 AA: query by role in tests (Testing Library), axe-core checks in CI, keyboard operability, visible focus, sufficient contrast, and no information carried by colour alone (see `performance-and-nfr-testing`).

## UX checklist for a PR
- [ ] Discoverable: the available actions are visible and labelled
- [ ] Signifiers: text plus icon; state never shown by colour alone
- [ ] Constraints: invalid actions are prevented, not just reported
- [ ] Mapping: layout matches the real world and time order
- [ ] Feedback: immediate response, visible state and connection status
- [ ] Conceptual model: glossary terms used consistently
- [ ] Errors: undo for slips; explanations prevent mistakes; messages are actionable
- [ ] Accessibility: axe clean, keyboard and focus, role-based tests
