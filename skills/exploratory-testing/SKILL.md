---
name: exploratory-testing
description: Plan, run and debrief exploratory testing sessions — charters, time-boxed sessions (session-based test management), James Whittaker's tours from Exploratory Software Testing (business, historical, entertainment, tourist, hotel and seedy districts), testing in the small (inputs, state, code paths, data, environment), scenario-based variation, session notes, debriefs, and turning findings into BDD regression scenarios. Use when a user-visible feature, API or event flow is ready to explore, when planning manual or AI-assisted exploration, or when reviewing a session.
---

# Exploratory testing

Exploratory testing is **simultaneous learning, test design and execution**. It finds what scripted tests can't: surprises, missing requirements and confusing behaviour. It complements automation; it never replaces the BDD regression suite (see `bdd-acceptance-testing`).

## 1. Charter (before)
Write one sentence, Elisabeth Hendrickson's template:
> Explore **<target>** with **<resources: tour, data, tool, fault>** to discover **<information: risks, questions>**.

Pick **one tour** (section 3) and **one persona** (from the product docs) per session. Time-box it: 15–20 minutes in the daily lab, 45–90 in real teams.

## 2. Session (during)
- Keep a running session sheet: what you tried, what you saw, questions, bugs, and ideas for new charters.
- Vary things (Whittaker's "testing in the small"): **inputs** (legal, illegal, boundary, combinations, sequences), **state** (do the same input in different states; get into a state and back out), **code paths**, **user data** (empty, huge, realistic, corrupt), and **environment** (slow network, offline, small screen, other browser, clock skew).
- Follow surprises: when something looks off, spend a few minutes on it, then note it and return to the charter.
- Use tools to see more: browser devtools, Playwright traces, the API docs UI, Kafka UI or `kcat`, Grafana traces and logs filtered by correlation id.

## 3. Whittaker's tours, adapted to services and event streams
| District | Tour | Idea | In an event-driven product |
| --- | --- | --- | --- |
| Business | Guidebook | follow the docs and onboarding exactly | the README and runbooks: does each step work as written? |
| | Money | the features customers pay for | the core journey (an alert reaching the user) |
| | Landmark | hop between key features in different orders | dashboard → incident → camera → back, in varied order |
| | Intellectual | ask the hardest questions | extreme or tricky inputs to the agent and the API |
| | FedEx | follow data from input to every output | trace one event through ingest → Kafka → enrich → store → UI by its correlation id |
| | After-Hours | what runs when nobody is looking | scheduled jobs, TTL deletion, nightly summaries, retries |
| | Garbage Collector's | visit everything systematically, briefly | every route, page, topic and error message |
| Historical | Bad-Neighborhood | where bugs clustered before | modules with recent bug fixes or flaky tests |
| | Museum | old, untouched code | legacy adapters, rarely changed config |
| | Prior Version | compare with the previous release | regression versus the last deploy or schema version |
| Entertainment | Supporting Actor | features next to the main one | filters, settings, empty states around the main view |
| | Back Alley | least-used features | admin pages, export, rarely used endpoints |
| | All-Nighter | keep it running a long time | long sessions, soak, reconnecting SSE streams |
| Tourist | Collector's | collect every output | every alert type, severity and error response |
| | Lonely Businessman | go to the farthest feature | deep links and nested views |
| | Supermodel | look only at the surface | layout, wording, signifiers, accessibility (Norman's principles) |
| | Scottish Pub / TOGOF | find hidden spots; one of each | hidden features; one instance of every object type |
| Hotel | Rained-Out | start and cancel operations | cancel midway, close the tab, kill a consumer mid-batch |
| | Couch Potato | do as little as possible | defaults, empty fields, minimum input |
| Seedy | Saboteur | deprive it of resources | stop Kafka or the database, slow the network (Toxiproxy) |
| | Antisocial | do the opposite of what's expected | invalid, malformed and poison events, wrong content types |
| | Obsessive-Compulsive | repeat the same thing | double clicks, duplicate events, replays (idempotency) |

Check tour names against the book; the mapping column is this skill's adaptation.

## 4. Hybrid: scenario-based exploration
Take an existing BDD or spec scenario and vary it on purpose: insert steps, remove steps, repeat steps, swap data, change the environment. Each variation that matters becomes a charter or a new scenario.

## 5. Debrief (after, about 5 minutes)
Answer PROOF (Session-Based Test Management, Jonathan and James Bach): **P**ast (what happened), **R**esults (what you found), **O**bstacles, **O**utlook (what's left), **F**eelings (how confident you are). Record:
- the time split: test design and execution / bug investigation / setup
- coverage: which tour, persona and areas
- **every bug** becomes an issue and, before the fix, a `@regression` BDD scenario
- new charters for next time

## 6. AI-assisted exploration
An agent (for example through Playwright MCP) can propose charters, run a Garbage Collector's sweep or generate input variations, but **a person runs the session and judges the results**. Log what the agent tried; treat its findings as leads to verify, not as bugs.

## Review checklist
- [ ] A one-sentence charter with a tour and a persona
- [ ] Time-boxed, with a session sheet
- [ ] Inputs, state, data and environment were varied
- [ ] Debrief recorded (PROOF, time split, coverage)
- [ ] Each bug has an issue and a regression scenario
- [ ] No real people's data or footage used
