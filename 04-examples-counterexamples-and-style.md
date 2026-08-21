# Chapter 4 — Examples, Counterexamples, and Style

## The Lesson You Did Not Intend to Teach

A writing instructor pastes one of her own essays into a prompt and asks Claude to draft a bio in the same style. Claude returns a bio that mimics her rhythms closely — the sentence length, the em-dash placement, the preference for concrete nouns over abstract ones. She is pleased.

Then she uses the same approach for a research memo. She pastes the first memo she wrote — the informal one from last year — as the example. Claude returns something equally informal: casual hedges, fragments, first-person asides. This is not what she wanted for the memo. But it is exactly what she asked for. The example she chose taught Claude what informal looked like, and Claude learned the lesson she did not intend to teach.

Examples are not just helpful illustrations. They are instructions in disguise. This chapter is about writing them deliberately.

---

## What This Chapter Lets You Do

After reading this chapter, you will be able to:

- Choose examples that teach what you actually intend to demonstrate
- Label examples so Claude knows which features to copy and which to ignore
- Use counterexamples to prevent the failure modes that unlabeled examples often create
- Control voice and style without losing substance
- Keep examples from overfitting — producing outputs that mimic surface features while missing the underlying task

---

## How Examples Work: In-Context Learning

When you provide an example in a prompt, you are using a mechanism researchers call few-shot prompting or in-context learning. The term describes what happens: Claude observes the pattern demonstrated by the example and applies it to the new task without any retraining. The model is learning, within the context of the current conversation, from the evidence you provide (Brown et al. 2020; Anthropic prompt engineering documentation).

A single example is one-shot prompting. Two or more examples are few-shot prompting. Zero examples — just a task — is zero-shot prompting.

Each choice trades something. Zero-shot is fast and makes the fewest assumptions about what the output should look like — useful when you want Claude's default framing. One-shot gives Claude a model to work from — useful when you know what the output should look like but your task description alone is not precise enough to get there. Few-shot shapes the output more narrowly — useful for extraction, classification, or any task where format consistency matters more than creative variation.

The risk rises with each example added: the more examples you provide, the more Claude anchors to their specific features — including the ones you did not mean to include.

---

## What Examples Teach (Besides What You Think)

An example teaches format, level, tone, reasoning pattern, length, vocabulary register, and rhetorical approach — often all at once, whether or not you intended any of those lessons.

A research note pasted as an example teaches Claude to hedge like a researcher. A corporate memo teaches Claude to use passive voice and nominalize verbs. A casual email teaches Claude to write casually. The content of the example matters less than its form.

This is why choosing an example carelessly produces surprising results. The writer pasting her informal memo was not thinking about tone. She was thinking about structure. Claude learned both.

The remedy is annotation: tell Claude what the example demonstrates and what it does not.

---

## Before and After: Annotating an Example

### Weak Prompt

> Here is a paragraph I wrote. Write two more paragraphs in the same style.
>
> *When the grant came through, everything changed. We had the resources, finally, and the question was how to use them without wasting the first real chance the lab had seen in four years. I called the team together. Nobody said anything for about thirty seconds.*

**What Claude produces:** Two more paragraphs in the same voice: short declarative sentences, close third-person perspective, past tense, understated emotion. The style is consistent. But the new paragraphs also inherit the subject matter — budgets, teams, labs — because the example contained content as well as style.

### Why It Fails

The prompt does not distinguish between what the example demonstrates (the sentence rhythm and emotional restraint) and what it does not (the specific subject matter and setting). Claude infers that all features are to be reproduced.

### Revised Prompt

> Here is a paragraph I wrote. The features to copy are: short declarative sentences, past tense, understated emotional observation, and a preference for showing rather than telling. Do not copy the subject matter — the new paragraphs should be about a job interview, not a lab or grant. Write two more paragraphs that match the style described above, not the content below.
>
> *When the grant came through, everything changed. We had the resources, finally, and the question was how to use them without wasting the first real chance the lab had seen in four years. I called the team together. Nobody said anything for about thirty seconds.*

**What Claude produces:** Two paragraphs about a job interview, written with short declarative sentences, past tense, and emotional restraint. The voice is consistent. The subject matter is not carried over.

### What Changed

The revision annotated the example explicitly:
- It named which features to copy (sentence rhythm, tense, emotional register)
- It excluded which features not to copy (subject matter)
- It stated the target subject matter directly

This is the principle: an example is not self-explanatory. The example teaches by demonstration, but you are responsible for narrating what the demonstration is showing.

---

## Counterexamples: Teaching by Showing What to Avoid

A counterexample is an example of what you do not want. It sounds counterintuitive — why show Claude a bad version? — but it works because Claude, like most learners, often grasps a principle more clearly when it sees the contrast (writing pedagogy literature on models and imitation; HCI literature on contrast effects in example-based learning).

Counterexamples are especially useful for:

**Tone failures** — when you cannot describe the tone you want but can point to one you do not want.

**Format drift** — when a particular structural failure keeps recurring (e.g., bullet-point bloat, passive voice, unnecessary hedging phrases).

**Overfitting prevention** — when an earlier example was too specific and you want to break its grip without removing it.

A counterexample prompt looks like this:

> Draft feedback on this student paragraph. Write feedback that is specific and constructive. Do not write feedback like the example below, which is evaluative without being actionable:
>
> *Counter-example (do not do this): "This paragraph lacks coherence and the argument is weak."*
>
> Instead, write feedback that names the specific sentence or claim that is causing the problem and suggests a concrete revision direction.

The counterexample teaches faster than a paragraph of description because it shows the failure mode rather than naming it abstractly.

---

## Style Transfer: Voice Samples and Their Limits

Style transfer is a specific use of examples: providing a sample of writing and asking Claude to produce new text in that style. It is useful for:

- Maintaining a consistent authorial voice across multiple documents
- Adapting content for a different audience while preserving brand register
- Producing multiple versions of the same content in different registers

Style transfer has limits that are important to name.

**Copyright.** Reproducing substantial portions of copyrighted text as an example may raise intellectual property concerns, depending on jurisdiction, use, and the amount reproduced. Academic citation and quotation standards (MLA, APA, Chicago) provide guidance on fair use for quotation; for commercial or publication contexts, legal advice may be warranted. The safest approach for style transfer is to use your own writing as the example, or short non-reproducing excerpts.

**Copyright in outputs.** The question of whether Claude's style-transfer outputs are derivative of the example is an open area of law and policy [verify — current as of writing]. For professional use, the conservative position is to treat style (not content) as transferable and to use original or openly licensed examples.

**Inappropriate mimicry.** Anthropic's AI safety guidance cautions against prompts designed to replicate specific living authors' voices in ways that could mislead readers. Where the concern is accuracy of attribution, style transfer should be disclosed.

**Style drift.** Claude does not have a stable memory of a style guide across a long conversation [verify — current as of writing]. If style consistency matters across a long document, include the style sample at the start of each major section or use a project-level instruction where available.

---

## Few-Shot Extraction: When Format Consistency Matters Most

The strongest use case for few-shot examples is extraction — pulling structured data from unstructured text. A single example teaches Claude the output format precisely; two or three examples teach it the edge cases.

**Zero-shot extraction prompt:**

> Extract the key findings from the text below and list them.

**Few-shot extraction prompt:**

> Extract the key findings from each text and list them in the format shown. Each finding should be one sentence, start with a verb, and include the population studied.
>
> **Example text:** A 2022 study of remote workers in financial services found that workers who chose their own hours reported 18% fewer stress-related absences.
>
> **Example extraction:**
> - Found that worker-chosen schedules reduced stress-related absences by 18% among remote financial services workers (2022 study).
>
> Now extract findings from the text below in the same format:
>
> [new text]

The few-shot approach reduces post-processing: every extraction follows the same structure. The tradeoff is that edge cases not covered by the examples may be formatted inconsistently — which is why two or three examples, including one edge case, are usually better than one.

---

## Style Guides as Reusable Context

A style guide pasted as context is a special form of few-shot example: it provides multiple rules and examples at once, covering tone, format, vocabulary, and common failure modes. If you have a document that codifies how you or your organization writes — a house style sheet, an editorial guide, a voice document — it can be included in the context section of a prompt.

The practical limit is length. Including a twenty-page style guide in every prompt is expensive in tokens and may bury other important context. The solution is a condensed style summary — a one-page version of the most important rules, tested against example outputs to confirm it produces the desired register.

A condensed style summary might read:

> **Writing style rules for this output:**
> - Active voice preferred; passive only when the subject is genuinely unknown
> - Sentences average 15–18 words; no sentence over 30 words
> - Avoid: "it is worth noting," "it is important to emphasize," "in conclusion"
> - Technical terms defined at first use; no jargon assumed
> - Headlines are noun phrases, not imperative sentences

This is more effective than "write in a clear, professional style" because it is inspectable. You can check each rule against the output. That checkability is the point.

---

## The Human Gate

Examples shape output powerfully — and that power is not always visible in the result. The output can look exactly like the example while subtly missing the task.

The review question for example-based prompts is not just "does this look right?" It is "does this do what I asked it to do?"

Specific gates:

- **Substance check:** Does the output contain accurate information, or did it copy the form of the example while inventing the content?
- **Scope check:** Did Claude stay on the stated subject, or did it drift toward the example's subject matter?
- **Style fidelity check:** Does the output match the named features of the style — not just look vaguely similar to the example?
- **Overfit check:** Would this output make sense to a reader who had not seen the example, or does it depend on implicit example-knowledge to parse?

A useful practice after getting an example-based output is to read it as if you had never seen the example. Does it stand on its own?

---

## Common Mistakes

**Using an example without annotating it.** An unannotated example teaches all its features equally. Claude cannot know which ones you consider essential and which are incidental unless you say.

**Using an example from the wrong register.** Pasting an informal document as an example for a formal task is the most common version of this error. Match the register of the example to the register of the target output, or explicitly name the mismatch and instruct Claude to adjust.

**Using too many examples when one is enough.** More examples do not always produce more consistent outputs. For some tasks, one well-chosen example is more effective than three inconsistent ones, which signal conflicting features.

**Expecting style to substitute for substance.** A style example teaches Claude how to write, not what to know. If the task requires specific content knowledge, the example must be accompanied by the content — not expected to carry it.

**Reproducing copyrighted examples at length without consideration.** If your workflow relies on pasting published text as examples, be aware of the copyright and fair-use considerations noted above.

---

## Try This

**Exercise 1 — Annotate Your Example**
Choose a piece of writing you have used — or might use — as a style example. Before pasting it into a prompt, write a three-to-five sentence annotation that names: (a) which stylistic features to reproduce, (b) which content features not to copy, and (c) one feature of the example that would be wrong for the target output. Use the annotated version in a prompt. Compare results with and without annotation.

**Exercise 2 — Add a Counterexample**
Think of a recurring output failure in your Claude work — a tone problem, a format habit, an overly hedged phrase that keeps appearing. Write a counterexample that shows that failure clearly. Add it to your next prompt with the instruction: "Do not write like this counterexample." Does the failure mode disappear?

**Exercise 3 — Build a Condensed Style Summary**
Take a document you have written that you consider representative of how you write at your best. Extract from it five rules that describe its style. Test those rules by asking Claude to write a short paragraph following only those rules, without seeing the original document. How close is the output to your voice?

---

## What Would Change My Mind

This chapter argues that examples are powerful and require annotation and counterexamples to use well. I would revise that claim if:

- Research showed that Claude reliably inferred the intended features of an example without annotation in professional writing tasks — which would reduce the case for the annotation step
- Evidence emerged that counterexamples reliably introduced their own confusion — that showing a bad example increased rather than decreased its incidence in outputs
- Better mechanisms for persistent style memory appeared in Claude's product features [verify — current as of writing], making it unnecessary to re-supply style examples in each prompt

Until then, the conservative position is: annotate every example, include a counterexample when a failure mode is predictable, and test whether the output stands without the example as a crutch.

---

## Still Puzzling

- How many examples produce diminishing returns in few-shot prompting for creative tasks? The research on this is clearer for classification tasks than for open-ended generation.
- Is there a principled way to condense a style guide into examples rather than rules? Or are rules always more reliable than demonstrations for constraining style?
- How should examples be managed across a long project with many prompts, when Claude's context window and memory differ across interfaces [verify — current as of writing]?

---

## Bridge to Chapter 5

You now know how to show Claude what good looks like. But showing good output is not the same as specifying what makes it good. Chapter 5 takes the next step: writing the criteria by which the output will be judged before the output is produced. This is the chapter that closes the loop between specification and review.

---

## Sources Used

- Brown, T. B., et al. (2020). Language models are few-shot learners. *Advances in Neural Information Processing Systems*, 33.
- Anthropic prompt engineering documentation. https://docs.anthropic.com/ [verify — current as of writing]
- NIST AI Risk Management Framework (2023). National Institute of Standards and Technology.
- Writing pedagogy on models and imitation. Various, including Murray, D. M. (1985). *A Writer Teaches Writing*. Houghton Mifflin.
- Human-computer interaction literature on examples. Sweller, J. (2006). The worked example effect and human cognition. *Learning and Instruction*, 16, 165–169.
- Technical communication sources on voice and audience. Alred, G. J., Brusaw, C. T., & Oliu, W. E. *Handbook of Technical Writing*. Bedford/St. Martin's.
- Copyright and quotation norms. MLA Handbook (9th ed.); APA Publication Manual (7th ed.); Chicago Manual of Style (17th ed.).
- Anthropic AI safety guidance (responsible use documentation). https://docs.anthropic.com/ [verify — current as of writing]
