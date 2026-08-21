# Chapter 6 — Iteration and Revision Loops

The first draft is not the problem. The problem is what you do next.

You ask Claude to write a strategy memo. It comes back four paragraphs, confident in tone, organized in structure. Something is off — the section on budget is too thin, the audience is wrong, the third paragraph makes an unsupported claim. You stare at it and type: *"Make it better."*

Claude produces another version. Different, but not necessarily improved. The budget section is still thin. Something new is off in paragraph two. You have not made the work better. You have made the work different.

This chapter is about replacing "make it better" with a method.

---

## What This Chapter Lets You Do

After this chapter you will be able to:

- Diagnose what failed in an output before sending a revision prompt
- Name the specific change target so Claude knows what to preserve and what to change
- Ask for a change log so you can inspect what moved
- Compare two revision candidates against stated criteria
- Recognize when an output cannot be improved by revision and must start over

---

## The Core Problem: Drift Without Diagnosis

A vague revision prompt tells Claude to change something but not what or why. The model fills the gap with inference — it guesses what you wanted and generates toward that guess. Sometimes the guess is right. Often it is not, and you get drift: the output shifts in a direction you did not specify, fixing one problem while introducing another.

Revision drift is not a model failure. It is a specification failure. The work order was incomplete. The fix is to treat the revision prompt as a second specification — as careful as the first, and addressed to what the first output revealed.

The revision loop has four steps:

1. **Diagnose.** Name what the output did wrong and why.
2. **Target.** Specify which element should change.
3. **Preserve.** Name what must not change.
4. **Verify.** Ask for a change log or comparison so you can confirm what moved.

Each step turns a vague "make it better" into a work order.

---

## Before / After: A Revision Prompt Walkthrough

### The Task

You asked Claude to draft a two-paragraph executive summary for a nonprofit's annual report. The output is technically correct but reads too formally for a donor audience and buries the year's key accomplishment in the second paragraph.

### BEFORE — Vague Revision Prompt

```
Make it more engaging and fix the structure.
```

What Claude hears: something about tone and structure. It will probably soften language and move sentences. You cannot predict which sentences or how. You cannot confirm it preserved the facts. You have no record of what changed.

### AFTER — Diagnostic Revision Prompt

```
This draft has two problems I want fixed separately.

Problem 1 — Tone: The language reads as formal institutional reporting.
The intended reader is a first-time donor. Revise the language in
paragraph 1 to be warmer and more direct. Do not change the facts.
Do not add new claims. Keep the donor's name placeholder [DONOR_NAME].

Problem 2 — Structure: The key accomplishment (serving 1,200 families)
appears in sentence 4 of paragraph 2. Move it to sentence 1 of paragraph 1.

After your revision, give me a three-line change log: what you moved,
what you changed in language, and what you kept exactly as written.
```

This prompt does three things the first did not:

- Names the failure categories (tone, structure) separately
- Constrains the edit (facts unchanged, no new claims)
- Requests a change log for audit

The output you get is inspectable. You can check whether the accomplishment moved, whether the facts survived, and whether the tone shifted in the direction you meant.

---

## The Revision Checklist

Before you send a revision prompt, answer these five questions. If you cannot answer one, the prompt is not ready.

| Question | Why It Matters |
|---|---|
| What specifically failed? | Vague diagnosis produces vague revision |
| What is the specific change target? | Unnamed targets invite unwanted changes everywhere |
| What must not change? | Preservation constraints prevent drift into new errors |
| How will you verify the revision was made? | Without a change log, you are guessing |
| Is the original insufficient or just unpolished? | Some outputs cannot be fixed by revision |

---

## The Diagnose-Revise-Compare Pattern

When you are uncertain which revision direction is better, use compare-and-select. Ask Claude to produce two versions of the changed element — each following a different approach — and then evaluate them against your criteria.

```
The transition between section 2 and section 3 is abrupt. The reader
needs to understand that we are moving from the problem statement
to the proposed solution.

Produce two alternative transition sentences:
  Version A: Uses a question to bridge the sections
  Version B: Summarizes what section 2 established before opening section 3

Then tell me which of the two better serves a reader who is new to
this topic and may have skimmed section 1.
```

This pattern externalizes the decision. You see both options, you see Claude's reasoning, and you make the final call. If neither is right, you have more information about what the gap actually is.

---

## The Change Log Request

A change log is a short, explicit accounting of what moved in the revision. It is the single most useful tool for keeping iteration honest.

Without a change log, you must compare the before and after text yourself to determine what changed. That comparison is error-prone when outputs are long, and it is the kind of close reading that most people skip when they are in a hurry — which is exactly when the errors enter.

Request format:

```
After your revision, provide a change log with three items:
1. What was moved (and where it went)
2. What language was changed (and what principle guided the change)
3. What was kept exactly as written
```

If the change log reveals that Claude changed something you did not authorize, you have caught a drift before it compounds. If it reveals that Claude did not actually move the thing you asked to move, you have caught a compliance failure. Either way, the next prompt is more specific.

---

## Preserve Constraints Explicitly

One of the most common revision failures is the implicit overwrite. You ask Claude to improve the tone of a paragraph. It does — and in the process, it softens a specific claim you needed to remain strong, drops a caveat you added to protect accuracy, or changes a number without flagging it.

This does not happen because the model is careless. It happens because the revision prompt said "improve the tone" and did not say "preserve the claim in sentence two, the caveat in sentence four, and all numbers exactly."

The work order for a revision is not just the change you want. It is the change you want plus the wall around what must not be touched.

Preservation constraint format:

```
Revise the introduction for a non-specialist audience.
Constraints:
- Keep all statistics exactly as written
- Keep the three-part structure (problem / approach / outcome)
- Do not add new examples or claims
- Do not change the title
```

[verify — current as of writing] Claude generally follows explicit preservation constraints when they are stated clearly, but it is not guaranteed to catch every implied constraint from context. State them explicitly.

---

## When to Start Over, Not Revise

Some outputs cannot be improved through iteration. They need a new first prompt.

Signals that revision is the wrong move:

- **Wrong task entirely.** Claude misunderstood the goal. Every revision will be trying to fix an output built on a wrong foundation.
- **Wrong structure.** The argument is organized around the wrong framework. Line-by-line revision will not fix a structural problem.
- **Compounding errors.** Each revision introduces a new problem as it fixes the old one. The output is deteriorating, not improving.
- **Constraint violations accumulate.** If you have revised three times and each version violates a different constraint, the specification is not clear enough to support iteration.

In these cases, stop. Return to the original specification and ask: what did it fail to say that would have prevented this? Rewrite the prompt, not the output.

---

## Self-Critique Prompts

You can ask Claude to critique its own output before you do. This is not a substitute for your judgment, but it can surface failures that are easier to see with fresh framing.

```
You just produced a draft of [X]. Before I review it, critique the draft
against these three criteria:
1. Does it address the audience I described?
2. Does every claim rest on the sources I provided?
3. Is the structure appropriate for a reader who will skim first and
   read in detail second?

List any weaknesses. Do not revise yet — just diagnose.
```

[verify — current as of writing] Claude's self-critique is more useful for surface-level issues (tone, completeness, structure) than for factual accuracy of claims. Do not use self-critique as a substitute for verifying facts yourself.

The self-critique prompt produces a diagnosis you can then use as the basis for your revision prompt. It turns the revision loop into a three-step cycle: draft, diagnose, revise — with the diagnosis step made explicit rather than kept inside the black box.

---

## Reusable Revision Template

Copy and adapt this template for any revision task:

```
I'm revising this draft: [paste output or name the section]

What failed:
[Name the specific failure — tone, structure, completeness, accuracy, etc.]

Change target:
[Name the specific element to change — a sentence, a section, a transition, etc.]

Constraints (do not change these):
[List what must survive the revision unchanged]

How to verify:
After revising, provide a change log:
1. What you changed
2. What principle guided the change
3. What you kept exactly as written

Revision:
```

---

## Common Mistakes

**Sending "make it better" without a diagnosis.** The model will make it different. Whether that is better depends on inferences you have not shared.

**Revising everything at once.** If you ask Claude to fix tone, structure, accuracy, and length in one pass, you cannot tell which change caused a new problem. Fix one thing at a time.

**Skipping the change log on long documents.** The longer the document, the more quietly a revision can alter something important. The change log is most critical when the output is most complex.

**Iterating past the point of diminishing returns.** After three or four revision passes, you are often no longer improving the output — you are exploring a space of variation. If you cannot name what specific failure remains, stop iterating and review the whole.

**Using self-critique as fact-checking.** Claude's self-assessment of factual accuracy is not reliable. The human owns the fact check.

---

## Try This

**Exercise 1 — Diagnose before you revise.**
Find any prompt you have sent in the past week that produced an output you were not satisfied with. Before writing a new prompt, write a diagnosis: what specifically failed? Name at least two distinct failure categories. Then write a revision prompt that targets each separately and preserves what worked. Compare the result to what you would have typed if you had sent "make it better."

**Exercise 2 — Request a change log.**
Take a document you want Claude to line-edit. Send the edit request with an explicit change log requirement (use the format above). When you receive the response, check the change log against the text. Did Claude change anything it said it kept? Did it keep anything it said it changed? What does that tell you about where review effort should go?

**Exercise 3 — Compare-and-select.**
Choose a transition or opening sentence in a document you are working on. Ask Claude to produce two alternatives with different approaches. Before reading Claude's evaluation, pick your preferred version. Then compare your choice to Claude's. If they differ, ask Claude to argue for its choice and you to argue for yours. Decide based on the argument, not the source.

---

## What Would Change My Mind

This chapter argues that structured revision prompts are always better than vague ones. If readers find that the overhead of writing a diagnostic revision prompt costs more time than the quality gain is worth — across multiple real-world use cases — then the template needs to be lighter, not more thorough. The checklist format is a hypothesis about what makes revision work; it should be tested against real task cycles, not treated as a fixed protocol.

---

## Still Puzzling

When does asking Claude to self-critique actually improve the next output versus simply producing a longer exchange that arrives at the same place? The research on LLM self-correction is mixed (Huang et al. 2023 found that self-correction without external feedback often does not improve and can degrade output quality). The honest answer is: self-critique prompts are most useful as a way to externalize the diagnosis step for the human's benefit, not as a reliable internal quality filter for the model.

---

## Bridge

You now have the full revision toolkit: diagnosis, targeting, preservation constraints, change logs, compare-and-select, and the signal for when to start over rather than iterate. That closes the foundations arc. The next arc applies the specification discipline to specific domains of work. Chapter 7 takes the first domain: research and literature work, where the stakes of unverified output are highest and the temptation to trust Claude's fluency is strongest.

---

## Sources Used

- Anthropic prompt engineering documentation. https://docs.anthropic.com/
- Huang, J., et al. (2023). Large language models cannot self-correct reasoning yet. *Proceedings of ICLR 2024* (arXiv:2310.01798).
- NIST AI Risk Management Framework (NIST AI RMF). https://www.nist.gov/system/files/documents/2023/01/26/AI%20RMF%201.0.pdf
- Cognitive apprenticeship literature on externalized metacognition (Collins et al. 1989)
- Technical communication revision guidance (general field practice)
