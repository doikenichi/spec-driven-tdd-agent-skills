---
name: reading-companion
description: Turn technical books into practice — map chapters to the work at hand, assign just-in-time reading, write book references into challenges, ADRs and reviews, prompt active recall and reflection, and connect concepts to interview answers, without reproducing the books' text. Use when planning a lab or challenge, writing an ADR or review, preparing interview answers, or when someone asks "what should I read for this?".
---

# Reading companion

Books give the *why* and the trade-offs. The lab gives the *how*. Connect them every time: **read → apply → explain**.

## Core loop (per concept)
1. **Read just in time:** assign the chapter or section right before the challenge that uses it, never months ahead.
2. **Apply:** the challenge implements the concept (a test, a design choice, a metric). Its "Book reference" line names the chapter and the concept.
3. **Explain:** the learner writes a short note in their own words (`docs/reading-notes/<book>.md`): the key idea, how it showed up in the platform (with a link to the code, test or ADR), and one question they still have.
4. **Recall:** a later drill or review asks about the concept *without* the book open (spaced repetition: about 2 days, 1 week and 1 month later).
5. **Interview:** connect it to an answer in `docs/interview/` ("as Kleppmann describes…, so in my platform I…").

## Writing a book reference
- Format: `Book reference: <Short title>, ch. N, "<chapter title>" — <the concept, in your own words>`.
- Cite the chapter, and the section when known. Check the chapter numbers and titles against the official table of contents (publisher page, the author's site, or a free online edition) before writing them. Never guess a number.
- **Never reproduce passages.** Paraphrase in your own words. If a quote is essential, keep it under 15 words, with at most one per book per document.
- When a book and current practice differ (books age), say so, and follow the current primary source. Example: a 2022 book may predate a 2026 tool or API.

## Choosing what to read
- Match the challenge's concept to the reading map in `docs/READING.md`. If it's missing, find the best chapter and add it to the map.
- Prefer one focused section (20–30 minutes) over a whole chapter when time is short. Name the section.
- Rotate across the books, so all of them stay in use.

## Reflection prompts (pick one per assignment)
- What trade-off does this chapter describe, and which side did we choose in the platform? Why?
- What would break in our system if we ignored this idea? Can we write a test for it?
- Where does the book's advice not fit our constraints (free tiers, small scale)? What did we do instead?
- How would you explain this concept to a client in 2 minutes? To a QA lead? To a developer?

## In reviews
Check the reading note exists and is in the learner's own words. Correct any misunderstanding gently, and link it back to the code. Suggest one follow-up section if the concept was shaky.
