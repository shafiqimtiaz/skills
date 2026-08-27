---
name: any-question
description: Use when a user's request may be ambiguous, underspecified, internally inconsistent, or open to materially different interpretations in the current project—especially when they ask "any questions?", "is this clear?", "before you start", "what do you need from me?", "do you understand what I mean?", or "flag gaps", or say "don't assume", "not sure which", or "clear enough". Do not use for brainstorming, planning, implementation, explanation, or review when the requested outcome is already clear.
---

# Any Question

Resolve ambiguity in a request before acting on it. Inspect the project for facts, then ask the user only the questions that change the requested outcome.

This skill does not plan, design, implement, review, estimate, or improve the requested work. Its single output is a corrected understanding of what the user wants.

## The failure mode to avoid

Being invoked is not evidence that questions are needed.

The strongest pull on you right now is to justify this skill by producing questions. Resist it. A confident "I read this as *X*. Proceeding." is a successful outcome and often the correct one. Every unnecessary question costs the user a turn and buries the real ambiguity in a list they'll skim.

## Process

### 1. Take the user's words as settled

Read the request and extract what is already decided: the outcome, explicit instructions, named constraints, stated non-goals, references to existing behavior.

Anything the user stated is fixed. Re-confirming it ("so you want X, correct?") reads as not having listened, and it trains the user to skip past your questions — including the one that mattered.

### 2. Answer what you can from the project

Before asking anything, look. Read the relevant files, tests, config, adjacent implementations, and naming conventions.

Most apparent ambiguity is just missing context on your side. "Which auth mechanism does this use?" is not a question for the user — it's a question for the codebase. Asking it anyway tells the user you didn't look, and now they're doing your reading for you.

Project evidence establishes what exists. It does not override an explicit instruction. If the user asked for something the project contradicts, that conflict is a candidate question (step 3), not a reason to quietly follow the project instead.

### 3. Test each remaining uncertainty

For every gap that survives steps 1 and 2, apply one test:

> Would two reasonable readings of this lead me to build materially different things?

**No** → proceed on the more likely reading. State the assumption in your response instead of spending a question on it.

**Yes** → ask.

*Materially different* means: different files touched, different behavior shipped, different scope, or work the user would make you redo.

## What earns a question

- Which of several plausible behaviors the user wants
- What is in and out of scope
- Which existing behavior must survive the change
- Whether a conflict with the current project is intentional
- What an ambiguous term means *in this request* — the project may define it differently than the user does
- Which of several existing things the user is pointing at

## What does not

- Anything the user already stated
- Anything the files answer
- Implementation details that don't change the outcome — library choice, naming, file layout, ordering
- Style preferences irrelevant to the result
- Hypotheticals with no evidence they apply here
- Decisions the user already made, even ones you'd have made differently

That last one matters. If you disagree with a settled decision, say so in a sentence as an observation and move on. Don't relitigate it disguised as a question.

## How to ask

Lead with your current understanding, then the specific fork. Exposing your interpretation lets the user correct the *framing*, not just answer the question — and a wrong framing is the more expensive error, because it survives the answer.

**Weak:**

> What should I do with the legacy caller?

**Strong:**

> I'm reading this as: keep the public API unchanged, replace only the internals. `reports/export.ts` calls the old internal function directly, bypassing that API. In scope, or leave it?

The strong version is answerable in three words and shows you already found the thing.

Use the environment's native mechanism for asking the user when one exists. If the possible answers are genuinely known and enumerable, structured choices are faster than typing. When you don't know the option space, ask openly rather than inventing three plausible-sounding choices; a forced multiple-choice on an open question quietly discards the answer the user actually had.

Prefer the fewest questions that resolve the fork. One sharp question beats four hedged ones. Past roughly three you've stopped clarifying and started interviewing — and quality drops as count rises, because the weak questions dilute attention from the one that was load-bearing.

## Example

Request: “Replace the legacy export path, but preserve behavior for jobs already in flight.”

Project inspection finds `jobs/legacy-export.ts` and `jobs/queue.ts`; the queue owns in-flight state.

Good:

> I read `jobs/legacy-export.ts` and `jobs/queue.ts`. I understand that new jobs should use the replacement while in-flight jobs retain the legacy behavior. Should the legacy path remain callable only for those in-flight jobs, or must it remain available to any caller during the transition?

Bad:

> Should I update the API, tests, docs, deployment, and monitoring?

The first question resolves user intent. The second inventories implementation work.

## After an answer

Fold the answer into your understanding, then re-read the request against the project with that correction applied. One answer often resolves several uncertainties at once, or makes a question you were holding moot.

Don't reflexively ask again. Ask only if a genuinely material fork remains.

Stop when you can state the intended outcome plainly without inventing any consequential requirement.

## Finishing

Hand back a one-line statement of the understanding you're proceeding on, plus any assumptions you made instead of asking. Then continue with the original task, or return control to whatever invoked this skill.

Produce no plan, spec, ticket list, design doc, or code here. Those are the next step, not this one.

## Success

The agent can proceed without guessing about anything that matters.

Question count is not the metric. Zero questions, correctly, is the best possible run.

## Clearability

Use Strict ASD-STE100 principles for every question. A question must have one clear meaning and one clear answer.

- Use active voice and plain words.
- Use one instruction or question per sentence.
- Keep each instruction to 20 words or fewer.
- Do not use semicolons, phrasal verbs, or noun clusters with more than three nouns.
- Use the same term for the same project item throughout the response.
- Define an unfamiliar project term before you use it.
- Preserve uncertainty. Do not change "may" or "could" into a fact.
- State the current interpretation before the question.
- State the specific decision that remains open.
- Include evidence only when it explains the decision.
- Use choices only when the choices are complete.

The user must be able to answer without rereading the request or inspecting the project.

## Clearability Check

Before you send a question, apply this check:

1. Use Strict ASD-STE100 principles for the question and its context.
2. Put one decision in each question.
3. Put one condition in each sentence.
4. Keep question sentences to 20 words or fewer.
5. Keep context sentences to 25 words or fewer.
6. Start with the actor, action, or condition. Do not bury the decision in background.
7. Use active voice unless the actor is unknown or irrelevant.
8. Use a direct verb. Do not use a noun in place of an action.
9. Do not use phrasal verbs, semicolons, or omitted words.
10. Keep noun clusters to three nouns or fewer. Split longer clusters into a clause.
11. Use one name for each project item. Do not rotate synonyms.
12. Define a technical term if the user may not know its project meaning.
13. Preserve all uncertainty, limits, and exceptions from the request and project evidence.
14. Do not add a cause, frequency, or certainty that the evidence does not show.
15. Use a list when the user must compare three or more conditions or choices.

Use simple tenses when they preserve the meaning. Keep a compound tense when it carries required current relevance or uncertainty.

Rewrite the question if any rule fails. If a required detail prevents a shorter sentence, keep the detail. Do not remove it for brevity.

Example:

**Unclear:**

> The legacy path is possibly used in some existing calls, so should we perhaps keep it around for compatibility, or update them?

**Clear:**

> `reports/export.ts` calls the legacy path. Should that caller use the new path, or remain unchanged?
