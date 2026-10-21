## What it does

`grill-with-docs` interviews you about a plan or design until you and the [agent](https://www.aihero.dev/ai-coding-dictionary/agent) share one understanding of it, keeps domain vocabulary in your glossary, and records actual decisions through the project's installed official OpenSpec workflow. It is the same interview [grill-me](https://aihero.dev/skills-grill-me) runs (a round of questions, then wait, then the next round), pointed at a codebase.

It is **[stateful](https://www.aihero.dev/ai-coding-dictionary/stateful)**. Every other grilling skill leaves the [session](https://www.aihero.dev/ai-coding-dictionary/session) in your head; this one leaves project records. When a term resolves, the skill writes it to `GLOSSARY.md` at once, not in a batch at the end. When human input settles or changes a decision, the project's installed official OpenSpec workflow records it with its actual confirmation source and scope. The skill does not invent a parallel format or location. If OpenSpec is unavailable, decision recording remains incomplete until its prerequisite is met.

## When to reach for it

You invoke this by typing `/grill-with-docs`, and the agent won't reach for it on its own.

Reach for it at the start of a change, in a repo, when the plan is still fuzzy and the words for the thing are not settled yet. It is the single-session tool. Which grilling skill you want depends on what is in front of you:

| What you have | Reach for |
| --- | --- |
| You aren't working in a working directory at all | [grill-me](https://aihero.dev/skills-grill-me) |
| A repo, and a change you can settle in one session | `grill-with-docs` |
| An effort too big to hold in one session (a greenfield build, a large feature) | [wayfinder](https://aihero.dev/skills-wayfinder) |
| A repo with no domain docs at all, and no particular feature in mind | `grill-with-docs`, aimed at the repo rather than a change |
| A decision blocked on knowledge in someone else's head | [to-questionnaire](https://aihero.dev/skills-to-questionnaire) |

The wayfinder split comes down to session count: `/grill-with-docs` for single-session planning, `/wayfinder` for multi-session planning.

## Prerequisites

The skill writes into your repo, so you need to be somewhere it is safe to write. Resolved terms go to a `GLOSSARY.md` glossary at the root, or to the relevant context's `GLOSSARY.md`, if a `GLOSSARY-MAP.md` at the root marks the repo as multi-context. Decision records use the target project's installed official OpenSpec workflow, its existing configuration, and its required setup. OpenSpec owns the format, location, change selection and lifecycle. If the workflow is not installed or the project needs initialization, the skill reports that prerequisite and leaves decision recording incomplete.

It also needs two other skills present, because its own `SKILL.md` is one line that delegates to them. [grilling](https://aihero.dev/skills-grilling) supplies the interview, and [domain-modeling](https://aihero.dev/skills-domain-modeling) supplies the writing. Installing `grill-with-docs` alone gets you a skill that does not work.

## The paper trail

Terms and decisions have separate destinations.

| What resolved | Where it lands |
| --- | --- |
| A term: the project's own word for a thing | `GLOSSARY.md`, inline, the moment it resolves |
| An actual decision settled or changed by human input | The project's installed official OpenSpec workflow, using its configured artifacts |

`GLOSSARY.md` remains only a glossary. It holds no implementation details, specifications, or scratch notes. OpenSpec maintains its specifications and decision artifacts through its own official workflow. The issue-tracker brief produced by [to-spec](https://aihero.dev/skills-to-spec) captures the build context and can be discarded after the work ships; it does not replace or expire the project's OpenSpec records.

The glossary is the main output. This skill builds domain language: the project's own words, agreed once, so you, the agent and your colleagues do not have to work them out again. Not everyone agrees that this improves agent performance. The strongest objection is that a term and its plain-English expansion get the same result from the [model](https://www.aihero.dev/ai-coding-dictionary/model), and that the vocabulary mainly shortens communication between the humans who share it. On that view the glossary is still valuable, but the value goes to the humans.

## Common questions

**Should I use this or `/wayfinder`?**
Scope decides it. Use this for anything you can settle in one session; use [wayfinder](https://aihero.dev/skills-wayfinder) when the effort is too big to hold in one, and it charts the work as a map of decision [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) first. Wayfinder is slower and denser, and reaching for it on a well-scoped feature is the common mistake. It does not replace this skill, and it can start a grilling session for the parts of the map that suit one.

**It ran, but no glossary or decision record appeared.**
Resolved terms are written to `GLOSSARY.md`; actual decisions are recorded through the project's installed official OpenSpec workflow. If OpenSpec is unavailable or the project needs initialization, the skill reports that prerequisite and decision recording remains incomplete. When the workflow is available, check its result before treating the decision as recorded.

**It asked everything at once, with no recommendations, and never mentioned `GLOSSARY.md`.**
That is the skill failing to load its two dependencies. Because `SKILL.md` is a one-line delegation, an agent that does not pick up [grilling](https://aihero.dev/skills-grilling) and [domain-modeling](https://aihero.dev/skills-domain-modeling) guesses at what grilling means, and you get every question at once with no structure. Partial loading is more confusing. `grilling` loads, `domain-modeling` does not, and you get a good interview with no paper trail. How often it happens depends on the model and the [effort](https://www.aihero.dev/ai-coding-dictionary/effort) level, and it is the most reported problem with this skill. If you suspect it, ask the agent directly which skills it loaded.

**Does the issue-tracker spec replace the project's OpenSpec specifications?**
No. The issue-tracker brief from [to-spec](https://aihero.dev/skills-to-spec) preserves the build context for implementation and is disposable after the work ships. The project's official OpenSpec workflow continues to own its specifications and confirmed decision artifacts. Keep the two roles distinct when handing work to another session.

**Can I point it at an existing repo that has no docs at all?**
Yes. This is the right skill for a codebase with no decision records, no domain language and no design principles: invoke it and say "help me document my repo". Users often pair it with [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) for building or repairing a `GLOSSARY.md`. Expect to steer it. It reads code and asks you about what it finds, and you decide which of the words already in the codebase are the right ones.

**What should I do when the session ends?**
The skill's closing message is often open-ended, which is a known problem. In the main flow the answer is [to-spec](https://aihero.dev/skills-to-spec), in the same conversation. If the change is small enough to build immediately, go straight to [implement](https://aihero.dev/skills-implement) instead.

**Why is it called that?**
Nobody is happy with the name. There is an open suggestion to rename it `grill-domain-model`, which describes the behaviour more accurately. Nothing has moved on it. If a rename ever lands, the docs page moves with it and the URL changes.

## It's working if

- `GLOSSARY.md` changes *during* the session, term by term, rather than appearing in one lump at the end.
- The glossary reads as pure vocabulary (your project's words with tight definitions) and contains no implementation detail or spec-like prose.
- Questions the codebase can answer get answered by reading the codebase, not asked of you.
- Each actual decision is recorded through the installed OpenSpec workflow with its confirmation source and scope clear.
- It challenges a word you used because your existing glossary defines it differently.

## Where it fits

`grill-with-docs` is the head of the main build chain:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

It comes before the issue-tracker brief is written. It produces shared understanding and settled vocabulary that [to-spec](https://aihero.dev/skills-to-spec) then synthesises without interviewing you again; the issue brief is disposable after delivery, while the project's OpenSpec specifications and decision records continue through their official lifecycle. Its close neighbours are [grill-me](https://aihero.dev/skills-grill-me), the same interview with no repo and no files, and [domain-modeling](https://aihero.dev/skills-domain-modeling), the glossary and OpenSpec discipline it drives; both use the [grilling](https://aihero.dev/skills-grilling) primitive for the interview. Upstream of it, [wayfinder](https://aihero.dev/skills-wayfinder) charts efforts too large for one session and can hand parts of the map back down to it. When you're unsure which skill or flow fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
