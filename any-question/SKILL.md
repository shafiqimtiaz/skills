---
name: any-question
description: Clarifies ambiguous coding requests against the current project before work proceeds. Use when the user asks "any questions?", "is this clear?", "before you start", "what do you need from me?", warns not to assume, or when project evidence leaves materially different interpretations of the requested outcome.
---

# Any Question

Clarify the agent's understanding of the user's request.

Inspect the current project for facts. Ask the user only when a material ambiguity about their intent remains.

This skill does not plan, design, implement, review, estimate, or improve the requested work. Its only goal is:

> Understand what the user wants well enough to avoid consequential guessing.

## Core Rule

**Investigate first. Ask only when necessary.**

Invoking this skill does not mean a question is required. Zero questions is a successful result.

Do not invent uncertainty to justify using the skill.

## 1. Read the User Literally

Separate the request into:

* **Settled** — explicit, unqualified decisions or constraints.
* **Tentative** — "maybe", "probably", "I think", "not sure".
* **Unclear** — terms or references with more than one reasonable meaning.
* **Conflicting** — statements that cannot all be true at the same time.

Treat settled decisions as fixed. Do not reconfirm them.

If the user clearly revises an earlier instruction, use the newer one. If it is unclear whether they revised or contradicted it, that can justify a question.

Preserve uncertainty. Do not silently turn tentative language into a decision.

## 2. Inspect the Project

Resolve factual uncertainty from the project before asking the user.

Start with the smallest useful scope:

1. Files, symbols, or features the user named.
2. Their direct implementations and interfaces.
3. Relevant tests and configuration.
4. Direct callers, consumers, or adjacent code.

Expand further only when doing so can resolve a live ambiguity.

The project answers these, not the user:

* Which authentication mechanism already exists?
* Which function handles this request?
* What does this project call this concept?
* Which callers currently use this API?
* What behavior do existing tests preserve?
* Which convention do sibling features follow?

Project evidence establishes the current system. It does not decide the user's desired outcome. An explicit user instruction outranks an existing convention.

If the request conflicts with project evidence, determine whether the conflict changes the meaning of the request.

Do not treat one failed search as proof that something does not exist.

## 3. State the Current Understanding

Before asking anything, complete these three lines internally:

> The user wants ___.
>
> The project tells me ___.
>
> I would still have to assume ___.

Only the third line can produce questions.

## 4. Apply the Wrong-Guess Test

For each remaining assumption:

> If I pick the wrong reasonable interpretation, would the user say "that is not what I asked for"?

A question is justified when the wrong pick changes the intended:

* behavior
* scope
* target
* preserved behavior
* public contract
* compatibility requirement
* destructive effect
* user-visible outcome

Two possible implementations do not by themselves imply two possible intents. Different libraries, file layouts, names, internal abstractions, or code paths are yours to choose.

If both interpretations satisfy the request, pick the more likely one and record it as an assumption.

## 5. Apply the Question Gate

Ask only when all of these hold:

1. The user did not already settle it.
2. Project evidence cannot settle it.
3. At least two reasonable interpretations remain.
4. They produce materially different requested outcomes.
5. It does not depend on another unanswered question.
6. It does not rely on an unverified premise.

If any condition fails, do not ask.

### Dependency Rule

Ask the question that eliminates the most downstream uncertainty first.

Do not ask a question when another unresolved answer could make it irrelevant.

Reassess after each answer. A previously valid question can become unnecessary. Delete it rather than asking it anyway.

### Premise Rule

Verify a question's factual premises against the project.

Present unresolved interpretations as interpretations, not facts.

Do not invent timelines, migrations, deprecations, or compatibility requirements.

## 6. Ask About Intent, Not Implementation Inventory

**Good subjects:**

* Which behavior does the user intend?
* What is inside or outside the requested scope?
* Which existing behavior must remain?
* Which referenced project item does the user mean?
* Is a conflict between the request and current behavior intentional?
* What does an ambiguous term mean in this request?

**Bad subjects:**

* Which files should change?
* Should tests or documentation be updated?
* Which internal class name should be used?
* Which library should implement an already-settled behavior?
* Should an explicitly requested approach be used?

Do not relitigate a settled user decision because you prefer another approach. That belongs outside this skill.

## 7. Write the Question

**Expose your interpretation before the fork.**

Weak:

> What should I do with the legacy caller?

Strong:

> I understand this as preserving the public API and replacing only its internals. `reports/export.ts` bypasses that API. Include that caller?

The strong version shows what you currently believe, what evidence created the doubt, and exactly which decision is open.

**State your default when you have one.**

Name the option you will take if the user says nothing, and why. The user can then answer in one word, and your leaning stays visible while it is still cheap to correct.

Do not select the answer for the user. A stated default is a prediction they can overturn, not a decision made on their behalf. Wait for the answer when the user can give one.

**Match the user's abstraction level.**

Ask about outcomes in the language the user already uses. If they discuss product behavior, do not make them choose internal enums or class names. If they already use exact implementation terms, use those terms.

**Use the environment's native question mechanism** when one exists. Do not assume a specific tool or runtime.

Use structured choices only when the valid options are genuinely known and complete. Use an open response when the option space is unknown. Do not invent plausible options to fill a multiple-choice interface.

**Ask one question at a time.**

Multiple questions can share one interaction only when they are independent. Never batch when one answer could change or remove another. Never ask more merely because the interface supports a batch.

**Clarity rules:**

* One decision per question.
* Same term for the same project item.
* 20 words or fewer per question sentence, 25 per context sentence, when practical.
* Define an unfamiliar project term when necessary.
* Make each offered choice describe a distinct outcome.

The user should be able to answer without rereading their request or opening the project.

## 8. Reassess After Every Answer

Fold the answer into the current understanding. Recheck only the project context that answer affects. Then reconsider every remaining uncertainty.

Do not walk a stored question list mechanically. One answer can resolve several ambiguities, invalidate earlier assumptions, make later questions unnecessary, or expose one new one.

Ask again only when a material ambiguity survives the full process.

## 9. Return the Result

Return exactly these parts, in this order:

**Understanding** — one statement of the requested outcome, in the user's terms.

**Assumptions** — every choice you made instead of asking. One line each: the choice, and the alternative you rejected.

Assumptions is required. Write `none` only when you made no choice at all.

Do not append a plan, specification, design, tickets, implementation steps, code, or a review. Return control after clarification.

Do not ask "Is that correct?" when nothing remains ambiguous. That converts a resolved understanding back into an unnecessary question.

## Examples

### No Question

> Add caching to `UserService.getProfile()` using the existing Redis client. Keep the current TTL.

The project confirms the Redis client and the current TTL. Both decisions are settled — do not reconfirm either.

**Understanding:** add caching to `UserService.getProfile()` using the existing Redis client, preserving the current TTL.

**Assumptions:** cache key follows the `resource:id` shape used by the other cached services; no invalidation hook, matching the TTL-only convention.

### Project Resolves the Gap

> Apply the same authorization to the new endpoint.

Adjacent endpoints all use `requireAdmin`. The reference is unambiguous. Do not ask which mechanism to use.

### Material Ambiguity

> Replace the legacy export path, but preserve behavior for jobs already in flight.

The project shows new jobs enter `jobs/queue.ts`, in-flight jobs resume through `jobs/legacy-export.ts`, and other code calls the legacy export directly.

Ask:

> I understand only in-flight jobs need legacy behavior. Must other callers keep access to the legacy export? I will keep it unless you say otherwise — removing it would silently change what those callers produce.

The answer changes the requested compatibility boundary.

### Dependent Questions

Do not ask:

1. Should the legacy API remain?
2. Which callers should migrate?
3. When should the old implementation be deleted?

Question 1 can make 2 and 3 irrelevant. Ask it, then reassess.

## Success Criterion

You can state what the user wants without guessing about anything consequential.

Question count is not the metric.

**Zero questions, when correct, is the best possible run.**
