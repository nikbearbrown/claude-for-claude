# Chapter 2: Context: What Claude Needs and What It Does Not

## The Scene: Two Context Problems, One Chapter

**Problem One.** A researcher asks Claude to help draft a methods section for a paper. She provides a paragraph of background on her field, a sentence about her research question, and a long description of the statistical software she used. Claude produces a methods section that is technically well-structured but describes a study design the researcher did not use. The background she provided was too abstract — it told Claude about the field but not about the specific study.

**Problem Two.** A manager asks Claude to write a one-paragraph project update for a status report. He pastes three previous status reports (six pages), a project charter (twelve pages), an email thread (forty messages), and adds: *Write a paragraph summarizing where we are.* Claude produces something coherent but oddly long, unfocused, and weighted toward the project charter — the most formally structured document — rather than the most recent email thread, which had the actual current status.

Both failures are context failures. The first is too little of the right context. The second is too much of the wrong context, poorly organized.

This chapter is about the judgment that sits between those two problems.

## What This Chapter Builds

After this chapter you will be able to:

- Distinguish between context Claude needs, context that is neutral, and context that actively hurts.
- Organize context so Claude can prioritize it correctly.
- Apply context hygiene: label sources, state assumptions, declare exclusions, remove irrelevant material.
- Identify when context is creating privacy risk and what to do about it.

## What Context Actually Does

Context in a prompt is information Claude uses to calibrate its output. It is not decoration and it is not the task. Good context answers questions the task leaves open:

- Who is the audience for this output?
- What do they already know?
- What is the source material I am drawing on?
- What assumptions should Claude make about my situation?
- What should Claude treat as background and not include in the output?

Without context, Claude fills these gaps with defaults. Its defaults are not random — they are reasonable — but they are generic. A generic answer to a specific professional question is often accurate in the abstract and wrong for the situation.

The research literature on retrieval-augmented generation (RAG) and long-context models makes a point that applies directly to prompting: context helps when it is relevant, organized, and bounded. Irrelevant context does not just fail to help — it can actively distort output by creating false priority signals or burying the information that matters (Anthropic long-context guidance, 2025).

## The Four Types of Context

It helps to think about context in four categories.

### 1. Essential Context

Essential context is information that would change the output materially if Claude did not have it. Without it, Claude will produce something that looks right but is wrong for the situation.

Examples:
- The audience for the output (*a committee of finance non-specialists*)
- The source material Claude should draw on (*use only the attached report*)
- A constraint on assumptions (*assume the reader has not seen the underlying data*)
- The purpose the output will serve (*this will go directly into a grant proposal*)
- A definition that applies in your domain but not in Claude's defaults (*in our organization, "baseline" means the pre-intervention period, not the industry average*)

Essential context should always be in the prompt.

### 2. Useful Context

Useful context is information that helps Claude produce a better output but would not cause a fundamental failure if absent. Including it improves the output; leaving it out produces something serviceable but not optimal.

Examples:
- Tone markers (*this team communicates informally; bullet points preferred*)
- Organizational history (*we moved to this policy in 2023 after a budget constraint*)
- Related documents Claude might find helpful as background

Include useful context when you have it and when it is brief. Cut it when it would add more noise than signal.

### 3. Neutral Context

Neutral context is information that does not change the output in any observable way. Including it adds length and cognitive load without improving the result.

Examples:
- Biographical details about yourself that do not affect the task
- Historical background that the task does not draw on
- Previous conversations that are not relevant to the current request

Neutral context should be cut. It is not harmful on its own, but it contributes to context overload — the condition where the volume of context makes it harder for Claude to locate and prioritize the information that matters `[verify — current as of writing]`.

### 4. Harmful Context

Harmful context is information that actively degrades the output or creates other risks.

**Structural harm:** Disorganized, unlabeled source material. When multiple documents are pasted together without labels or hierarchy, Claude cannot determine which source is authoritative or current. It will weight documents in ways you did not intend — often by structural prominence rather than recency or reliability.

**Signal harm:** Context that contradicts itself. When you provide conflicting information without flagging the conflict, Claude resolves the contradiction silently. You may not notice until the output is wrong in a specific way.

**Privacy harm:** Personal or proprietary information included in a prompt that does not need to be there. Prompts enter Claude's context window; depending on your platform and settings, they may be logged. Data minimization principles apply: include private data only when the task requires it, and only the minimum necessary (NIST AI RMF, 2023).

**False priority harm:** A large, formally structured document alongside a small, informal one. Claude may weight the formal document more heavily even if the informal one is more current or more relevant.

## Context Hygiene: Four Practices

### Practice 1: Label Your Sources

When you include multiple documents or excerpts, label each one before it appears. Tell Claude what each source is, how authoritative it is, and how to treat it relative to the others.

```
Source A [authoritative]: Attached report — use this as the primary source.
Source B [background only]: Attached email thread — for context, do not quote directly.
```

Without labels, Claude assigns weight based on inference. With labels, you assign weight. That is your specification responsibility.

### Practice 2: State Your Assumptions Explicitly

Every prompt contains implicit assumptions. The professional habit is to make them explicit, so Claude does not have to infer them — and so you can check them.

*Assume the reader is familiar with the regulatory framework but not with our internal process.*

*Assume this will be presented in a meeting, not read as a document.*

*Assume the budget figures are final unless I say otherwise.*

Stated assumptions can be checked and revised. Unstated assumptions create silent failures — outputs that are wrong because Claude made a different assumption than you did, and neither of you noticed.

### Practice 3: Declare Exclusions

Tell Claude what it should leave out or ignore. Exclusions are constraints applied to context rather than to the task itself.

*Do not draw on the 2022 version of this report — only the 2025 version I have attached.*

*Ignore the appendices; focus only on the executive summary and Section 3.*

*Do not include any information from the email thread in the output — use it only to understand the timeline.*

Exclusions prevent Claude from using material that is present in the context but should not shape the output. Without them, Claude may use everything it has access to.

### Practice 4: Prune Before You Paste

The single most underused context technique is editing the context before submitting it. Most professionals paste documents whole when they need only a section. They include long email threads when they need only the most recent message. They include full meeting notes when they need only the decisions.

Pruning takes two minutes and often improves output quality more than any other single change.

Ask: *What is the minimum amount of this document Claude needs to do the task?* Then provide that, labeled clearly.

## Before/After: The Project Update

Return to the manager's scenario from the opening.

---

**Weak prompt:**

> [Previous status report 1 — 2 pages]
> [Previous status report 2 — 2 pages]
> [Previous status report 3 — 2 pages]
> [Project charter — 12 pages]
> [Email thread — 40 messages]
>
> Write a paragraph summarizing where we are.

**What Claude produces:** A paragraph that reads like a blend of the charter's formal framing and the status reports' historical narrative. The most recent email thread — which contained the actual current status — is underrepresented because it is the least formally structured document in the set.

**The problem:** The context is unorganized and unprioritized. Claude cannot tell which source carries current status or how to weight them.

---

**Specified prompt (with context hygiene applied):**

> **Task:** Write one paragraph (100–120 words) summarizing where the project stands for a Friday status report.
>
> **Audience:** Senior leadership — they want current status, risks, and next steps. They do not need history.
>
> **Source material:**
> - [Current status] Final message in the email thread (pasted below, 3 paragraphs) — this is the most current information; use it as the primary source.
> - [Background only] The three bullet points from the previous status report summarizing completed milestones (pasted below) — use for continuity, do not repeat in detail.
>
> **Constraints:** Do not include project history or original charter scope. Do not speculate about causes of delays — only report what is stated in the sources.
>
> **Format:** Single paragraph, 100–120 words. Plain prose, no bullets.
>
> **Evaluation criteria:** A senior leader reading this paragraph should know: current status, one active risk, and one concrete next step — all from the sources provided.
>
> ---
>
> [Current status — final email, 3 paragraphs pasted here]
>
> [Background — 3 bullet points from previous status report pasted here]

**What Claude produces:** A focused, 110-word paragraph that draws primarily from the email thread, mentions the completed milestone for continuity, names the active risk, and closes with the next step.

**What you still need to judge:** Whether the risk framing is accurate to your organization's situation, whether "next step" matches what leadership expects to see as actionable, and whether any proprietary details in the email thread should have been omitted before submitting. Claude produced an accurate synthesis of the context you gave it. You verify the judgment and the disclosure.

---

The difference between the two prompts is not the task — both ask for a status paragraph. It is context curation. The second prompt prunes the sources to what is necessary, labels them by authority, and tells Claude which one is current. The output is better not because Claude worked harder but because the specification problem was solved upstream.

## The Privacy Question

A prompt is not a private conversation. Depending on your platform, organization settings, and usage context, prompts may be logged, reviewed, or used in model improvement processes. This matters for context hygiene in a specific way.

Before including real personal data, proprietary documents, client information, or confidential details in a prompt, ask:

- Does this information need to be here for the task to work?
- If so, can I anonymize or abstract it without losing the task-relevant content?
- Does my organization's data policy permit this use?

Data minimization — including only what is necessary — is a principle from information privacy practice (NIST AI RMF, 2023) that applies directly to prompt construction. The fact that Claude is helpful does not mean it is the right place for every piece of information you have.

## Context and Conflicting Sources

When your context includes documents that contradict each other — earlier and later versions of a report, an email that corrects a document, a policy that supersedes an older one — Claude will resolve the conflict, but it will do so without telling you it did `[verify — current as of writing]`.

The professional response is to resolve the conflict explicitly before you prompt:

*The 2025 version of this report supersedes the 2022 version. If they conflict, use 2025.*

Or:

*The final email in this thread corrects the figures in Section 4 of the report. Treat the email as authoritative for those figures.*

Stated conflict resolution is a constraint. Without it, Claude's resolution is silent and may be wrong.

## Common Mistakes

**Pasting entire documents when you need sections.** Cut to what is relevant. Your attention to the task and Claude's attention to the task both improve when the context is specific.

**Mixing sources without labels.** Multiple documents pasted together without labels are treated by Claude as a single undifferentiated block. Label each source before it appears.

**Confusing background with context.** Background is information about the field or topic. Context is information about this task and this situation. A prompt full of field background and no situational context is under-specified.

**Omitting the audience.** Audience is almost always essential context. It changes tone, vocabulary, length, and framing. When the audience is obvious to you, it is not obvious to Claude.

**Including private data unnecessarily.** If the task can be done with anonymized or abstracted information, use that instead.

**Trusting that more is better.** It is not. More context adds noise as well as signal. Curated context outperforms comprehensive context.

## Try This

**Exercise 1 (Context audit).** Take a prompt you submitted recently that included source material. Map each piece of context against the four types: essential, useful, neutral, harmful. What would you cut? What would you label?

**Exercise 2 (Prune and label).** Take a document you regularly paste into Claude prompts. Select only the section that the task actually draws on. Label it. Submit both versions — full document and curated section — on the same task. Compare the outputs. What changed?

**Exercise 3 (Audience test).** Take a prompt that produced an output too technical (or too basic) for its intended reader. Add one sentence naming the audience and their knowledge level. Resubmit. Measure the difference.

**Exercise 4 (Conflict resolution).** If you have ever given Claude two sources that might conflict, write the explicit conflict-resolution instruction you should have included. What hierarchy did you actually intend?

## What Would Change My Mind

This chapter argues that curated, labeled, prioritized context produces better output than comprehensive, unprioritized context dumps. If strong evidence showed that model improvements had largely solved the prioritization problem — that current Claude reliably identifies authoritative sources and ignores irrelevant ones without labels — the curation argument would weaken for most routine tasks. The privacy and data minimization argument would remain regardless of model capability, since it is about appropriate use, not output quality.

## Still Puzzling

The right level of context specificity is not constant across task types. For creative and exploratory tasks, openness in context may produce more useful outputs than tight curation. The discipline of context hygiene probably needs to be calibrated to the task's tolerance for inference versus specificity. A general rule — curate for professional tasks with defined success criteria, leave room for inference for exploratory tasks — seems right but has not been rigorously tested.

## Bridge

You now know what goes in a prompt (Chapter 1) and how to curate the information Claude needs to do the task (this chapter). The next chapter turns to the component that is easiest to forget and most expensive to omit: constraints. What should Claude not do? What should it refuse to invent? Where should it stop?

---

## Sources Used

- Anthropic. (2025). *Prompt engineering overview and long-context guidance*. https://docs.anthropic.com/
- Anthropic. (2025). *Prompting best practices*. https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- NIST. (2023). *AI Risk Management Framework (AI RMF 1.0)*. National Institute of Standards and Technology. [Data minimization and privacy principles.]
- Information foraging theory. (Pirolli & Card, 1999, and subsequent literature on cognitive load and document relevance.)
- Technical communication literature on audience analysis and purpose-driven writing.
- Documentation design literature on source hierarchy and labeling.
- Research on retrieval-augmented generation (RAG) and long-context model behavior (various, 2023–2025).

---

*Tags: #context #context-hygiene #source-labeling #assumptions #exclusions #privacy #data-minimization #audience #specification*
