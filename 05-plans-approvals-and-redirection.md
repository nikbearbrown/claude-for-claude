# Chapter 5 — Plans, Approvals, and Redirection

## TL;DR

- A Cowork plan is a proposal, not a commitment. Review it before any action starts.
- Good plan review checks five things: goal fit, source coverage, risky steps, missing verification, and stop conditions.
- You have four responses to a plan: approve, redirect, pause, or stop. All four are legitimate.
- Clicking approve without understanding the plan is not supervision. It is abdication with extra steps.

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to:

- Read a Cowork plan and identify missing steps, risky actions, and wrong assumptions.
- Use the plan-review checklist to evaluate a plan systematically before approving.
- Write a redirection instruction that revises a flawed plan without starting over.
- Know when to stop a task entirely rather than trying to redirect it.

---

## Opening Scene

The plan looks competent. Cowork has laid out six steps for organizing a grant files folder:

1. Inventory all files in the folder.
2. Identify duplicates and outdated drafts.
3. Propose a naming convention.
4. Apply the convention to all files.
5. Remove duplicates and outdated drafts.
6. Produce a final folder manifest.

Six steps, clear language, logical order. Kenji reads it in about twenty seconds and clicks approve.

Steps 1 through 3 run fine. Step 4 renames every file in the folder without a backup. Step 5 deletes twelve files. The "duplicates" include an earlier application version that was actually different from the final submission — different enough that the program officer will notice if the question comes up later.

Kenji had not noticed that steps 4 and 5 were irreversible and unsequenced from any backup or dry run. The plan looked complete. It was not safe.

The problem was not the plan's format. It was that Kenji had no checklist for what a safe plan includes.

---

## A Plan Is a Proposal, Not Proof

Agentic AI systems, including Cowork, can produce plans that are internally coherent but brittle — plans that handle the expected case well and the unexpected case badly (Liang et al. 2024; "Revealing the Barriers," arXiv 2024). Fluent language is not evidence of correct reasoning about your specific files, your specific constraints, or the specific risks in your workspace.

This matches a well-established principle from automation research: humans must remain engaged enough to intervene (Bainbridge 1983). Automation that is supervised only at the beginning — through a vague approval — and then left alone is automation that will eventually surprise you. The surprise will happen precisely when the unexpected case arises, because that is when the plan's brittleness becomes visible.

The approval step is not a formality. It is where you apply your domain knowledge — the knowledge Cowork does not have — to the specific steps the task requires.

---

## The Plan-Review Checklist

Before approving any Cowork plan, work through these questions:

**Goal fit**
- Does the plan match the objective in my brief?
- Are there steps that are not connected to the objective?
- Has Cowork made assumptions about scope, audience, or priority that I did not specify?

**Source coverage**
- Are the source files or folders named or inventoried in the plan?
- Does the plan respect the allowed sources in my brief?
- Are any exclusions from the brief present in the plan?

**Risky steps**
- Does the plan include any step that modifies, moves, renames, or deletes original files?
- Does the plan include any step that sends, submits, or publishes anything?
- Does the plan include browser interaction, form submission, or account access?
- Are any of these steps irreversible?
- If an irreversible step exists, is there a backup or dry run before it?

**Missing verification**
- Does the plan include a step where I review the output before it is finalized?
- Does the plan include source logging or evidence of what was used?
- Does the plan flag gaps, conflicts, or unresolvable ambiguities?

**Stop conditions**
- Does the plan include points where Cowork will pause and ask rather than proceed?
- Are missing sources or unreadable files handled with a pause rather than a silent workaround?

If any of these checks produces a "no" on a high-stakes item, redirect before approving.

---

## Four Responses to a Plan

The approval step has more options than approve or reject.

**Approve.** The plan is bounded, matches the brief, separates risky steps from reversible steps, and includes verification. Proceed.

**Redirect.** The plan has specific problems — a missing step, a risky sequence, a wrong assumption. Write a correction and ask Cowork to revise the plan before acting. This is the most common response for complex tasks, and it is not a sign that the brief failed. It is the review process working as intended.

**Pause.** The plan reveals something the brief did not cover: a source is missing, an assumption needs clarification, a scope question has emerged. Stop the plan and supply the missing information before asking Cowork to try again.

**Stop.** The task as scoped cannot be completed safely. The plan reveals that the work requires access you should not grant, involves irreversible actions you cannot adequately supervise, or depends on sources you cannot verify. Stop the task and decide whether it belongs in Cowork at all.

Redirection is not failure. It is what plan review is for. Plans that need no redirection are either very simple or very well-briefed (Parasuraman, Sheridan, and Wickens 2000).

---

## Redirection in Practice

When a plan needs revision, a redirection instruction has three parts: what to keep, what to change, and the new constraint.

Here is the redirection Kenji should have written for the folder-organization plan:

> "Do not act yet. Revise the plan as follows: after step 3, add a backup step — copy all files to a staging folder before any renaming or deletion. After the backup, run a dry run that lists what would be renamed and what would be deleted, and show me that list before any file is touched. Do not delete anything without my explicit approval after reviewing the dry run list."

This instruction does not restart the task. It adds the missing safety structure to a plan that was otherwise reasonable.

The research on language agent planning confirms that redirection instructions that are specific — naming the step to change and the replacement requirement — produce better results than general corrections like "be more careful" (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

The general form:

> "Do not act yet. Revise the plan so that [specific change]. Before proceeding to [step], show me [specific output] and wait for my approval."

---

## Worked Walkthrough — Report Plan Review

Priya submits her task brief from Chapter 4 for the board summary. Cowork responds with this plan:

> 1. Read and inventory files in /tasks/board-summary-june.
> 2. Identify Q3 and Q4 2024 program result sections.
> 3. Draft the executive summary.
> 4. Draft section-by-section results by program area.
> 5. Produce board-summary-june-2024.docx.

Priya works through the checklist:

- **Goal fit:** yes — all steps connect to the board summary objective.
- **Source coverage:** step 1 mentions the correct folder; the exclusions (draft files, template files) are not mentioned. Problem.
- **Risky steps:** no file moves, no deletions, no external submission. Writing a new file is low-risk. Acceptable.
- **Missing verification:** there is no step where Priya sees the source inventory and proposed structure before the draft is written. Problem.
- **Stop conditions:** no mention of what happens if financial figures are absent. Problem.

Priya's redirection:

> "Do not draft yet. Before step 3, add: (a) show me the full source inventory — which files are being used and which are excluded; (b) confirm that no files named 'draft' or 'template' are in the source set; (c) show me the proposed outline. I will approve before drafting begins. Also add: if financial figures are not present in the source folder, stop and notify me before proceeding."

Cowork revises the plan. Now steps 2a, 2b, and 2c are explicit. The financial-gap stop condition is included. Priya approves the revised plan.

The draft Cowork produces after that approval is a draft that matches the brief — because the plan did, and the draft followed the plan.

---

## When the Plan Reveals a Problem with the Brief

Sometimes plan review surfaces a problem not with the plan, but with the brief. Cowork proposes a step you had not considered, or asks a clarifying question that reveals your objective was underdefined.

This is the right outcome. The goal of plan review is not to approve plans; it is to ensure the work is well-defined before execution. If the brief needs revision, revise it. Pause the current plan, update the brief, and ask Cowork to replan.

Do not try to fix an underdefined brief through redirection alone. Redirection adjusts the plan. It does not substitute for a clear objective or missing decision rules. If the brief is the problem, fix the brief first.

---

## Computer Use and High-Supervision Plans

When a plan includes browser access or computer use, plan review requires more attention, not less. Cowork interacting with a browser or desktop application can move through multiple steps quickly, and each step may have consequences — page navigation, form input, account interaction — that are not easily reversible.

For these plans:

- Require each step to be shown before it is executed (Anthropic, "Let Claude use your computer in Cowork" [verify — current as of writing]).
- Explicitly prohibit form submissions, purchases, and account actions unless each is pre-approved.
- Name the specific sites or applications Cowork is permitted to interact with.
- Keep sensitive windows closed and unrelated applications hidden during the session.

A browser plan that includes "collect publicly available data from these three URLs" is reviewable. A browser plan that says "search for the information and collect what seems relevant" is not. The latter has no bounded source set and no visible action list. Redirect to specifics before approving.

---

## The Human Gate for This Chapter

Plan review is where your knowledge of the work — knowledge Cowork cannot have — enters the process before any action is taken. You know which files are sensitive. You know which program results are contested. You know that the "duplicates" folder actually contains earlier application versions that might matter. Cowork does not.

The plan review checklist gives your knowledge a place to land. Working through the checklist is not bureaucratic caution. It is the act of supervision that makes delegation responsible.

There is a known risk in automation: humans who approve steps they do not understand are not supervising; they are creating the appearance of supervision (Bainbridge 1983; Microsoft Research 2019). Clicking approve because the plan looks reasonable is not the same as approving because you have checked that the plan is safe. The checklist is the difference.

---

## Common Mistakes

**Approving because the plan sounds reasonable.** Reasonable prose is not the same as a safe plan. Check the specific steps, not the overall impression.

**Treating redirection as evidence of failure.** Redirection is the review process working. A plan that needed no revision was either trivial or was not checked carefully.

**Approving once and not monitoring.** In multi-step tasks, approval at the beginning does not cover what happens at step 4. Build in checkpoints, not just an initial approval.

**Skipping the risky-step check for "simple" tasks.** File operations, name changes, and deletions are irreversible regardless of how simple the surrounding task seems.

**Not reading the stop conditions.** A plan without stop conditions will proceed through gaps, errors, and missing sources by making its best guess. A plan that says "stop and tell me" catches those problems before they compound.

**Letting Cowork interpret approval as permission to expand scope.** Approving a plan approves that specific plan. If Cowork encounters something unexpected and adjusts scope, it should pause and ask, not proceed on the assumption that original approval covers the new situation.

---

## Try This

**Exercise 1 — Plan review on a provided example.** Here is a Cowork plan for extracting data from a folder of invoices:

> 1. Read all files in the invoices folder.
> 2. Extract vendor name, date, amount, and invoice number.
> 3. Compile into a spreadsheet named invoice-extract.xlsx.
> 4. Remove any duplicate entries.

Work through the plan-review checklist. What is missing? Write a redirection instruction that fixes the plan without starting over.

**Exercise 2 — Write a plan you would approve.** Using the brief template from Chapter 4, write a brief for a task you might actually run. Then write out the plan you would want Cowork to propose — step by step, including the verification checkpoint and stop conditions. Then submit your brief to Cowork and compare its plan to yours. What did it include that you missed? What did you specify that it skipped?

**Exercise 3 — Practice redirecting.** In a Cowork session, submit a task brief for a low-stakes task (a summary, a list, a draft outline). When Cowork proposes a plan, use the plan-review checklist and identify at least one thing to redirect — even if the plan is mostly fine. Write the redirection instruction using the three-part form: what to keep, what to change, the new constraint. Observe whether the revised plan addresses the redirection.

---

## What Would Change My Mind

This chapter recommends checking every plan against a checklist before approving. That recommendation would scale down if:

- Cowork developed explicit risk-flagging that reliably surfaces irreversible steps, missing verification, and out-of-scope actions before the user has to find them — reducing but not eliminating the need for manual review.
- Evidence showed that nontechnical users performing checklist-based plan review do not catch meaningfully more errors than those who use their judgment alone. The existing research on human-automation interaction suggests the opposite (Parasuraman, Sheridan, and Wickens 2000).
- Task risk profiles became granularly classifiable so that low-risk plans (read-only, no file writes, no external output) received a lighter review protocol than high-risk plans (file operations, external submission, computer use).

In the current product environment, the default is checklist review for all plans that involve file access, and especially for any plan that includes modification, deletion, submission, or scheduling.

---

## Still Puzzling

- Approval fatigue is a real risk for users who run many Cowork tasks. The right cadence of checkpoints — tight enough to catch errors, loose enough not to exhaust the reviewer — is an open empirical question. More research on nontechnical agent users would help.
- When a plan is revised after redirection, how much does the revised plan differ from what would have happened with a better brief? In practice, most redirections compensate for brief gaps, not plan defects.
- The boundary between "plan review" (checking the proposed steps) and "output verification" (checking what the steps produced) is not always clean in practice. Some errors are only visible in outputs. Chapter 6 addresses verification for specific task types.

---

## Bridge to Chapter 6

You have scoped access, written a brief, reviewed the plan, and approved or redirected. Cowork executes. Now you have an output.

For report and document tasks, verification is reading carefully against your brief. For data extraction tasks — receipts, invoices, spreadsheets, forms — verification requires something more systematic: row counts, spot checks, exception logs, and a willingness to find that the output contains errors the plan did not anticipate. Chapter 6 teaches that discipline for document data.

---

## Sources Used

- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic, "Let Claude use your computer in Cowork," Claude Help Center, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork [verify — current as of writing]
- Liang, W., et al. "Understanding the Planning of LLM Agents: A Survey," arXiv, 2024. https://arxiv.org/abs/2402.02716
- "Ask-before-Plan: Proactive Language Agents for Real-World Planning," Findings of EMNLP, 2024. https://arxiv.org/abs/2406.12639
- "Revealing the Barriers of Language Agents in Planning," arXiv, 2024. https://arxiv.org/abs/2410.12409
- Parasuraman, R., Sheridan, T. B., and Wickens, C. D. "A Model for Types and Levels of Human Interaction with Automation," 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
- Microsoft Research, "Guidelines for Human-AI Interaction," CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
- Bainbridge, L. "Ironies of Automation," 1983. https://doi.org/10.1016/0005-1098(83)90046-8
- OWASP, "Top 10 for LLM Applications 2025." https://owasp.org/www-project-top-10-for-large-language-model-applications/

---

## AI Wayback Machine

**Lucy Suchman** wrote *Plans and Situated Actions* in 1987, arguing that plans are not scripts that people follow step by step. They are resources people draw on while doing something that is actually more improvised, contextual, and responsive to what actually happens. A plan and the situated reality rarely match exactly — and the gap between them is where the real work of adjustment and judgment takes place.

Cowork's proposed plan is a resource, not a script. Your plan review is the situated judgment.

**Run this:**

```
Who was Lucy Suchman, and how does her idea of "plans and situated action" 
apply to reviewing an AI agent's proposed task plan? Keep it to three 
paragraphs. End with one question her work raises about what humans should 
do when a Cowork plan meets an unexpected situation.
```

→ Search **"Lucy Suchman plans and situated action"** for background.

**Now make the prompt better.** Try one of these:

- Ask how Suchman's critique of formal planning applies to AI systems that generate task plans from natural language briefs.
- Ask what the gap between a Cowork plan and the actual state of a user's files might look like in practice, drawing on her framework.

What becomes clearer? What new redirection strategy does this suggest?
