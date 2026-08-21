# Chapter 3 — Prompting as Specification

## Opening Scene

You send Claude a message: "Can you improve this introduction?"

Two minutes later you have a rewritten paragraph. It is polished. The sentences are shorter. The word choice is cleaner. It is also completely wrong for your audience, your journal, and the argument you are making — because Claude had no idea what "better" meant to you.

This is the chapter's central failure. Not Claude's failure. Yours. You did not write a specification; you made a wish. And language models are not genies. They are highly capable collaborators with no context about your purpose, your constraints, your audience, or what success looks like. They will do something — and it will be fluent, and it will be wrong.

This chapter teaches you to write prompts that work the way work orders work: specific enough that another capable person could follow them, and specific enough that you can tell whether the result is right.

---

## What This Chapter Lets You Do

After this chapter you will be able to:

- Identify the six components of a well-specified prompt
- Convert a vague request into an inspectable work order
- Use structure (plain headings or XML tags) to separate task, context, constraints, source material, output format, and evaluation criteria
- Diagnose a failing prompt before rewriting it
- Know when to ask Claude to elicit missing requirements before producing an answer

---

## The Core Concept: A Prompt Is a Work Order

The research community has spent years studying prompting as a family of techniques for adapting language models to tasks through input design (Liu, Yuan, Fu, Jiang, Hayashi, and Neubig 2023). The practitioner version of that insight is simpler: **a prompt is a specification of work.**

A good specification tells the contractor — human or AI — four things:

1. What to do
2. What to use
3. What to avoid
4. What the finished work should look like

When non-experts write prompts, they tend to under-specify goals, rely on trial and error, and lack a design vocabulary for iteration (Zamfirescu-Pereira, Wong, Hartmann, and Yang 2023). That is not a character flaw. It is because they have never been taught to think of prompting as specification writing. They have been taught to think of it as search — type the thing you want, see what appears.

Search and specification are different. Search retrieves. Specification instructs. Claude is not a search engine. It will construct an answer based on whatever frame the prompt implies. Give it a vague frame, get a plausible-but-wrong answer. Give it a clear frame, get something you can inspect and use.

Anthropic's own documentation makes this explicit: Claude is like a capable new collaborator who needs explicit instructions, context, and success criteria (Anthropic, "Be clear, direct, and detailed"). That is the operating premise for this entire chapter.

---

## The Six Components of a Well-Specified Prompt

Every effective prompt answers six questions. Not every prompt needs to answer all six in depth — a simple task may need only three. But knowing the six helps you diagnose why a prompt failed.

| Component | Question it answers | Example |
|---|---|---|
| **Task** | What should Claude do? | "Evaluate this methods section for logical coherence." |
| **Context** | What does Claude need to know? | "This is for a CHI-style HCI conference paper." |
| **Source material** | What evidence should Claude use? | "Use only the pasted abstract and methods section below." |
| **Constraints** | What must Claude avoid? | "Do not add citations. Do not invent findings." |
| **Output format** | What should the answer look like? | "Return a table: issue, severity, suggested fix." |
| **Evaluation criteria** | How will success be judged? | "Flag causal overclaims and vague operationalizations." |

The most commonly omitted components are **source material**, **constraints**, and **evaluation criteria**. When these are missing, Claude fills the gap with its best guess — which may be reasonable for a general reader and wrong for your specific situation.

---

## Worked Workflow: Revising a Research Methods Section

Here is how the same task looks before and after specification.

### Before

> "Can you improve the methods section of my paper?"

Claude will improve it by its own standard of clarity, academic prose, and logical completeness. It does not know your journal's statistical reporting conventions, your sample constraints, or the reviewer concern you are anticipating.

### After

```xml
<task>
Evaluate the methods section below before rewriting anything.
</task>

<context>
This is a short empirical paper for a CHI-style HCI venue. The reviewers
will expect clear operationalization, appropriate sample justification,
and honest scope statements.
</context>

<source>
[paste your methods section here]
</source>

<constraints>
Do not add citations not present in the draft.
Do not invent new variables or findings.
Do not rewrite yet — diagnosis first.
</constraints>

<output>
Return three things:
1. A bullet-point diagnosis (issue, severity: high/medium/low, reason).
2. A prioritized revision plan (what to fix first and why).
3. An optional revised opening paragraph only if the opening is unclear.
</output>

<criteria>
Flag: causal overclaims, vague operationalizations, unjustified sample size,
missing scope limitations.
</criteria>
```

The output of the second prompt is inspectable. You can check whether the issues it flags match your concerns. You can disagree with the severity ratings. You can accept or reject the revision plan. None of that is possible if Claude has already rewritten the section without a diagnosis.

Note the structural feature: the prompt asks for **diagnosis before rewriting**. This is one of the most useful habits in practice. Outputs that include explicit reasoning or plans are easier to verify than outputs that simply produce an artifact. Wei et al. (2022) established that task decomposition and intermediate structure can improve performance on multi-step tasks; the practitioner version is that *you* also benefit from seeing the reasoning before accepting the result.

---

## Why XML? Why Not Just Write It Out?

You do not have to use XML tags. For a simple prompt with one or two components, plain prose or Markdown headings work fine.

XML tags become useful when your prompt has multiple sections and the model might confuse them (Anthropic, "Use XML tags to structure your prompts"). A long source document pasted after a long instruction, for example, can blur the line between the source and the task. XML tags make the boundary visible to Claude and to you.

The rule of thumb: use plain prose for short prompts. Use XML or Markdown headings when your prompt has more than three components, contains pasted source material, or has multiple output requirements.

Neither format is magic. The structure helps, but it helps because it imposes clarity on your thinking, not because Claude has a special XML mode. If your specification is muddled, tags do not fix it. [verify — current as of writing]

---

## The Requirement-Gathering Conversation

Not every task starts with a complete specification. Sometimes you do not know exactly what you need until you start. In that case, the right first prompt is not a work order — it is a requirements conversation.

> "I need help evaluating a research methods section, but I am not sure what angle is most useful. Ask me three questions that will help you understand what I actually need, then propose a plan."

This uses Claude as a collaborator in the specification process itself. The result is a back-and-forth that surfaces your real goal before Claude starts producing artifacts.

This is not a replacement for specification — it is how you build one when you do not have all the components yet. Once the requirements are clear, you write the prompt that uses them.

---

## The Human Gate: What to Verify

The specification frame clarifies what you still own.

**You must supply:**
- The purpose — why this work matters and what it will be used for
- The source constraints — what evidence is valid, what is not
- The evaluation criteria — what "good" means in your institutional, scientific, or professional context
- The final decision — whether the output is acceptable for use

**Claude cannot supply:**
- Knowledge of your organization's unstated norms
- Your discipline's peer-review standards unless you describe them
- A sense of whether the framing is ethically appropriate for your context
- Verification that its own output is correct

**How to inspect a prompt output:**

1. Check whether it answered the task as specified, or drifted to something adjacent.
2. Check whether it stayed within the source material or added claims from elsewhere.
3. Check whether it violated a constraint.
4. Check the output format — is the table complete? Are the categories right?
5. Check each flagged issue against your own judgment. Does it match what a human reviewer would catch?

If the output passes these checks, it is worth using. If it fails, diagnose which component of the specification was under-specified, revise, and iterate.

---

## Common Mistakes

**1. Asking for a finished artifact before asking for a diagnosis.**
Request diagnosis and plan before the rewrite. It is easier to correct a plan than a completed draft.

**2. Using role prompts as a substitute for a task specification.**
"Act as an expert researcher" tells Claude to adopt a tone, not to do specific work. Role prompts can supplement a specification but should not replace it. Specify the task first; add role context if the register or perspective matters.

**3. Treating XML or Markdown tags as magic.**
The structure helps you and Claude separate components. It does not fix an under-specified task or supply missing source material.

**4. Asking Claude to "make it better" without defining better.**
Every evaluation criterion is implicit in your prompt. Make it explicit.

**5. Accepting a fluent output as a correct one.**
The research on non-expert prompting is clear: end users tend to accept outputs that look plausible rather than testing them against ground truth (Zamfirescu-Pereira et al. 2023). A smooth sentence is not evidence of a correct claim.

**6. Writing a massive template before understanding why each part exists.**
Learn the six components from concrete failures, not from memorizing a template. Use the template once you understand what each component prevents.

---

## Try This

**Exercise 1 (10 minutes): Diagnose a failing prompt.**

Take a prompt you have used this week that produced an unsatisfying result. Write down:
- What the output got wrong.
- Which of the six components was missing or vague.
- One sentence that would repair each missing component.

Then rewrite the prompt using those repairs and compare the outputs.

**Exercise 2 (15 minutes): Specification before artifact.**

Choose a writing task you have coming up — a report section, an email, a presentation outline. Instead of asking Claude to draft it, write a specification first:
- Task: What exactly should Claude evaluate or draft?
- Context: What is the audience, venue, and purpose?
- Source material: What can Claude use, and what is off-limits?
- Constraints: What must it not do?
- Output format: What structure should the output take?
- Evaluation criteria: What would a good output get right that a mediocre one misses?

Send the specification. Inspect the output against your criteria. Note which criteria revealed a gap.

**Exercise 3 (hands-on, any length): Requirement-gathering first.**

Pick a task you have not fully defined yet. Ask Claude to ask you three questions that will help it understand what you need. Answer the questions. Let Claude propose a specification. Review the specification before asking for any output. This is the discipline of not producing before diagnosing.

---

## What Would Change My Mind

This chapter treats prompting as specification because that frame is the most useful for the target reader — a knowledge worker who writes vague prompts and gets vague results. If evidence showed that detailed specifications consistently underperformed iterative conversational prompting for the kinds of tasks knowledge workers actually do, I would weight iteration more heavily.

I would also revise the XML-tags recommendation if Anthropic significantly changed Claude's ability to parse multi-section prose without explicit markers. [verify — current as of writing]

---

## Still Puzzling

The research on end-user prompting failures (Zamfirescu-Pereira et al. 2023) studies simple tasks in a lab context. More evidence is needed on how specification quality scales across complex, multi-session, high-stakes knowledge work. It is also not clear how much specification quality matters as models improve their ability to infer intent from thin context. The disciplinary answer is: it still matters that *you* can inspect the output, and a specification gives you the benchmark for doing that.

---

## Bridge to Chapter 4

Specification is the discipline that makes every other Claude workflow inspectable. The next chapter takes that discipline into a different environment: a codebase. Claude Code is not a chat assistant that writes code. It is an agent that operates on your files, runs commands, and requires a different kind of supervision. The specification habits from this chapter carry forward — but the verification tools change.

---

## Sources Used

- Anthropic, "Be clear, direct, and detailed," Claude Docs. https://anthropic.mintlify.app/en/docs/build-with-claude/prompt-engineering/be-clear-and-direct
- Anthropic, "Use XML tags to structure your prompts," Claude Docs. https://anthropic.mintlify.app/en/docs/build-with-claude/prompt-engineering/use-xml-tags
- Pengfei Liu, Weizhe Yuan, Jinlan Fu, Zhengbao Jiang, Hiroaki Hayashi, and Graham Neubig, "Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing," *ACM Computing Surveys*, 2023. https://arxiv.org/abs/2107.13586
- J. D. Zamfirescu-Pereira, Richmond Y. Wong, Bjoern Hartmann, and Qian Yang, "Why Johnny Can't Prompt: How Non-AI Experts Try (and Fail) to Design LLM Prompts," *CHI 2023*. https://people.eecs.berkeley.edu/~bjoern/papers/zamfirescu-johnny-chi2023.pdf
- Jason Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models," *NeurIPS 2022*. https://proceedings.neurips.cc/paper_files/paper/2022/hash/9d5609613524ecf4f15af0f7b31abca4-Abstract.html

---

*Tags: #prompting #specification #workflow #claude #practitioner #human-gate*
