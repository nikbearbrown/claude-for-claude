# Chapter 7 — Planning Before Acting
*The first moment the agent's interpretation of your task becomes visible is the moment to look.*

A manager asks an agent to clean up the project folder and make sure everything is organized. The agent begins. Twenty minutes later, she discovers that files have been moved into new subdirectories, several items she considers active are in an archive folder, and one document she was about to edit has been renamed. Nothing is gone. But nothing is where she expected it, and she now has a secondary task: figure out what happened, and undo what she did not want.

The instruction was clear enough to start. It was not clear enough to supervise. And no one asked whether those two things were the same.

A plan would have changed this — not because a plan guarantees good behavior, but because a plan is the first moment at which the agent's interpretation of the task becomes visible to the human before anything changes. Before any file moves. Before any rename. The folder cleanup that took twenty minutes to undo could have been caught in two minutes of plan review, if the plan had been requested.

---

## The Planning Step That Already Happens

Here is something worth understanding about modern agentic systems: they plan whether you ask them to or not.

The ReAct architecture that underlies most current agentic systems interleaves reasoning and action — each tool call generates observations that feed the next round of reasoning (Yao et al., 2023). There is always a sequence the agent is working from, always an internal ordering of what to do before what. The question is not whether planning happens. The question is whether it happens in language the human can read before execution begins.

An agent working from an invisible plan and an agent working from a visible plan produce identical output in the best case. In the worst case, they produce very different outcomes — and only one of them gave the human an opportunity to catch the problem before it materialized.

Requiring an explicit plan before execution does not add a step. It surfaces a step that was always there. That surfacing is the point.

---

## What a Plan Is Actually For

A plan is a proposal, not a contract. It says: here is what I understand the task to be, what I will need to complete it, what I will do in what order, and what I will not touch. Every element is something the human can inspect and correct before action begins.

Research on LLM agent planning identifies what planning accomplishes at each layer (Liang et al., 2024). Task decomposition breaks a complex goal into steps that can be reviewed individually — "organize the project folder" becomes "inventory files, propose categories, create a backup, move items in groups, ask before archiving anything from the active list." Dependency order exposes which steps must happen before others, so the backup happens before the move, not after. Missing information identification surfaces what the agent does not yet know, so a decision criterion that was never stated appears as a gap in the plan rather than a silent assumption baked into an action. Stop conditions define when the agent should pause and ask rather than proceed. And verification evidence names what will show the task is complete.

That last one is the element most often omitted and most often consequential. A plan without verification evidence is a plan that ends when the agent says it does. There is no external criterion for done, only the agent's report. Chapter 8 builds on exactly this: verification is not a final check but the mechanism that makes delegation safe to close.

<!-- → [TABLE: Plan element checklist — columns: Element, What it names, Review question; rows: Goal, Inputs, Tools, Sequence, Permissions, Reversibility, Stop condition, Evidence; caption: use this table to evaluate any plan before authorizing execution — a plan thin on any row should be sent back for revision] -->

---

## The Clarifying Question Is Not Friction

Good plans often start with a question, and the question is not inefficiency — it is the work.

Research on proactive language agents shows that when task information is incomplete, asking before forming an execution plan produces better outcomes than filling gaps silently (Ask-before-Plan, 2024). An agent that asks "Do you want me to include archived folders in the cleanup, or only active project folders?" before proceeding has surfaced a scope boundary that was implicit in the instruction. The human now makes a decision that was previously invisible. That is supervision functioning correctly.

An agent that fills in missing information silently — deciding on its own what "organized" means, which files count as active, what the folder taxonomy should look like — is not being efficient. It is making scope decisions that belong to the human. The agent's choice may be reasonable. It may also be wrong in a way that takes longer to undo than the clarification would have taken to answer.

The rule is simple: underspecified task, clarifying question before plan.

The two failure modes to avoid are equally real. Too little planning for high-risk tasks produces the folder-cleanup scenario. So much required structure for low-risk tasks that practitioners stop asking for plans at all is its own kind of failure — a bureaucratic weight that trains people to skip the step. The right planning depth should match the consequence of error.

---

## What a Good Plan Looks Like

Here is a plan produced in response to "prepare a summary report on our Q2 customer feedback files":

> I understand the goal as: a written summary of patterns across Q2 customer feedback, organized by theme.
>
> I need these inputs: the files in /projects/customer-feedback/Q2/ — I count 14 documents. I will not open files outside that folder or access the CRM system.
>
> I propose these steps: read each document and note the main themes and any specific complaints or compliments; group themes across documents; draft a summary organized by theme with representative quotes; create a source map showing which quotes come from which files.
>
> I need approval before: creating any new files, sharing the summary outside this session, or accessing any systems beyond the files listed.
>
> I will verify by: confirming I read all 14 files, providing a word count for the summary, and listing the source map at the end.
>
> I will stop if: I encounter a document I cannot open, if the themes are contradictory in ways that require a business judgment I cannot make, or if I find documents outside the Q2 scope that seem relevant.

This plan is reviewable. A human can read it and check: the right folder is named, the scope boundary is explicit, the steps are in a sensible order, there is a stop condition, and the verification method is concrete. The human can also correct it before anything is read — "include Q1 as well" or "skip the quotes, just aggregate themes" — and those corrections cost nothing because no action has happened yet.

Now compare that to a different response to the same instruction:

> I'll read through the Q2 customer feedback files and prepare a comprehensive summary with actionable insights.

This is a caption for a plan, not a plan. It names the output but shows none of the inputs, steps, boundaries, stop conditions, or verification method. A human who approves this has not approved anything concrete — they have approved the agent's judgment about all of the questions the first version made explicit. Research on LLM planning limitations confirms that agents produce confident-sounding plans that omit critical constraints, especially when those constraints are not salient in the original instruction (Barriers in Planning, 2024). The agent is not being deceptive. It is providing what it expects the user wants: an efficient, reassuring description of work about to happen. The human must learn to ask for more.

<!-- → [DIAGRAM: Side-by-side comparison of good plan vs. caption-plan — left side shows the Q2 feedback plan with all eight elements labeled; right side shows the one-line version with arrows marking the eight missing elements; caption: both responses sound competent — the difference is what you can review before authorizing action] -->

---

## Risk-Tiered Planning Depth

Parasuraman, Sheridan, and Wickens (2000) characterized this problem in the context of human-automation interaction: the appropriate level of human involvement depends on the consequence of error. The same principle applies to plan depth.

A task with no write access to external systems, no communication tools, and no changes to shared resources needs a light plan: the goal, the specific inputs, and what the output will be. A task with write access limited to a designated folder, moderate persistence, and one tool class requiring approval needs the full plan template — goal, inputs, exclusions, steps, permissions, stop conditions, evidence. A task touching production systems, external communications, or financial records needs a strict plan: full template, plus explicit approval before each action class, plus documentation of evidence requirements before execution begins.

The tiers are not rigid categories. They are a calibration tool. The question is always: given the consequence of an error in this task, what is the minimum plan depth that makes supervision meaningful? A plan that is thinner than the risk requires is a plan that provides the appearance of oversight without the substance.

---

## The Material-Plan-Change Rule

Agents do not always follow the plan they stated. A tool fails. A file is missing. A new piece of context changes the apparent path. The agent adapts.

Adapting within the scope of the approved plan is appropriate. An agent that encounters a file formatted differently than expected and adjusts its parsing method is doing its job. But when an agent changes the plan materially — uses a different tool, accesses a source not in the approved list, changes the destination of an output, alters the risk level of an action — it must stop and ask. Not report after. Ask before.

The approved plan was specific. A materially different plan requires a new approval. Reporting a scope change after the fact is an audit log, not supervision. The practical instruction to give any agent before a consequential task: "If at any point you need to deviate from this plan in a way that changes the tools you use, the data you touch, or the risk level of an action, stop and tell me before proceeding."

This rule is especially important because the most common mid-task plan changes feel small from the inside. The agent needs to read one additional file to clarify an ambiguity. The agent decides to send a draft to a shared folder for convenience. The agent installs a library it needs for a parsing step. Each of these is a scope expansion. Each of them bypasses the approval the original plan was designed to capture.

---

## The Folder Cleanup, Done Correctly

Return to the opening scene. Same manager, same instruction, different workflow.

The agent's first move is not to act. It is to ask: "What counts as organized — do you have a folder structure in mind, or should I propose one? Are there files you'd like me not to move? What should I do with items that look inactive?"

After the manager answers, the agent produces a plan: the target folder path, the proposed taxonomy, which items it will not touch without explicit approval, and a stop condition — "I will pause before moving any file modified in the last 30 days or any file I cannot confidently classify."

The manager reviews and adjusts: "Use 60 days, not 30. And don't rename anything — only move."

The agent now works within a bounded scope. Anything it is uncertain about goes into a "review required" list rather than being moved. After execution, it produces a move log, a list of held items, and the before/after folder structure.

This took longer to begin. It cost nothing that could not be recovered. The original version — immediate action from a vague instruction — required recovery work harder than the task would have been if a plan had been requested. The slowdown at the start is not inefficiency. It is the price of not having to undo.

Roth et al. (2004), studying mixed-initiative planning for UAV teams, found that effective human oversight depends on having clear levers to modify or veto plans before execution. For agentic AI, those levers are the plan review, the clarifying question, the explicit approval, and the material-plan-change rule. Remove any one of them and you have reduced oversight to monitoring — watching what happens rather than shaping it before it does.

---

## What a Plan Is Not

A plan is not proof of comprehension. It proves the agent has a coherent description of a path. It does not prove that path is correct, that the constraints are right, or that the agent will not encounter a situation its plan did not anticipate. The plan is the starting hypothesis. The verification evidence is what closes it.

A plan is not a substitute for scope clarity in the original instruction. An agent handed a vague instruction will produce a plan that fills in the vague parts — and the fill-in will be invisible unless the human reads carefully enough to notice that assumptions have been made. The plan review is not a second chance to scope the task. It is a check that the scoping you did in the instruction was accurate. If the plan reveals gaps, those gaps were in the instruction, not introduced by the plan.

A plan is not transferable across task types. A plan appropriate for reading and summarizing a folder of documents is not appropriate for writing files, contacting external services, or running commands against a live system. The plan depth should match the action surface of the task, and the action surface of a task can be larger than it appears.

---

## LLM Exercises

**Exercise 1.** Give an agent an open-ended task instruction relevant to your work — "prepare a brief on the latest updates in our product category" or "organize the files in this folder." When the agent responds, do not let it proceed. Instead, rate the response against the plan checklist: does it name inputs, steps, permissions, stop conditions, and verification evidence? Ask the agent to revise the plan until all elements are present. Note which elements it omitted on the first try.

**Exercise 2.** Write a deliberately bad plan for a task you know well — one that sounds confident but omits scope boundaries, stop conditions, and evidence. Then rewrite it as a strict-tier plan. Compare them. What would have happened if the bad plan had run? What would have been the first sign that something had gone wrong?

**Exercise 3.** Give an agent a task with a deliberate ambiguity — a term like "recent," "relevant," or "important" that requires a scope decision. Watch whether it asks a clarifying question or fills in the gap silently. If it fills in the gap, ask it to name all the assumptions it made in forming the plan. Compare that list to what you would have said if asked directly.

---

## Graduated Exercises

**Warm-up**

1. *Identify the missing element.* An agent responds to "analyze our sales data from last quarter" with: "I'll review the sales data and identify trends, anomalies, and key performance indicators." Name every plan element from the checklist that this response omits. Which omission is most consequential and why?

2. *Apply the risk tier.* Classify each of the following tasks as light, standard, or strict plan depth, and state one reason for each classification: (a) summarize a set of uploaded PDFs into a bullet list, (b) draft and send a weekly status email to a distribution list, (c) reorganize a local folder with no external system access.

**Application**

3. *Write a complete plan.* A team member asks an agent to "find all duplicate entries in our customer database and flag them for review." Write the plan the agent should produce before beginning — including goal statement, inputs, exclusions, steps, permissions required, stop condition, and verification evidence. The database contains PII.

4. *Apply the material-plan-change rule.* An agent is midway through a document organization task when it discovers that three files are password-protected and cannot be opened. Its original plan did not account for this. Write the message the agent should send before proceeding, naming what it has found, what options it sees, and what it needs from the human before continuing.

5. *Evaluate a plan.* An agent presents this plan for a task involving pulling financial data into a report: "I'll access the finance folder, compile the relevant numbers, generate a summary table, and email the report to the finance team." Rate this plan against the checklist, identify the two most dangerous omissions for this specific task type, and write the revised plan.

**Synthesis**

6. *Design a planning protocol.* Your organization is deploying an agent to assist with weekly competitive intelligence reports. The task involves reading public web sources, summarizing findings, and distributing a report to fifteen stakeholders. Design a planning protocol: what tier applies, what the required plan elements are, what constitutes a material plan change requiring re-approval, and what the verification evidence should be before distribution.

7. *Trace a failure.* A manager delegated a task to an agent to "update the client contact records with the information from the meeting notes." The agent ran without requesting a plan review, updated 40 records, and overwrote several fields that had been manually corrected the previous week. Trace the failure: at which step did supervision break down, which plan elements were missing, and what single intervention would have prevented the outcome?

**Challenge**

8. *Design the threshold.* The chapter argues that planning depth should match the consequence of error. Propose a decision procedure for practitioners to use when determining plan tier for an unfamiliar task. Your procedure should handle at least four variables (e.g., write access, reversibility, external-facing actions, shared resources), produce a consistent tier recommendation, and include at least one worked example of a task where the tier is non-obvious.

---

## Sources Used

- Liang, Wenliang et al. "Understanding the Planning of LLM Agents: A Survey." arXiv, 2024. https://arxiv.org/abs/2402.02716
- "Ask-before-Plan: Proactive Language Agents for Real-World Planning." Findings of EMNLP, 2024. https://arxiv.org/abs/2406.12639
- "Revealing the Barriers of Language Agents in Planning." arXiv, 2024. https://arxiv.org/abs/2410.12409
- Yao, Shunyu et al. "ReAct: Synergizing Reasoning and Acting in Language Models." ICLR 2023. https://arxiv.org/abs/2210.03629
- Shinn, Noah et al. "Reflexion: Language Agents with Verbal Reinforcement Learning." NeurIPS 2023. https://papers.nips.cc/paper_files/paper/2023/hash/1b44b878bb782e6954cd888628510e90-Abstract-Conference.html
- Parasuraman, R., Sheridan, T. B., and Wickens, C. D. "A Model for Types and Levels of Human Interaction with Automation." 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
- Roth, E. M. et al. "Human-in-the-Loop Evaluation of a Mixed-Initiative System for Planning and Control of Multiple UAV Teams." 2004. https://journals.sagepub.com/doi/pdf/10.1177/154193120404800301
- Microsoft Research. "Guidelines for Human-AI Interaction." CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
- Anthropic. "Claude Code Overview." Claude Code Docs. https://code.claude.com/docs
- Anthropic. "Get Started with Claude Cowork." Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork
