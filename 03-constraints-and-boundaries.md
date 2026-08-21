# Chapter 3 — Constraints and Boundaries

## The Problem That Looks Like Success

A researcher asks Claude to summarize the evidence on a contested medical topic. Claude returns four paragraphs: confident, well-organized, full of specific claims. The researcher skims it, finds it plausible, and pastes it into a draft. Two days later, a colleague points out that one of the cited effects does not appear in any of the papers the researcher actually provided. Claude had filled the gap with something that sounded reasonable.

The prompt never said to use only the provided sources. The prompt never said what to do with uncertainty. Claude performed exactly as requested — it summarized the topic — and the result was a confident fabrication dressed in professional prose.

The failure was not Claude's. The failure was in the specification. No constraint had been placed on the evidence rules.

---

## What This Chapter Lets You Do

After reading this chapter, you will be able to write prompts that:

- Define the scope of a task so Claude stays inside the job and does not invent adjacent material
- Set evidence rules so Claude draws only on what you provide
- Require uncertainty disclosure when evidence is missing or incomplete
- Protect private or sensitive data from appearing in outputs
- Specify a tone or register that must not drift
- Give Claude a clear instruction for what to do when a constraint conflicts with the request

This is the chapter that closes the gap between a plausible output and a trustworthy one.

---

## What a Constraint Is

A constraint is any part of a prompt that limits what Claude does rather than specifying what Claude produces. The task says "write a summary." The constraint says "only from the three documents below, flagging anything that is not directly supported."

Constraints come in five main types:

**Scope constraints** define what is inside the task and what is not. "Summarize only the methodology section." "Cover only cost, not timeline." "Do not include recommendations — only findings."

**Evidence constraints** specify what Claude can draw on. "Use only the sources I provide." "Do not add information beyond what is in the passage above." "If a claim requires a source not in these materials, say so explicitly and stop."

**Uncertainty constraints** require honesty about the limits of the evidence. "If you are uncertain, say so." "If the data does not support a conclusion, say the data does not support a conclusion." "Rate your confidence for each claim: high, medium, or low."

**Privacy and ethics constraints** protect sensitive information. "Do not use any names, student IDs, or case details in the output." "Do not reproduce any quotation longer than one sentence without attribution." "This output will be public-facing — treat all health information as general and non-identifying."

**Tone and register constraints** set the style and hold it. "Write for a general audience, not an expert one." "Avoid hedging phrases like 'it is worth noting.'" "Use plain language at approximately a tenth-grade reading level."

A good prompt often contains constraints from more than one category.

---

## Before and After: Adding Constraints to a Prompt

### Weak Prompt

> Summarize the research on remote work and productivity.

**What Claude produces:** A confident multi-paragraph summary drawing on Claude's broad training, including studies, meta-analyses, and observations from across years and contexts. The output will be fluent and organized. It will also contain specific claims about effect sizes, dates, and findings that the user cannot verify — because no sources were provided and no constraints were set.

### Why It Fails

The prompt contains a task and nothing else. Claude cannot know:

- Whether it should confine itself to sources the user provides
- What to do when evidence is mixed or contested
- Whether the user wants confidence levels or just conclusions
- How long the output should be
- Whether "productivity" means self-reported satisfaction, output measurement, or something else

So Claude applies defaults: draw on training, produce comprehensive output, write with confidence.

### Revised Prompt

> Using only the three reports I have pasted below this prompt, summarize what the evidence says about remote work and productivity. Define "productivity" using whatever definitions the reports use — do not import your own. If the reports conflict, name the conflict. If the evidence is insufficient to support a conclusion, say so rather than speculating. Maximum 300 words.
>
> [Report 1 text]
> [Report 2 text]
> [Report 3 text]

**What Claude produces:** A summary anchored to the three provided texts, using only their definitions and findings. When the reports conflict, Claude notes the conflict. When evidence is absent, Claude says so. The output is 300 words or fewer.

### What Changed

- An evidence constraint was added: "using only the three reports below."
- A definition constraint was added: use the reports' own terms.
- A conflict disclosure constraint was added.
- An uncertainty constraint was added.
- A word limit was set.

Each constraint closes a specific gap. The output is now inspectable: the user can check each claim against the provided reports. That is the test of a good constraint — does it make the output easier to review?

---

## The "Do Not Invent" Instruction

One constraint earns its own section: telling Claude not to fabricate.

Claude does not lie in the conventional sense. It generates text that is statistically coherent with its training. When it does not know something, it sometimes produces plausible-sounding text anyway — a phenomenon widely called hallucination in the research literature (though the mechanism is more precisely described as next-token prediction operating beyond the limits of grounded knowledge). The NIST AI Risk Management Framework (2023) identifies this as a key reliability risk for AI systems in professional contexts.

The fix is not to distrust Claude entirely. The fix is to write constraints that make fabrication visible.

Useful "do not invent" instructions include:

- "If a specific fact, figure, or name is not in the materials I have provided, do not include it."
- "If you would need to guess, say 'I do not have a source for this' and stop."
- "Do not add context from your training that was not given in this prompt."
- "If you are uncertain about a claim, mark it with [uncertain] and I will verify it."

The last version is especially useful because it does not ask Claude to stop — it asks Claude to signal, which keeps the workflow moving while flagging exactly where human judgment must enter.

---

## Constraints on Refusal: What to Do at the Boundary

A constraint is not useful if Claude does not know what to do when it cannot comply. Suppose you instruct Claude to use only provided sources, but the task cannot be completed with those sources alone. What should Claude do?

The default, without instruction, is to fill the gap. With explicit instruction, Claude can do something more useful.

**"Refuse or ask" rule:** "If you cannot complete this task using only the materials provided, do not attempt to fill gaps from your training. Instead, stop and tell me what additional information you would need."

**"Flag and continue" rule:** "Complete the task as best you can with the provided sources. Where you have drawn on your training rather than my materials, mark the claim with [from training] so I can verify it."

**"Narrow the scope" rule:** "If the evidence does not support a full answer, answer only the parts it does support and explicitly identify the parts it does not."

The choice between these depends on the use case. For high-stakes outputs — legal, medical, research — the "refuse or ask" rule reduces risk. For exploratory drafts where speed matters more, "flag and continue" keeps the workflow moving while preserving reviewability.

---

## Constraints That Conflict

Occasionally you will write constraints that pull against each other. "Be comprehensive" and "stay under 200 words" will conflict whenever comprehensiveness requires more space. "Use only provided sources" and "define all technical terms" will conflict if the sources do not define those terms.

When constraints conflict, Claude will typically pick one and proceed, often prioritizing the most recent or most specific instruction. A better approach is to anticipate common conflicts and specify a resolution order.

**Resolution by priority:** "If the word limit and completeness conflict, prioritize completeness and note when you have exceeded the limit."

**Resolution by exception:** "Maintain the 200-word limit unless a key finding requires more space. In that case, add a clearly labeled extension section."

**Resolution by query:** "If any of these constraints conflict in a way that affects the output significantly, stop and ask before proceeding."

The act of writing these resolution rules is itself useful: it forces you to notice the constraint conflicts before Claude does.

---

## The Human Gate

Constraints do not remove the need for review. They make review easier.

A constrained prompt produces output that can be checked against the constraint list. Each item on that list is a review question:

- Is every claim in these materials traceable to a provided source?
- Is every uncertain claim flagged?
- Is the output within the specified word count?
- Does the tone hold throughout?
- Has any private information appeared in the output?

Run the checklist. When a constraint has been honored, checking it takes seconds. When a constraint has been violated — the output invented a source, the tone slipped — the constraint tells you exactly what went wrong and where the revision begins.

A prompt without constraints produces output that can only be evaluated holistically. A constrained prompt produces output that can be audited systematically. That is the difference.

---

## Common Mistakes

**Stating the task without any constraints.** "Write a policy brief on this topic" is a task. A policy brief specification also states who the audience is, what sources count as evidence, how long it should be, and what it must not claim. Missing any of these opens the door to drift.

**Using constraints that are too vague to enforce.** "Be accurate" is not a constraint — it is an aspiration. "Draw only on the following four reports and mark any claim not traceable to them" is a constraint.

**Setting too many constraints without resolution order.** A prompt with twelve constraints and no priority ordering puts Claude in the position of improvising when they conflict. Name which constraints are non-negotiable and which are defaults.

**Assuming Claude remembers constraints from earlier in a conversation.** In a long session, Claude may appear to drift from instructions set at the beginning. If constraints are critical, restate the most important ones at the end of each follow-up prompt, or in a project-level instruction [verify — current as of writing for Claude Projects and persistent instructions].

**Treating a constraint as a substitution for judgment.** Constraints reduce the space of possible outputs. They do not guarantee that the output inside that space is good. You still have to read it.

---

## Try This

**Exercise 1 — Constraint Audit**
Take a prompt you have used recently that returned output that surprised or disappointed you. List every constraint that was present. Then list the constraints that were absent. For each absent constraint, write one sentence that adds it to the prompt. Run the revised prompt and compare the outputs.

**Exercise 2 — Constraint-First Drafting**
Before writing the task portion of a prompt, write the constraint portion first. Starting from the following categories, write at least one constraint for each that applies:
- Evidence (what sources can Claude draw on?)
- Scope (what is outside the task?)
- Uncertainty (what should Claude do when it does not know?)
- Tone (what register must the output hold?)
- Length (what is the word or page limit?)

Then write the task. Notice how the constraint-first approach changes how you frame the task itself.

**Exercise 3 — "Do Not Invent" in Practice**
Write a prompt that asks Claude to explain a specific topic you know well. Add: "If you are uncertain about any specific claim, mark it [uncertain]. If any claim is not in the materials provided, mark it [from training]." Review the output for unmarked claims. Verify one or two. How accurate are the unmarked claims? What does this tell you about the boundary between grounding and generation?

---

## What Would Change My Mind

The argument of this chapter assumes that constraints make outputs more reviewable and less likely to drift. I would revise that claim if:

- Evidence showed that heavily constrained prompts produced outputs that required more, not fewer, correction passes — suggesting that constraints introduce their own errors by misaligning Claude's defaults
- Research showed that "do not invent" instructions reliably suppressed hallucination without requiring source-level human verification — which would mean the instruction itself could be trusted rather than verified
- More sophisticated uncertainty signaling emerged from the models themselves [verify — current as of writing], reducing the need for explicit uncertainty constraints

Until then, the conservative position holds: write the constraint, then check the output.

---

## Still Puzzling

- Does constraint length have a point of diminishing return? At some number of constraints, does Claude begin to violate the ones at the end of a long list?
- How should constraints be ordered in a prompt for maximum reliability? Does position within the prompt affect how reliably each constraint is honored? [verify — current as of writing]
- Can a constraint library be made reusable across tasks, or does every domain need its own constraint vocabulary?

---

## Bridge to Chapter 4

You now have the tools to limit what Claude does. The next question is how to show Claude what right looks like. Constraints close doors. Examples open a specific one. Chapter 4 introduces few-shot examples, counterexamples, and style guidance — the part of a specification that teaches by demonstration rather than prohibition.

---

## Sources Used

- Anthropic prompt engineering documentation. https://docs.anthropic.com/ [verify — current as of writing]
- NIST AI Risk Management Framework (2023). National Institute of Standards and Technology.
- UNESCO generative AI guidance (2023). United Nations Educational, Scientific and Cultural Organization.
- Privacy-by-design principles. Ann Cavoukian. Office of the Information and Privacy Commissioner, Ontario.
- Human factors literature on automation bias. Mosier & Skitka (1996); Parasuraman & Manzey (2010).
- Technical writing guidance on scope control. Various, including Alred, Brusaw, & Oliu, *Handbook of Technical Writing*.
- Academic citation and quotation standards. APA, MLA, and Chicago style guides.
