# Chapter 1: Anatomy of a Claude Prompt

## The Scene: Same Task, Different Architecture

A policy analyst needs to turn a dense 20-page government report into a clear brief for a non-specialist committee. She opens Claude and types:

> *Summarize this report for me.*

She pastes the document and submits. The output is organized and reads well. It also runs to three pages, leads with methodology the committee does not care about, buries the financial findings on page two, uses regulatory jargon the committee was specifically told to avoid, and ends without a recommendation. She sends a follow-up: *Can you make it shorter?* Claude shortens it. The jargon stays. The financial findings are still buried.

She spends forty minutes revising the output manually.

A colleague facing the same task writes a different kind of prompt. He gets a tight, audience-appropriate brief in one pass — with the financial findings first, plain language throughout, and a three-sentence recommendation at the end. He spends eight minutes reviewing it.

The difference is not the model. It is the structure of the prompt. This chapter gives you that structure.

## What This Chapter Builds

After this chapter you will be able to:

- Name the six components of a well-structured prompt and explain what failure each one prevents.
- Write a prompt with each component present when the task requires it.
- Identify which components are required for a given task and which can be omitted without risk.
- Use a reusable skeleton that adapts to your work.

## The Six Components

A strong Claude prompt separates six kinds of information. They do not have to appear in a fixed order, and not every task needs all six. But each one exists to prevent a specific failure.

### 1. Task

The task is the core request: what you want Claude to produce, expressed with a specific action verb.

*Summarize, extract, rewrite, critique, compare, draft, list, explain, classify, translate, evaluate.*

Vague verbs create specification gaps. *Help me with* is not a task. *Review this* is ambiguous — review for what? *Look at this and tell me what you think* hands the entire specification problem back to Claude.

**What weak task statements produce:** Output that addresses the question Claude found in the text, not necessarily the question you had.

**What a specific task statement produces:** Output aimed at the job you named.

A useful test: can you tell from the task verb alone whether Claude succeeded? *Summarize* is testable — either you have a summary or you do not. *Help me think through* is not.

### 2. Context

Context is what Claude needs to know to do the task well: who the audience is, what the source material is, what situation this will be used in, what Claude should assume the reader already knows.

Context is not everything you know about the topic. It is the information that would change the output if Claude did not have it.

**What missing context produces:** Output calibrated to Claude's default assumptions, which are often the wrong assumptions for your situation.

**What relevant context produces:** Output calibrated to the actual situation.

Context is covered in depth in Chapter 2. The key point here is that context is distinct from the task. The task says what to do. Context says what the situation is.

### 3. Constraints

Constraints define the boundaries of the work: what Claude should not do, what it should not include, and what limits apply.

- *Do not invent citations.*
- *Use only the sources I have provided.*
- *Do not exceed 500 words.*
- *Do not recommend a specific vendor.*
- *Use plain language; avoid jargon.*
- *If you are uncertain, say so rather than speculating.*

Constraints are the difference between an output that is useful and one that is technically on-task but wrong for the use case.

**What missing constraints produce:** Output that does the task in the most obvious way, which may include hallucinated details, irrelevant scope, inappropriate tone, or excess length.

**What explicit constraints produce:** Output with a tighter perimeter — easier to review and safer to use.

A common mistake is treating constraints as negative: *don't do X, avoid Y.* Some of the most useful constraints are positive: *always include a confidence note, always end with a next-step recommendation, always flag assumptions.*

### 4. Output Format

Output format tells Claude what the deliverable should look like: length, structure, labeling, and medium.

- *One-page executive brief, three sections: situation, findings, recommendation.*
- *A bulleted list, no headers.*
- *A table with three columns: assumption, evidence for, evidence against.*
- *Plain paragraphs, no lists.*
- *JSON with keys: title, summary, confidence.*

**What missing format specification produces:** Claude chooses a format based on what it judges most natural for the task, which may not match your use context. You then spend time reformatting.

**What explicit format specification produces:** An output already shaped for its destination — a slide, a document section, a data pipeline, a committee brief.

Format is one of the highest-leverage, lowest-cost components to specify. It takes five words and it almost always helps.

### 5. Examples

Examples show Claude what good looks like — or what bad looks like (anti-examples). This is also called few-shot prompting in the research literature on in-context learning.

An example can be a full sample output, a sentence in the right voice, a correctly formatted row, or a specific phrase you want emulated.

**What no example produces:** Claude interprets the task through its own defaults for genre, register, and format.

**What one good example produces:** Output that matches the pattern you showed, not just the pattern Claude inferred from the task description.

Important limit: examples constrain. A single example in a narrow format can make Claude rigidly copy that format even when the content varies. Use examples to anchor voice and structure, not to over-specify every detail. Chapter 4 covers this in depth.

### 6. Evaluation Criteria

Evaluation criteria state how the output will be judged. They can be explicit (a rubric, a test) or a single sentence (*This output is successful if a first-year student could understand it without the original report*).

This is the component most professionals skip. It is also the one that does the most to make outputs inspectable.

**What no evaluation criteria produce:** Output that Claude judges internally against its own defaults. Your review process has no anchor.

**What stated evaluation criteria produce:** An output Claude has attempted to satisfy against your criteria, and a review process you can run against those same criteria. The criteria precede the output — they are not reverse-engineered after the fact.

Anthropic's own documentation frames prompt engineering around defining success criteria before building the prompt (Anthropic, 2025). The evaluation criteria component is where that principle appears inside the prompt itself.

## The Skeleton

Here is a reusable template. Use the components that matter for your task, in any order that reads clearly.

```
Task: [specific verb + object]
Audience: [who will use the output]
Source material: [what Claude should draw on]
Constraints: [what not to do, limits, required conventions]
Format: [structure, length, labeling]
Example: [optional: one example of good output or good format]
Evaluation criteria: [how you will judge whether this worked]
```

You do not need all seven lines every time. A simple task may need two. A complex workflow may need all of them and more.

## Before/After: The Policy Brief

Here is the scenario from the opening applied to the anatomy template.

---

**Weak prompt:**

> Summarize this report for me.
>
> [20-page government report pasted here]

**What Claude produces:** A 3-page summary organized around the document's own structure — heavy on methodology, light on implications, written in regulatory language, no clear recommendation.

**The problem:** The analyst needed a brief for a non-specialist committee, focused on financial findings, in plain language, with a recommendation — none of which was specified.

---

**Specified prompt:**

> **Task:** Write an executive brief from the attached report.
>
> **Audience:** A committee of five non-specialists who have not read the report and have fifteen minutes before the meeting. Their main concern is the financial risk findings.
>
> **Source material:** Use only the attached report. Do not draw on outside knowledge.
>
> **Constraints:** Plain language — no regulatory jargon. Maximum 400 words. Do not recommend a specific course of action beyond what the report explicitly supports.
>
> **Format:** Three sections — Situation (1 paragraph), Key Financial Findings (3–5 bullets), Recommended Next Step (1–2 sentences).
>
> **Evaluation criteria:** A committee member who has not read the report should be able to explain the financial risk picture accurately after reading this brief.

**What Claude produces:** A 380-word brief in three labeled sections. Financial findings are prominent. Language is accessible. The recommendation is hedged appropriately against the report's own caveats.

**What you still need to judge:** Whether the financial findings are accurately represented (requires checking against the source), whether the recommended next step is appropriate for your organization's situation, and whether this committee will respond well to this framing. Claude produced the artifact. You verify it.

---

The difference between these prompts is not sophistication. It is component coverage. The second prompt names the audience, scopes the source material, constrains the language, sets the format, and states the evaluation criterion. Those five additions — which took about two minutes to write — produced an output that required eight minutes of review instead of forty.

## The Human Gate

Specifying the prompt well does not remove the need to review. It changes the nature of the review.

With an underspecified prompt, review is open-ended: *Is this good? Does it have what I need? Where did it go wrong?* You are reconstructing the criteria after the fact.

With a specified prompt, review is against criteria you set in advance: *Is the language free of jargon? Are the financial findings prominent? Is it under 400 words? Can a non-specialist understand it?* The review is an inspection, not an interpretation.

Inspections are faster and more reliable than interpretations. Specification makes the inspection possible.

## Common Mistakes

**Over-specifying the task, under-specifying the constraints.** The task verb is precise, but there are no constraints on scope or accuracy. Claude does the right thing in the wrong way.

**Treating context as the task.** A prompt full of background information without a clear task is a context dump. Claude will do something with it, but not necessarily what you needed.

**Skipping format.** Format takes five words and saves five minutes. The most common reason professionals spend time reformatting outputs is that they did not name the format they wanted.

**Using vague evaluation criteria.** *Make it good* is not a criterion. *A first-year student should be able to follow this without the source* is a criterion. If you cannot check it, it is not a criterion.

**Leaving examples until you are disappointed.** Add an example when you have a clear model of what good looks like. Do not wait until you have three bad outputs to figure out that an example would help.

## Try This

**Exercise 1 (Audit).** Take a prompt you have used in the past two weeks. Map it against the six components. Which ones are present? Which are missing? Which missing ones would have helped?

**Exercise 2 (Build).** Choose a task you repeat regularly. Write a fully specified prompt using the skeleton above. Run it. Then audit the output against your evaluation criteria. Where did Claude hit the mark? Where did it miss?

**Exercise 3 (Minimum viable).** Write the shortest prompt that still includes a task, at least one constraint, and a format. How short can you make it while still getting an inspectable output?

## What Would Change My Mind

The argument here is that prompt components prevent specific failures. If evidence accumulated that adding components — constraints especially — systematically made outputs worse rather than better, the model would need to be revised. Some research on over-constrained prompts suggests diminishing returns at extreme constraint density `[verify — current as of writing]`. But the baseline — that explicit structure outperforms vague requests for professional tasks with defined success criteria — is well-supported by Anthropic's guidance, HCI research on task framing, and the practical record of people doing this kind of work.

## Still Puzzling

As models improve their ability to infer intent from vague requests, some components may become less necessary for routine tasks. The interesting design question is: which components will last longest? My current guess is evaluation criteria — because they are not about inference, they are about what you need, and only you know that.

## Bridge

You now have the skeleton. The next chapter focuses on the component that probably carries the most weight and is most often mishandled: context. How much does Claude actually need? What happens when you give it too much? And how do you decide what to cut?

---

## Sources Used

- Anthropic. (2025). *Prompt engineering overview*. https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
- Anthropic. (2025). *Prompting best practices*. https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- Anthropic. (2025). *System prompt guidance*. https://docs.anthropic.com/
- Brown, T. et al. (2020). *Language models are few-shot learners.* NeurIPS. [In-context learning / few-shot prompting research.]
- NIST. (2023). *AI Risk Management Framework (AI RMF 1.0)*. National Institute of Standards and Technology.
- Nielsen Norman Group. AI UX and task framing guidance (various, 2023–2025).
- Software acceptance criteria practice. (Technical communication and engineering literature.)

---

*Tags: #anatomy #task #context #constraints #format #examples #evaluation-criteria #specification #skeleton*
