# Chapter 10 — Designing Human Approval Gates
*A gate that is always clicked is not supervision. It is a ritual.*

A researcher glances at the corner of her screen. "Claude wants to run a command. Allow?" She clicks Allow. She has clicked it forty times in the last two days. Each time, the task completed fine. Each time, she does not know what command ran.

This is a gate. It is not functioning as one.

A gate that gets clicked without being read is not supervision — it is the performance of supervision. It reduces legal exposure ("I was asked") while providing none of the actual protection gates exist for: catching wrong actions, scope creep, and irreversible consequences before they happen. The dialog appeared. The human was technically present. Nothing was supervised.

This chapter is about gate design, not gate presence. The question is never whether the dialog appeared. The question is whether the human understood what they approved.

---

## What an Approval Gate Actually Is

An approval gate is a moment in the agentic workflow where execution pauses and the human decides whether to continue, revise, or stop. That definition is simple enough, but three things are often confused with it that need to be cleared away first.

A gate is not a plan review at the start that covers everything afterward. A plan approval authorizes a proposed sequence. It does not authorize scope changes, tool additions, or target shifts that happen during execution. A plan is a snapshot; the work is a film.

A gate is not a final check after consequential action has already occurred. If the email has sent, the file has been deleted, or the form has been submitted, the gate arrived too late. The gate belongs before the action takes effect — not as a record of what happened, but as the decision about whether it should happen.

A gate is not a trust signal. "The agent asked, so it must be okay" is a sentence that sounds reasonable and is dangerous. The agent asking is the precondition for supervision. The human evaluating what was asked is the supervision itself. These are not the same thing.

Parasuraman, Sheridan, and Wickens (2000) mapped where in a decision sequence a human can be involved: in gathering information, analyzing it, selecting an action, or executing it. A gate can sit at any of these points. For agentic AI, the highest-value placement is usually action selection — before the agent decides to use a specific tool — and action execution — before that tool call fires. Reviewing what information the agent gathered is less critical than reviewing what it plans to do with that information.

---

## What a Gate Needs to Contain

A gate that says only "Allow?" is a speed bump. The human's foot lifts slightly and comes back down. Nothing has been evaluated.

A gate that functions as supervision must give the human six things:

What action the agent wants to take — delete, send, move, run, submit, write, call. Not "the next step" but the specific verb applied to a specific thing.

What the target is — which files, which address, which command, which API, which record. Generic descriptions are not enough. "Delete some files" is not a target; "delete /projects/client-alpha/archive/2022/" is.

Why the agent thinks this action is needed — the connection between this specific action and the task the human assigned. This is where scope creep becomes visible. If the reason doesn't connect to the task, the action is outside the approved work.

What the risk is — what could go wrong if this action is incorrect. An agent that cannot state the risk of its own proposed action is an agent whose proposal should not be approved.

Whether it is reversible — can it be undone, how easily, and by what means. Reversibility is not binary. A moved file with a backup is different from a moved file without one. A sent email to one person is different from one sent to a distribution list.

What to check afterward — the evidence the human should inspect to confirm the action produced the right result. A gate without a verification criterion hands the human a completed action and no means to evaluate it.

The prompt that elicits all six elements: "Before you do this, state the exact action, the target, why it is needed, what could go wrong, whether it is reversible, and what evidence I should inspect afterward."

Microsoft Research's guidelines on human-AI interaction identify the same requirements from a different angle: users need to know what the system is doing, why, what went wrong if it fails, and how to take back control (CHI 2019). These are not interface preferences. They are what makes a gate function as supervision rather than performance.

---

## Classifying Actions Before Placing Gates

Not every action needs the same gate. Gate maximalism — a gate at every step — produces the forty-clicks problem. The researcher clicking Allow without reading is the predictable outcome of a workflow where every minor action pauses for approval. The goal is gate appropriateness: placing gates where the action's risk and irreversibility warrant a real decision.

Two axes determine placement.

The first is risk: what is the consequence if this action is wrong? Risk scales with blast radius — how many things are affected — and with the sensitivity of what is affected: financial, personal, regulatory, reputational, or relationship consequences.

The second is reversibility: if this action is wrong, how hard is it to undo? Some actions are fully reversible — a draft that has not been sent. Some are partially reversible — a file that was moved but has a backup. Some are practically irreversible — an email sent to a large list, a record submitted to a regulatory system, a database deletion without backup. Reversibility is a spectrum, not a switch.

<!-- → [DIAGRAM: 2x2 grid with risk (low/high) on one axis and reversibility (reversible/irreversible) on the other — four cells: light gate (low risk, reversible), approval gate (high risk, reversible), approval gate (low risk, irreversible), human-only (high risk, irreversible); caption: gate type follows from the quadrant, not from how the agent described the action] -->

The most important quadrant is irreversible and high-risk. Actions in that cell are not candidates for agent execution even with approval. They belong to a category called human-only: the agent can draft, prepare, or recommend, but the human is the one who presses send, submits the form, or executes the command. The approval is not "allow the agent to do this" — it is "I am doing this; the agent prepared it."

This distinction matters because approval fatigue and automation bias make rubber-stamping predictable in long workflows. If the human is likely to click Allow on high-risk irreversible actions after an hour of routine tasks, then designing those actions as agent-executed-on-approval has already failed. Designing them as human-initiated removes the rubber-stamping risk from the equation.

<!-- → [TABLE: Gate matrix — columns: Action type, Example, Gate type; rows covering: Drafting, Transforming a copy, Editing source files, Running a command, Moving or deleting, External action (send/submit), Sensitive data access; caption: use this table to classify actions in any planned agentic task before the agent starts] -->

---

## Five Points in the Loop Where Gates Belong

The agentic loop has five phases where gates apply. They are not all equal. Some matter more than others depending on the task. All five are worth designing before the agent starts.

**Tool and permission approval, before the task begins.** Which tools does the agent have access to for this specific task? Every enabled tool is a potential action channel. Claude Code's permissions system allows users to specify which commands and file operations run automatically versus which require explicit approval (Anthropic, Claude Code Docs). Cowork safety guidance distinguishes which connectors, apps, file scopes, and external actions require user confirmation (Anthropic, Claude Help Center, 2026). This gate is architectural: its question is whether the agent needs all of these tools for this task, or whether the tool surface can be reduced before starting. Removing a tool from scope is always simpler than catching its misuse mid-task.

**Plan approval, before execution begins.** The agent proposes a plan: steps, tools, order of operations, expected outputs. This is the moment to review the plan against the task's risk classification, catch scope problems, and verify that the tool set matches the task. Plan approval does not cover later changes. If scope, tool, target, or risk changes during execution, a new gate is required. Treating plan approval as a blanket session authorization is the single most common gate design error.

**Action approval, before high-risk or irreversible actions execute.** For low-risk reversible actions within an approved plan, this gate can be lightweight or waived — the agent proceeds within defined bounds. For anything irreversible or outside the original scope, the gate fires with all six required elements. Claude Code's permission tiers implement a version of this: some operations run automatically, others pause for confirmation, others are blocked entirely.

**Verification, before the output is used.** The agent has acted. Before the output is sent, published, merged, or submitted, the human verifies it against pre-defined evidence criteria. This gate catches plausible-sounding summaries, silent omissions, and confident completion claims before they leave the workflow. It is not optional on consequential work. An agent that completes a task correctly and an agent that reports completing a task correctly look identical until the verification gate.

**External-effect gate, before the output reaches outside the system.** Sending, publishing, submitting, merging to production, sharing with a third party. This gate is always human-initiated for anything irreversible or external-facing, regardless of how clean the prior verification looked. Cowork safety guidance specifically identifies browser form submission and external app actions as requiring this control level. The principle is simple: the agent does not send things. The human sends things the agent prepared.

---

## Four Responses, Not Two

Approval gates are decision points, and decisions have more than two options. The workflow that offers only "Allow" and the implicit option of closing the window without clicking is a workflow designed to produce approvals.

The four gate responses are approve, redirect, pause, and stop.

Approve means the action is consistent with the task, the target is correct, the risk is acceptable, and the reversibility is understood. The work continues as planned.

Redirect means the action is on the right track but something is wrong: wrong target, wrong scope, wrong tool, output that failed verification. Give the agent a corrected instruction and let it try again before the next gate. Redirect is the most underused response because it requires formulating a correction, which takes effort. Gates designed to make redirect as natural as approve — prompting the human to state what should change, not just whether to proceed — use it more.

Pause means there is not enough information to approve or redirect. The agent needs to provide more detail — a fuller log, a source list, a completed inventory — before the decision can be made. Pause is not a stop; it is a request for evidence. A gate that cannot produce a pause response has no way to ask for what it is missing.

Stop means the task is unsafe, unverifiable, outside sanctioned scope, or the risk has materially changed since the plan was approved. Execution ends. The agent does not proceed. The human determines whether and how to re-scope before starting again.

Research on overreliance and automation bias shows that approval gates in practice default toward approve, especially when the agent has been reliable and time pressure is present (Stanford SCALE Initiative, 2025; Springer AI & Society, 2025). Building redirect, pause, and stop into the gate design at the same level as approve — not as available options buried in a menu, but as equally visible choices with equal friction — reduces approval drift.

---

## A Worked Walkthrough

A communications director wants to reorganize three years of project folders. She asks her agent to propose a new folder structure, identify files to archive, and flag duplicates.

Risk-reversibility classification: moving and potentially deleting files is irreversible without a backup. Some files may be shared with external collaborators. Risk is moderate; reversibility is low. By the grid, this task is in the approval-gate quadrant, trending toward human-only for any deletion.

She designs five gates before the task starts.

The tool gate restricts the agent to read access on the folders and write access to a staging folder only. No delete access. No access to shared drives.

The plan gate requires the agent to produce a proposed folder map and a list of files flagged for archiving with reasons before any files move. She reviews this list — not to understand the agent's competence but to catch any file she knows the classification of that the agent does not.

The action gate requires her approval per batch of moves, not per file. She reviews each batch's destination, confirms it against her own knowledge, and approves explicitly per batch.

Deletion is human-only. The agent never deletes. It moves candidates to a staging folder labeled PENDING-DELETE. She makes the deletion decision herself after reviewing the staged items.

The verification gate confirms file counts match expected counts after each batch and spot-checks three files per batch to confirm they are accessible and intact.

During the task, the agent flags a client folder for archiving because the client name matches an older completed project. She knows the client is still active. She redirects — keep the folder in active, adjust the matching criterion. The task completes correctly.

This did not catch a dramatic failure. It caught an ordinary classification error that would have been annoying and time-consuming to reverse. The gates did not prevent the agent from working. They gave the director the decision points where her knowledge was necessary.

---

## The Skill That Gates Require

Approval gates require human expertise, but the expertise is not technical. It is consequential: knowing what the action would mean if it went wrong.

The agent may know how to click, delete, or submit. The human decides whether that action should happen, to that target, at that moment, given what is known about what the output will be used for. That judgment requires knowing the context the agent does not have: which files are actually active, which clients are sensitive, which outputs will be shared with whom.

Bainbridge (1983) identified the core problem of automation: routine automation atrophies human expertise, so the moment when a human is most needed — when the system reaches the edge of its scope — is exactly when the human is least practiced at intervening. Approval gates are a partial design response to this problem. By placing real decisions at real points in the workflow, they keep the human practiced at evaluating the agent's actions, even when most of those decisions are straightforward approvals. The habit of genuine evaluation does not survive workflows where every gate is clicked without reading. Build the habit at the low-stakes gates and it is available at the high-stakes ones.

NIST's AI Risk Management Framework codifies the underlying principle: high-stakes, irreversible, or regulated actions require qualitatively different oversight than low-stakes reversible work (NIST, 2023). Gate design is the mechanism through which that principle becomes practice in an actual task. The framework names the tiers; the gate is the enforcement point.

---

## LLM Exercises

**Exercise 1.** Choose one agentic task you have run or plan to run. Classify each significant action using the risk-reversibility grid. For each action that falls in the approval gate or human-only category, write out the gate prompt you would use — all six elements: action, target, reason, risk, reversibility, verification evidence. Then run the task using the gate design you specified. Did any gate change your decision? Did any reveal something unexpected?

**Exercise 2.** Find a workflow — yours or a described one — where the only gate is "Allow?" Redesign it. What action, what target, what risk, what reversibility, what post-action evidence should this gate display? Write out what you would need to see to redirect or stop rather than approve.

**Exercise 3.** Run a short agentic task with the four-response gate structure explicitly in mind. Before each gate, decide which response you would give if the action were slightly wrong — what would redirect look like, what would pause look like, what would stop look like? Compare how this preparation affects what you notice when the gate fires.

---

## Graduated Exercises

**Warm-up**

1. *Classify an action.* A developer asks an agent to run a database query that reads all records from a customer table to generate a usage report. No records are modified. Using the risk-reversibility grid, classify this action and name the appropriate gate type. What single additional fact about the deployment environment would change your classification?

2. *Name the missing elements.* An agent presents this gate prompt before sending a report: "I'm about to email the Q3 summary to the finance team. Allow?" List every element from the six-element gate requirement that this prompt omits. Which omission is most consequential for this specific action?

**Application**

3. *Design a gate sequence.* A researcher wants an agent to compile a literature review from PDFs in a project folder, draft a summary document, and save it to a shared team drive. Design the gate sequence: identify the five phases that apply, state what each gate should contain for this specific task, and classify the final save action using the risk-reversibility grid.

4. *Write a redirect response.* An agent is midway through organizing a project folder and presents a gate: "I'm about to move 23 files from /projects/active/client-beta/ to /archive/2023/. The files have not been modified in 14 months. Allow?" You know that one active deliverable for this client is stored in that folder. Write the redirect response — what you tell the agent, what specific correction you require, and what the next gate should show before any files move.

5. *Apply the human-only rule.* A marketing team uses an agent to draft and schedule social media posts. The current workflow has the agent draft posts, ask for approval, and then publish directly to company accounts. Identify which action should be redesigned as human-only, explain why using the irreversibility and risk criteria, and describe what the redesigned workflow looks like.

**Synthesis**

6. *Design gates for a sensitive task.* An HR coordinator wants an agent to help process job applications: read resumes, score them against a rubric, and draft interview invitations for candidates above a threshold score. Design the complete gate structure for this workflow, classifying each action and specifying what each gate must contain. Identify which actions, if any, should be human-only, and explain the regulatory and sensitivity rationale.

7. *Diagnose approval fatigue.* A team has been running a daily agentic report workflow for three weeks. The workflow fires 15 approval gates per session. A team member reports that she has not rejected or redirected a gate in ten days. Using the automation bias research and the gate design principles from this chapter, diagnose what has likely happened and propose the minimum redesign that would restore genuine evaluation without eliminating the gates entirely.

**Challenge**

8. *Build a gate policy.* You are designing the gate policy for a small team that uses agentic AI across three task types: internal research summarization, customer-facing document preparation, and CRM data entry. For each task type, specify: the risk-reversibility classification of the primary actions, the gate type at each of the five loop phases, which actions are human-only and why, and what training the team needs to evaluate gates genuinely rather than reflexively. Defend one decision in your policy that a colleague might argue is overly conservative.

---

## AI Wayback Machine

**Run this:**

```
Who was James Reason, and how does his Swiss cheese model of accident causation connect to the approval gate design we covered in this chapter? Keep it to three paragraphs. End with the single most surprising thing about his career or ideas.
```

→ Search **"James Reason Swiss cheese model"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask it to map each layer of the Swiss cheese model onto one of the five gate phases in this chapter.
- Ask it whether the Swiss cheese model suggests gates should be redundant or sequential, and why that distinction matters for agentic workflows.

What changes? What becomes more precise? What becomes harder to argue?

---

## Sources Used

1. Parasuraman, Sheridan, and Wickens, "A Model for Types and Levels of Human Interaction with Automation," 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
2. Bainbridge, "Ironies of Automation," Automatica, 1983. https://doi.org/10.1016/0005-1098(83)90046-8
3. Microsoft Research, "Guidelines for Human-AI Interaction," CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
4. NIST, "Artificial Intelligence Risk Management Framework (AI RMF 1.0)," 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
5. Anthropic, "Configure permissions," Claude Code Docs. https://code.claude.com/docs/en/permissions
6. Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely
7. Anthropic, "Let Claude use your computer in Cowork," Claude Help Center, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork
8. OWASP, "Top 10 for LLM Applications 2025." https://owasp.org/www-project-top-10-for-large-language-model-applications/
9. OWASP, "Top 10 for Model Context Protocol." https://owasp.org/www-project-mcp-top-10/
10. Stanford SCALE Initiative, "Overreliance on AI: Literature Review." https://scale.stanford.edu/ai/repository/overreliance-ai-literature-review
11. Springer AI & Society, "Exploring automation bias in human-AI collaboration: a review and implications for explainable AI," 2025. https://link.springer.com/article/10.1007/s00146-025-02422-7
