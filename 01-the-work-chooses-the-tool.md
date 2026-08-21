# Chapter 1 — The Work Chooses the Tool

## Opening Scene

A program manager at a healthcare nonprofit has been using Claude every day for three months. She asks it to summarize meeting notes, draft emails, outline reports, and explain policy language. She is good at it. She trusts the outputs. She also trusts them a bit too uniformly.

On a Monday morning she pastes in a set of client case summaries — de-identified, she believes — and asks Claude to identify common patterns so she can write a needs-assessment section for a grant application. The output is excellent. The prose is clear. The patterns ring true.

Two things she did not ask: whether pasting those summaries into a chat interface was appropriate under her organization's data policy. And whether the output was based on her summaries or on plausible generalizations from Claude's training. She cannot tell from the text alone.

She has not asked the wrong questions of Claude. She has not asked them at all. She reached for the familiar surface before she asked what the task actually required.

This chapter gives you the questions to ask first.

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to classify any task before choosing a Claude surface. You will use five routing dimensions — output type, context source, risk, reversibility, and verification path — to decide whether the work belongs in Claude AI, Claude Code, Claude Cowork, or human-only territory.

The goal is not a rigid lookup table. It is a fast diagnostic habit: before you open a Claude window, spend sixty seconds on the five questions. That minute is the difference between a useful partner and a confident mistake.

---

## The Decision Rule

The central principle of this chapter is simple: **the work chooses the tool, not the other way around**.

This sounds obvious. It is regularly violated. Users choose the Claude surface that is closest at hand (chat) or the one they recently read about (Code) or the most powerful-seeming option. They ask "can Claude do this?" instead of "what does this task actually require?"

Parasuraman, Sheridan, and Wickens (2000) demonstrated that this pattern — choosing automation by convenience rather than fit — is not specific to Claude users. It is a general feature of human-automation interaction. People delegate to the level they can access rather than the level that matches the task's demands on information, analysis, decision, and action.

The remedy is not caution about AI. The remedy is task classification before tool selection.

---

## The Five Routing Dimensions

Before choosing a Claude surface, classify the task on five dimensions. Each question takes fifteen seconds. Together they take less than a minute.

### 1. Output Type

**What does success look like as an object?**

Text (prose, email, explanation, critique) routes to Claude AI chat. Code, a script, a diff, or a technical document tied to a repository routes to Claude Code. A structured file, assembled report, organized folder, or multi-document synthesis routes to Claude Cowork. A judgment, a decision, an ethics call, or a final signature routes to human-only.

This dimension eliminates wrong surfaces immediately. If you want a diff across a codebase, chat cannot give you one. If you want an assembled grant report from a folder of files, chat will not find those files unless you paste them in yourself.

### 2. Context Source

**Where does the necessary information live?**

If the context is in your head, your clipboard, or a few pasted documents, Claude AI chat is the right surface. If the context lives inside a software repository — functions, tests, imports, version history — Claude Code is the right surface. If the context lives in a folder of files, a connected calendar, a linked spreadsheet, or a local application, Claude Cowork is the right surface.

A task with context in the wrong place for the surface you chose will fail not because Claude is incompetent, but because it cannot see what it needs.

### 3. Risk

**What is the cost of being wrong?**

Risk is multidimensional. A task can be low technical difficulty and high privacy risk. It can be low privacy risk and high reputational risk. It can be easy to generate and impossible to un-send. The NIST AI Risk Management Framework (NIST, AI RMF 1.0, 2023) treats risk across four modes: governing risk awareness, mapping it to specific uses, measuring it concretely, and managing it actively. For individual users, the mapping step is the one most often skipped: what specific harm would follow from this output being wrong?

The healthcare program manager's case notes scenario illustrates the gap. The technical difficulty was low. The privacy risk was potentially significant. She did not map the risk before she chose the surface.

The routing question is: Does the risk level for this task match the supervision I can realistically apply? If the risk is high and the verification path is weak, the task is not ready for delegation.

### 4. Reversibility

**Can the action be undone?**

A wrong draft in a chat window can be discarded. A wrong email sent to a hundred people cannot be recalled. A wrong code change that passed review can be reverted; a wrong code change that corrupted a database may not be. A wrong file rename in a Cowork session can be undone if you required a dry run and a before/after manifest; it cannot be undone if you approved without reviewing.

Endsley and Kiris (1995) identified what they called the "out-of-the-loop" problem: when automation handles enough of the routine work, the human user gradually loses the situational awareness needed to catch exceptions. The lower the reversibility of a task, the more actively the human needs to stay in the loop before and during Claude's work — not only after.

Irreversible tasks require explicit approval gates. They belong in surfaces with clear approval steps (Code, Cowork) or in human-only territory. They do not belong in unreviewed chat sessions.

### 5. Verification Path

**How will you know the output is right?**

This is the most important question. If you cannot describe a concrete verification method before you delegate, the task is not ready for delegation.

Concrete verification paths include: comparing a summary against the original document; running the suggested code against tests; checking extracted citations against a database; having a colleague review the draft; applying a rubric you defined in advance; auditing a file manifest against the folder contents.

Vague verification paths include: "it looks right," "the language sounds appropriate," "Claude seemed confident," "the structure makes sense." These are fluency checks, not verification checks. Amershi et al. (CHI 2019) treat this as a first-order design requirement for AI systems: users must have the ability to determine when the system is wrong and correct it. If you do not have that ability for a given task, that task should not be delegated or should be delegated to a surface where verification is easier.

---

## The Routing Table

Applied together, the five dimensions produce routing decisions. Here is the same framework as a worked table:

| Task | Best surface | Why | Human gate |
|---|---|---|---|
| Brainstorm research angles for a grant proposal | Claude AI | Conversational, ideation, no file action needed; context is in the conversation | Verify all leads externally; own the research question |
| Fix a failing test in a software repository | Claude Code | Context lives in the repo; output is a code change; verification uses tests and diff | Review plan, inspect diff, confirm test results before approving |
| Organize and rename project files across a shared folder | Claude Cowork | Needs folder access and file actions; multi-step | Require dry run first; review before/after manifest; approve explicitly |
| Draft a follow-up email from notes pasted into chat | Claude AI | Conversational output; context is in the chat window | Review tone, facts, and recipient list before sending |
| Assemble a report from twelve documents in a local folder | Claude Cowork | Context lives in files; output is a multi-source document | Verify every assembled claim against the source files |
| Decide whether to disclose a data breach | Human-only | High stakes, legal, ethical; Claude can inform but not decide | Human owns the decision and its consequences |
| Summarize a research paper uploaded to chat | Claude AI | Context is the uploaded document; output is text | Compare summary claims against the original paper before using |
| Clean and analyze a dataset using a Python script | Claude Code | Needs a file, a script, and a reproducible execution path | Review the script before running; verify output against known subset |

The table is illustrative, not exhaustive. The skill is not memorizing the table but applying the five dimensions quickly to tasks the table does not include.

---

## The Routing Diagram

If you prefer a sequential process, here is the routing decision as a flow:

```
What is the task output?
    → Text/prose/draft → Claude AI
    → Code/diff/script → Claude Code
    → File/report/folder operation → Claude Cowork
    → Decision/judgment/ethical call → Human-only

Where is the necessary context?
    → In conversation or pasted documents → Claude AI
    → In a code repository → Claude Code
    → In local files or connected apps → Claude Cowork

What is the risk if the output is wrong?
    → Low risk, reversible → proceed with appropriate surface
    → High risk or irreversible → require explicit approval gate or move to human-only

Is there a concrete verification path?
    → Yes, described in advance → proceed
    → No → do not delegate; redesign the task first
```

---

## Worked Walkthrough: A Grant Report

Here is the full routing decision for the program manager's grant report scenario from this chapter's opening.

**The task in full:** Produce a needs-assessment section for a grant application. Source material includes: twelve months of meeting notes in a shared folder, a program outcomes spreadsheet, and three partner-organization PDFs.

**Step 1 — Output type.** A structured report section, assembled from multiple files. This is not pure prose generated from conversation; it is synthesis across documents.

**Step 2 — Context source.** Files in a shared folder and local PDFs. Not in the conversation window.

**Step 3 — Risk.** The grant is public-facing; errors are reputationally consequential. No sensitive client data should be included (the program manager resolves to use program-level summaries only, not case notes).

**Step 4 — Reversibility.** The draft is reversible; the submitted grant application is not. Approval before submission is a hard requirement.

**Step 5 — Verification path.** Every outcome claim in the needs assessment will be traced to a source document. The program manager will keep a footnote log during drafting and check each claim before submission.

**Routing decision:** Claude Cowork, with a bounded folder (grant files only), an explicit task plan reviewed before execution, and a source manifest audited at the end.

What changed from the opening scene: the context is right (files, not pasted notes); the risk is managed (no case summaries); the verification path exists (source log); the sensitive data problem is resolved before delegation rather than discovered after.

---

## The Human Gate

The human gate for this chapter is the five routing questions. They are the one thing Claude cannot do for you. Claude will accept whatever task you give it on whatever surface you give it on. It will not tell you that the task belongs somewhere else. It will not ask whether you have a verification plan. It will not flag that the surface you chose cannot see the context you need.

You ask the routing questions. You own the routing decision. Everything downstream depends on whether you asked them before you typed.

---

## Common Mistakes

**Asking "can Claude do this?" instead of "should Claude do this on this surface?"** Capability is not fit. Many tasks Claude can technically perform are tasks where the surface mismatch, risk level, or missing verification path makes delegation costly.

**Skipping the verification path question.** This is the most commonly skipped question, and the most consequential. A task with no verification path is a task where being wrong is undetectable. That is not a Claude problem; it is a workflow design problem. Redesign the task until you can describe how you will check the output.

**Treating reversibility as binary.** A draft that will influence a decision is not fully reversible even if you can delete the text. Consider the downstream consequence, not just the document.

**Using risk labels loosely.** "Low risk" is often assumed rather than assessed. Spend thirty seconds naming the specific harm before deciding the risk is low.

**Routing by familiarity.** The fact that you use Claude AI chat every day does not make it the right surface for every task. Familiarity is a reason to be watchful, not a routing argument.

---

## Try This

**Exercise 1 — Five-Question Sprint.** Pick three tasks from your current work. For each task, write one sentence answering each of the five routing questions. Then write the routing decision and one sentence explaining why. Do this before you open a Claude window, not after.

**Exercise 2 — The Un-Delegatable Task.** Identify one task in your own work that you have delegated to Claude — or are tempted to — that fails the verification path test. Describe concretely why there is no verification path. Then redesign the task so that it does have one, or write out why the task should stay human-owned.

**Exercise 3 — Mismatch Audit.** Look at your Claude conversation history from the past week. For each session, identify which of the five routing dimensions you did not explicitly check before starting. What would you route differently?

---

## What Would Change My Mind

The routing framework rests on human factors and HCI research developed for industrial automation, cockpit systems, and medical devices — not for AI language models. If task-specific empirical research on Claude routing decisions showed that knowledge workers achieved better outcomes without a pre-routing diagnostic step, the specific framework would need revision. The underlying principles — match tool to task, require verification paths, manage reversibility explicitly — rest on evidence solid enough that I would need strong contrary evidence to abandon them. But the specific five-question format is a pedagogical choice, not a derived law.

---

## Still Puzzling

The boundary between Claude AI chat with file creation and Claude Cowork as a file agent is shifting as of this writing [verify — current as of writing]. Anthropic's own documentation notes that chat Claude can create and edit files in some contexts (Anthropic, "Create and edit files with Claude," Help Center). This means the routing decision for some file-creation tasks is genuinely ambiguous. The principle — classify by output type, context source, and verification need — remains stable even when the product boundary shifts. If you are unsure which surface applies, start with the most conservative option (chat) and upgrade to a file-action surface only when the task genuinely requires folder access or multi-step file operations.

---

## Bridge to Chapter 2

You have now routed the work. Chapter 2 enters the surface where most knowledge workers spend most of their time: Claude AI as a thinking partner. The chapter teaches what "thinking partner" actually means in practice — the difference between asking Claude to think for you and using Claude to pressure-test your own thinking.

---

## Sources

Amershi, S., Weld, D., Vorvoreanu, M., Fourney, A., Nushi, B., Collisson, P., Suh, J., Iqbal, S., Bennett, P., Inkpen, K., Teevan, J., Kikin-Gil, R., and Horvitz, E. "Guidelines for Human-AI Interaction." CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/

Anthropic. "Claude Code overview." Claude Code Docs. https://docs.anthropic.com/en/docs/claude-code/overview

Anthropic. "Let Claude use your computer in Cowork." Claude Help Center, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork

Anthropic. "What are artifacts and how do I use them?" Anthropic Help Center. https://support.anthropic.com/en/articles/9487310-what-are-artifacts-and-how-do-i-use-them

Anthropic. "Create and edit files with Claude." Anthropic Help Center. https://support.anthropic.com/en/articles/12111783-create-and-edit-files-with-claude

Bainbridge, L. "Ironies of Automation." *Automatica*, 19(6), 775–779, 1983. https://doi.org/10.1016/0005-1098(83)90046-8

Endsley, M. R. and Kiris, E. O. "The Out-of-the-Loop Performance Problem and Level of Control in Automation." *Human Factors*, 37(2), 381–394, 1995. https://ouci.dntb.gov.ua/en/works/4OzxyWb7/

NIST. "Artificial Intelligence Risk Management Framework (AI RMF 1.0)." NIST AI 100-1, 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10

Parasuraman, R., Sheridan, T. B., and Wickens, C. D. "A Model for Types and Levels of Human Interaction with Automation." *IEEE Transactions on Systems, Man, and Cybernetics*, 30(3), 286–297, 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
