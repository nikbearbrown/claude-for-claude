# Introduction: The Prompt Is the Work Order

## A Scene Worth Recognizing

You have a meeting in forty minutes. You need a one-page summary of a forty-page report for a group that has never seen the source material. You open Claude, paste in the document, and type: *Summarize this.*

Three seconds later you have something. It is fluent, organized, and the right length. You paste it into your slide deck and head to the meeting.

Halfway through, someone asks a question the summary does not answer. You check the original document. The answer is there, on page fourteen. Claude summarized what it judged to be most important — which was not the same as what your audience would find most important.

The output was not wrong. It was just not specified. You got a summary. You needed a summary for a particular audience, foregrounding a particular question, using particular terms your organization already uses, with a particular omission flagged so the audience knew the gap.

The gap between what you typed and what you needed is not a Claude problem. It is a specification problem. And fixing it is the subject of this book.

## What This Book Builds

This book teaches one discipline: writing prompts as specifications. Not magic phrases. Not clever wordings. Not tricks that coax the model into compliance. Specifications — the same intellectual work you do when you write a good brief, a clear task assignment, a usable rubric, or an inspection-ready requirement.

By the end, you will be able to:

- Build a prompt with the components that prevent the most common failures: task, context, constraints, output format, examples, and evaluation criteria.
- Decide what context Claude actually needs versus what buries the task.
- Use constraints to protect accuracy, scope, and tone.
- Give Claude examples that guide the shape of the output without over-constraining it.
- State evaluation criteria before Claude produces the output, so the review is predictable.
- Run a structured revision loop instead of vague follow-up.
- Translate conversational prompts into reusable workflow assets.

You will also learn what prompts cannot do — which is equally important. A prompt specifies the work. It does not guarantee the judgment that decides whether the work is right, complete, and worth acting on. That judgment stays with you.

## The Central Argument

There is a persistent misconception about prompt engineering: that the skill is finding the right secret phrase. Ask the model to act like an expert. Add *please*. Use a certain format. Say *take a deep breath.* The implication is that the model is hiding its best performance and clever phrasing unlocks it.

That frame is wrong in a durable way. The models you are using — including current Claude — are not reluctant. They are responsive. They do what the prompt asks. When output quality is low, the most frequent cause is not a locked model. It is an underspecified task.

The useful mental model is the work order. When a contractor receives a detailed work order — scope defined, materials specified, timeline bounded, quality criteria named, exceptions flagged — the result is more predictable than when they receive a vague request. The contractor's skill is the same in both cases. The specification is different.

Prompts work the same way. A vague prompt produces output shaped by Claude's defaults, which may or may not match your situation. A specified prompt produces output bounded by your requirements, which you can inspect, test, and revise.

## What Prompt Engineering Actually Is

The Anthropic prompt engineering documentation describes the goal as building prompts that meet defined success criteria — not prompts that sound clever (Anthropic, 2025). That framing is the right one. Success criteria come before the prompt, not after.

This connects prompt engineering to a discipline professionals already know: specification design. Technical writers specify deliverables. Engineers write acceptance criteria. Researchers write analysis plans before they see data. Teachers write rubrics before they grade. In each case, the discipline is the same: make the work inspectable before it is executed.

A prompt is a specification for one execution of a task. A good one includes:

1. What Claude should produce (the task)
2. What Claude needs to know to produce it (context)
3. What Claude should not do or include (constraints)
4. What the output should look like (format)
5. How the output will be judged (evaluation criteria)
6. If helpful, an example of what good looks like (examples)

You do not need all six components in every prompt. A simple task with shared context and obvious format may need only two or three. The point is not mechanical completeness — it is that each component exists to prevent a specific kind of failure, and knowing which failures you risk tells you which components to include.

## The Human Gate

There is one more thing the work-order frame makes clear: someone has to review the work.

A well-specified prompt makes review possible. It does not make review unnecessary. Claude produces an output. You decide whether that output is correct, complete, appropriately scoped, ethically sound, and worth using. That decision cannot be delegated.

This is not a weakness in the system. It is the design. Claude executes. You judge. The discipline of prompt engineering is partly about writing prompts that make your judgment easier — prompts whose outputs are bounded enough to check, specific enough to audit, and structured enough to compare against the criteria you set before the output arrived.

When the output is fluent but unreviewed, you have an artifact, not a result. A result is an artifact that has passed through a human gate.

## Why This Book Now

Prompting as a practice is maturing. Early guidance was often tip-list format: do this, avoid that, try this phrase. That guidance was useful when the main problem was getting any coherent output at all. The models have improved substantially, and the guidance needs to catch up.

The durable problems are not model problems. They are specification problems:

- Output that is accurate but answers the wrong question.
- Output that is relevant but wrong length, wrong format, or wrong audience.
- Output that is useful but impossible to verify.
- Output that is fluent but wrong in a way that takes expertise to detect.
- Output that is correct today but built on a model behavior that may change.

All of these are solvable with better specification. None of them require waiting for a better model.

A note on model-specific content: Claude's behavior changes as models are updated. Any specific behavior flagged in this book that depends on the current model is marked `[verify — current as of writing]`. The specification principles are model-independent. The model-specific tips are not.

## How the Book Is Organized

**Act One (Chapters 1–3)** replaces prompt tricks with specification thinking. You learn the anatomy of a prompt (Chapter 1), how to give the right amount of context without noise (Chapter 2), and how to write constraints that protect accuracy and scope (Chapter 3).

**Act Two (Chapters 4–6)** adds the components that shape and evaluate output. You learn to use examples without over-constraining the task (Chapter 4), to write evaluation criteria before output (Chapter 5), and to run structured revision loops (Chapter 6).

**Act Three (Chapters 7–13)** applies the discipline to real work: research, writing, data analysis, teaching, and handoffs to Claude Code and Claude Cowork. Chapter 12 turns successful prompts into maintained assets. Chapter 13 is a capstone: one workflow, three prompt passes.

## Before/After: The Meeting Summary

Return to the opening scene. Here is the original prompt:

---

**Weak prompt:**

> Summarize this.
>
> [forty-page report pasted here]

**What Claude produces:** A general summary, organized around what appears most prominent in the document — executive summary language, top-level findings, overall conclusions.

**The problem:** Your audience needs to know whether the project's Phase 2 budget assumptions are still valid, using the terminology their finance team uses, and they need to know explicitly what the report does not cover. None of that was in the prompt.

---

**Specified prompt:**

> Summarize the attached report for a finance review committee that has not seen this document before. The committee's primary question is whether the Phase 2 budget assumptions on pages 12–15 are still supported by the data. Use our organization's terminology: "baseline scenario," "stress scenario," and "reserve margin" (not "contingency"). Limit the summary to one page. End with a section called "What this report does not address" and list at least two items.

**What Claude produces:** A one-page summary organized around the Phase 2 budget question, using the specified terminology, ending with a gap section.

**What you still need to judge:** Whether the summary correctly characterized the budget assumptions, whether the gaps listed are the right ones, and whether the framing will land with this particular committee. Claude produced the artifact. You verify the judgment.

---

The difference is not magic phrasing. It is specification. The audience is named. The primary question is stated. Terminology is constrained. Format is bounded. A required section is named. The output is not just more useful — it is inspectable. You can check each requirement against what Claude produced.

That is prompt engineering.

## Try This

Before you read Chapter 1, take a prompt you have actually sent to Claude in the last week. Read it against these questions:

1. Did you name the task with a specific verb (summarize, extract, rewrite, critique, draft) — or did you leave it implicit?
2. Did you name the audience for the output?
3. Did you state one constraint — what the output should not do?
4. Did you state what format you expected?
5. Did you state one thing you would use to judge whether the output was good?

Most prompts answer none of these explicitly. That is the gap this book addresses. You are not looking for magic. You are learning to specify.

## What Would Change My Mind

This book argues that specification quality is the primary driver of prompt quality. If strong evidence emerged that vague prompts consistently produce better outputs than specified ones — not just for creative tasks where openness is the point, but for professional tasks with defined success criteria — the argument would need to be revised. So far, the evidence runs the other direction: Anthropic's own guidance, HCI research on task framing, and the practical experience of people who use Claude for complex work all support the specification frame.

The argument would also need revision if models advanced to the point where they reliably inferred what professionals meant by vague requests in unfamiliar domains. That day may come. It has not arrived yet. In the meantime, the specification discipline produces better results and makes outputs reviewable regardless of model capability.

## Still Puzzling

One genuine open question: as models improve, which parts of the specification can be safely omitted? Context inference is getting better. Format defaults are improving. At some point, the minimum viable prompt may shrink. The durable skill is probably not the mechanics of each component — it is knowing which specification gaps cause which failures, so you can diagnose and fix them faster.

## The Work Ahead

Each chapter that follows teaches one component of the specification. Each includes a before/after prompt walkthrough, exercises, and a note on what you still need to judge in the output. The model does the execution. You do the specification and the review.

That division of labor is the argument of this book. It is also the habit that separates professionals who use Claude well from professionals who use Claude fluently.

Fluency without specification produces polished artifacts. Specification with review produces trustworthy results.

---

## Sources Used

- Anthropic. (2025). *Prompt engineering overview*. https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
- Anthropic. (2025). *Prompting best practices*. https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- NIST. (2023). *AI Risk Management Framework (AI RMF 1.0)*. National Institute of Standards and Technology.
- Nielsen Norman Group. Guidance on AI UX and task framing (various, 2023–2025).
- UNESCO. (2023). *Guidance for generative AI in education and research*.

---

*Tags: #prompt-engineering #specification #work-order #claude #AI-literacy #professional-practice*
