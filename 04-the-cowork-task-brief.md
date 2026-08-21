# Chapter 4 — The Cowork Task Brief

## TL;DR

- A task brief is not a longer prompt. It is the specification that makes your intent executable and inspectable before Cowork does any work.
- A good brief has seven fields: objective, allowed sources, exclusions, output artifact, decision rules, review checkpoints, and stop conditions.
- Vague instruction plus broad access is how Cowork produces fluent, plausible, wrong output.
- The brief is also your verification standard: if the output does not match the brief, the work is not done.

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to:

- Write a Cowork task brief using a reusable seven-field template.
- Identify what is missing from a vague instruction and why the missing elements matter.
- Apply the brief template to a report task and an extraction task with worked examples.
- Use the brief as the standard against which you review the plan Cowork proposes.

---

## Opening Scene

Here is a real type of exchange that happens when professionals start using Cowork. Two people, same folder of program reports, same basic need.

**Version one.** Marcus types: "Turn these program reports into a summary document for the board."

Cowork produces something. It is clean, organized, seven pages. Marcus sends it. The board chair replies: "This is a good start but you've mixed the 2024 data with the 2023 data, the financial figures don't appear, and I see nothing about the pilot sites." Marcus had not specified the year range, had not mentioned financial figures, and had not said anything about pilot sites. Neither had the brief.

**Version two.** Priya types a task brief — the same seven-field document this chapter teaches — before Cowork writes a single word. The plan Cowork proposes covers the right years, separates pilot sites from full sites, and includes a note that financial figures were not present in the source folder and need to be added. Priya spots the gap before the draft exists. She uploads the financial tables and approves the revised plan. The board gets one version.

The difference between Marcus's experience and Priya's is not AI quality. It is specification.

---

## Why a Brief Is Not Just a Longer Prompt

Non-expert users trying to direct AI tools tend to rely on informal, conversational instructions and then troubleshoot failures after the fact rather than specifying requirements before (Zamfirescu-Pereira et al. 2023). The result is iterative trial-and-error — which wastes time and, in a Cowork context where files are being touched, can create a trail of partial outputs, modified files, and inconsistent drafts.

A task brief is a different kind of document. It defines the work before Cowork plans. It makes the delegation inspectable. Another person — or Cowork itself — should be able to read the brief and know what Cowork is allowed to do.

This is also why a brief serves as a verification standard. If you have specified "use only files from the Q3 folder" and the output includes material from Q1, you have a clear, brief-based reason to reject the output and redirect. Without a brief, you are evaluating against an informal, unrecorded intent that may shift as you read.

Prompting research confirms the principle: specific tasks, explicit context, clear constraints, and stated output requirements produce substantially better results than general instruction (Anthropic, "Be clear, direct, and detailed"; Liu et al. 2023). The brief is the form that captures all of those elements for a multi-step agentic task.

---

## The Seven Fields

### 1. Objective

What outcome matters? This is the one-sentence purpose of the task — not the process, not the output artifact, but the purpose.

Good: "Produce a board-ready summary of Q3 and Q4 2024 program results for an audience that has not read the original reports."

Not good: "Make a summary."

The objective anchors everything else. If a step in the plan does not serve this objective, it should not be in the plan.

### 2. Allowed Sources

What may be used? Name the folder, the specific files, or the set of documents Cowork is permitted to draw from. If you scoped access correctly in Chapter 3, this should match the access surface you granted.

Good: "Use only the files in the folder /tasks/board-summary-june. Do not use files from any other location."

This field prevents Cowork from pulling in adjacent material — draft documents, templates, historical files — that happened to be in the same account.

### 3. Exclusions

What must be ignored? This field is not redundant with allowed sources. Even within a clean folder, there may be files that should not be source material: outdated drafts, a template file, a notes document with internal commentary, a file containing someone's personal information.

Good: "Do not use the file marked DRAFT or any file with 'template' in the name. Do not include information from personal staff notes."

Exclusions are also where you prohibit actions: "Do not send, submit, or publish anything. Do not modify original files."

### 4. Output Artifact

What file or artifact should exist when the task is done? Name the format, approximate length or structure, and the file or document name if you have one.

Good: "Produce a Word document named board-summary-june-2024.docx with a one-page executive summary followed by section-by-section results, three to five pages total."

Cowork should not decide what format the output takes. You should. Output format is a commitment, not a preference.

### 5. Decision Rules

How should Cowork handle ambiguous situations? This field prevents silent inference. If a source file covers both 2023 and 2024 and the task is 2024-only, what should happen? If two source files give different figures for the same metric, what should Cowork do?

Good: "If a source document covers multiple years, use only the 2024 sections. If two documents give conflicting figures for the same metric, flag the conflict in a separate note rather than choosing one."

Without decision rules, Cowork makes judgment calls that you will not know it made until you find the error.

### 6. Review Checkpoints

What must be checked before the task continues? This field inserts human judgment at the right moments — before bulk changes, before a draft is finalized, before a file is written.

Good: "Before writing the draft, show me the source inventory and the proposed outline. I will approve or redirect before you proceed."

Review checkpoints are not bureaucratic friction. They are the places where your domain knowledge — which sources are reliable, which figures are contested, which framing is appropriate — enters the workflow. Cowork does not have that knowledge. You do.

### 7. Stop Conditions

When should Cowork pause and ask rather than continue? What situations require your input before any further action?

Good: "If source files are missing or incomplete, stop and tell me what is missing before proceeding. Do not attempt to fill gaps with external sources or inferences. If you encounter a document you cannot read, flag it and wait."

Stop conditions are especially important for file tasks, extraction tasks, and any task where a missing piece could compound through the output. They give you a chance to supply what is needed rather than receiving a plausible-but-wrong output that filled the gap silently.

The research supports building this into agent behavior: proactive clarification before acting reduces errors compared to silent inference (Ask-before-Plan, EMNLP 2024).

---

## The Template

Copy this for every Cowork task:

```
Objective:
[One sentence: what outcome matters?]

Allowed sources:
[Name the folder, files, or scope. Match the access you granted.]

Exclusions:
[What must not be used, touched, modified, sent, or submitted?]

Output artifact:
[Format, name, approximate structure or length.]

Decision rules:
[How should Cowork handle gaps, conflicts, and ambiguous cases?]

Review checkpoints:
[What must I see and approve before the task continues?]

Stop conditions:
[When must Cowork pause and ask rather than proceed?]
```

---

## Worked Example — Board Summary Report

Here is the full brief Priya wrote for the board summary from the opening scene:

```
Objective:
Produce a board-ready summary of Q3 and Q4 2024 program results for an 
audience that has not read the original reports and does not need full 
program detail.

Allowed sources:
Files in /tasks/board-summary-june only. Do not use any other folder, 
cloud document, or web source.

Exclusions:
Do not use any file with "draft" or "template" in the name. Do not include 
staff personal notes. Do not send, publish, submit, or modify any original 
file.

Output artifact:
Word document named board-summary-june-2024.docx. One-page executive 
summary, then section-by-section results organized by program area. Three 
to five pages total.

Decision rules:
Use only 2024 data sections even if a source covers multiple years. If two 
sources give different figures for the same metric, flag the discrepancy in 
a note — do not choose. If financial figures are absent from the source 
folder, note the gap and leave a placeholder.

Review checkpoints:
Before drafting: show me the source inventory (list of files used), the 
proposed document structure, and any gaps. I will approve or redirect.

Stop conditions:
Stop and tell me if: required source files are missing or unreadable; 
financial data cannot be found; sources conflict on a core program result. 
Do not proceed past the inventory without my approval.
```

Note what this brief does not do. It does not specify prose style, section headings, or word count beyond a range. Those are execution details. The brief covers scope, access, output, decision authority, and supervision. The rest is Cowork's job.

---

## Worked Example — Meeting Notes Extraction

The brief template works equally well for extraction and summarization tasks. Here is a brief for converting a transcript into a structured meeting summary:

```
Objective:
Convert the attached meeting transcript into a structured summary for the 
project file: decisions, action items with owners, open questions.

Allowed sources:
The attached file transcript-may28.txt only. No other source.

Exclusions:
Do not include informal side comments or personal remarks. Do not include 
anything said "off the record" or as an aside. Do not send the summary 
anywhere; produce a local file only.

Output artifact:
Plain text file named meeting-notes-may28.txt. Sections: Decisions (with 
rationale where given), Action Items (owner, due date, task), Open Questions 
(unresolved items from the agenda).

Decision rules:
If a speaker's name is unclear in the transcript, write "[name unclear]" — 
do not guess. If a due date was discussed but not confirmed, flag it as 
"[date unconfirmed]."

Review checkpoints:
After extraction, show me the full summary before saving. I will review for 
accuracy before the file is written.

Stop conditions:
Stop if the transcript is incomplete or appears cut off. Stop if you 
encounter sections marked confidential. Ask before proceeding.
```

---

## The Human Gate for This Chapter

The brief is where your professional judgment enters the task before any execution begins. Exclusions require you to know which files should not be sources. Decision rules require you to know how conflicts should be handled. Stop conditions require you to know what gaps are unacceptable.

None of those judgments can be made by Cowork. They depend on your knowledge of the work, the audience, the constraints, and the stakes. Writing the brief is not administrative overhead; it is where the human work happens.

The test for a complete brief: could someone else — a colleague, a supervisor, a future-you looking at a log — read this brief and know what Cowork was allowed to do? If yes, the brief is done. If not, keep writing.

---

## Common Mistakes

**Treating the brief as a longer version of a chat prompt.** A brief is a specification, not a conversation opener. Write it before Cowork responds, not as a back-and-forth.

**Omitting exclusions because the folder is clean.** A clean folder still contains files that should not be source material. Exclusions are not about distrust; they are about precision.

**Leaving decision rules blank.** Cowork will fill gaps with its best inference. That inference will not always match your intent, and you will not know it happened.

**Skipping stop conditions for "low-risk" tasks.** Stop conditions catch the problems you did not anticipate. Low-risk tasks can still produce consequential errors if a source file is missing or corrupted.

**Writing the output format as a preference rather than a requirement.** "A short document would be good" is not an output specification. "A Word document, three to five pages, named X, with sections Y and Z" is.

**Reviewing the brief after the plan.** The brief must come first. Once Cowork has produced a plan, you are reviewing a proposal — and it is harder to redirect fundamentally than to write a better brief at the start.

---

## Try This

**Exercise 1 — Brief a task you already know.** Think of a document task you completed manually last month — a summary, a compilation, a report. Write a task brief for it using the seven-field template. Do not run it yet; just write it. When you finish, answer: what would Cowork have gotten wrong without this brief?

**Exercise 2 — Identify what is missing.** Here is a vague instruction:

> "Organize the quarterly reports into a summary. Use the files I uploaded. Make it professional."

List every element this instruction is missing from the seven-field template. Then rewrite it as a complete brief for a hypothetical quarterly report task, using whatever scope, format, and rules seem appropriate to your actual work.

**Exercise 3 — Run it.** Write a task brief for a real Cowork task using the full template. Submit it to Cowork and ask Cowork to produce a plan based on the brief. Before approving the plan, check: does the plan reflect the brief? Are any fields missing from the plan? What has Cowork assumed that the brief did not specify?

---

## What Would Change My Mind

The seven-field brief recommended here errs toward structure. That recommendation would relax if:

- Evidence showed that nontechnical users who write structured briefs do not produce better outcomes than those who use conversational instructions — but Zamfirescu-Pereira et al. (2023) and general prompting research point strongly in the other direction.
- Cowork developed an interactive brief-building process that extracted objective, sources, exclusions, output, decision rules, checkpoints, and stop conditions through a guided conversation before acting. That would shift the form but not the underlying requirements.
- Tasks became reliably low-consequence — no file modification, no external output, no sensitive sources — in which case a lighter brief might be acceptable for a subset of tasks.

For the current product and risk profile, structured briefs remain the right discipline.

---

## Still Puzzling

- How brief a brief is too brief? For a quick extraction from three known files, a full seven-field document may be more friction than value. The research on prompt granularity relative to task complexity is not yet settled.
- How should teams share briefs? If a Cowork brief is a reusable document, what is the right format for storing and sharing brief templates across a team? (Chapter 11 addresses workflow reuse, but brief standardization within teams is an open question.)
- When Cowork asks clarifying questions before acting, is that a sign of a bad brief or good agent behavior? Usually both are true simultaneously.

---

## Bridge to Chapter 5

A well-written brief produces a plan worth reviewing. Chapter 5 teaches you what to look for when Cowork proposes that plan — how to spot missing steps, risky assumptions, and places where the plan would proceed without the verification your brief required. The brief you write in this chapter becomes the standard you hold the plan against in the next one.

---

## Sources Used

- Anthropic, "Be clear, direct, and detailed," Claude Docs. https://anthropic.mintlify.app/en/docs/build-with-claude/prompt-engineering/be-clear-and-direct [verify — current as of writing]
- Anthropic, "Use XML tags to structure your prompts," Claude Docs. https://anthropic.mintlify.app/en/docs/build-with-claude/prompt-engineering/use-xml-tags [verify — current as of writing]
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Zamfirescu-Pereira, J. D., et al. "Why Johnny Can't Prompt: How Non-AI Experts Try and Fail to Design LLM Prompts," CHI 2023. https://people.eecs.berkeley.edu/~bjoern/papers/zamfirescu-johnny-chi2023.pdf
- Liu, P., et al. "Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing," ACM Computing Surveys, 2023. https://arxiv.org/abs/2107.13586
- Microsoft Research, "Guidelines for Human-AI Interaction," CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
- Locke, E. A., and Latham, G. P. "Building a practically useful theory of goal setting and task motivation," American Psychologist, 2002. https://pubmed.ncbi.nlm.nih.gov/12237980/
- NIST AI RMF 1.0, 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
- "Ask-before-Plan: Proactive Language Agents for Real-World Planning," Findings of EMNLP, 2024. https://arxiv.org/abs/2406.12639

---

## AI Wayback Machine

**Grace Hopper** spent decades translating between human intent and machine execution — first with early compilers, then with COBOL, a language designed so that business people, not only engineers, could specify what a computer should do. Her core conviction was that computers should do what humans mean, and that the job of the programmer was to make intent precise enough for the machine to follow without guessing.

A Cowork task brief is the same project at a different level of abstraction.

![Grace Hopper](../images/grace-hopper-w8h.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

**Run this:**

```
Who was Grace Hopper, and how does her work on compilers and COBOL connect 
to the challenge of writing precise task instructions for AI agents? Keep it 
to three paragraphs. End with one question her work raises about where 
human intent stops and machine interpretation begins.
```

→ Search **"Grace Hopper COBOL compiler"** for background.

**Now make the prompt better.** Try one of these:

- Ask how Hopper's distinction between "what you want" and "what you said" applies when an AI agent receives a vague task brief.
- Ask what Hopper might have built if she were designing the specification language for AI workflow agents today.

What changes? What becomes more concrete? What ambiguity stays?
