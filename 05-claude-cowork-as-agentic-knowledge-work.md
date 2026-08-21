# Chapter 5 — Claude Cowork as Agentic Knowledge Work
*Polished and correct are different properties, and conflating them is how the damage gets done.*

A program manager asks an AI agent to prepare a briefing from the past month's project documents — meeting notes, status updates, email summaries, a few draft memos. The output arrives in twenty minutes: cleanly formatted, reasonable headings, confident bullet points. The manager shares it with leadership before reading it carefully.

Three things went wrong. The report attributed a decision to a person who had only proposed it — the final decision was never made. It dropped the one dissenting memo entirely. And it drew on a document clearly labeled DRAFT — NOT FOR DISTRIBUTION, which happened to be sitting in the same folder as everything else.

The output was fluent. The workflow failed. Not because the agent fabricated anything or acted in bad faith. Because "polished" and "correct" are different properties, and the manager treated the first as evidence of the second.

That gap — between what the output looks like and what it actually is — is what this chapter is about. Cowork is genuinely capable at the kind of multi-document, multi-step knowledge work that most professional life is made of. Using it well means understanding where the loop can slip before you build the workflow, not after you've already sent the briefing to your director.

---

## What Cowork Actually Is

Claude Cowork is an agentic AI environment for desktop knowledge work (Anthropic, "Get started with Claude Cowork") [verify — current as of writing]. That phrasing matters. It is not a document editor with AI suggestions wired in. It is an agent — with a tool set and an action surface — that operates in the domain where most professional work happens: files, folders, documents, spreadsheets, browser sources, connected services, and the applications running on your computer.

Concretely, Cowork can read files in a folder you designate, create or edit documents and spreadsheets, use connected services through plugins and MCP connectors, browse web sources, interact directly with desktop applications through computer use, and execute scheduled tasks without your presence.

The agentic loop from Chapter 2 operates here exactly as it did in the Claude Code context: Cowork observes the files and instructions you provide, forms a plan, acts through tool calls, checks its progress, and reports. What changes between the engineering case and the knowledge-work case is the medium — documents instead of code — and the verification method. There is no test suite. There are no diffs that tell you unambiguously what changed. Verification means a human reading the output against the sources, and that is not optional.

---

## The Access Ladder

Cowork reaches information and services through four increasingly powerful mechanisms, and the Anthropic documentation is explicit about preferring the lower rungs before climbing to the higher ones (Anthropic, "Let Claude use your computer in Cowork," 2026) [verify — current as of writing].

**Connectors and plugins** are the lowest-risk external access. They provide defined interfaces to specific services — a calendar, a project management tool, cloud storage — with bounded scope. Before enabling a connector, the question to ask is not just what it can read but what actions it can take. A calendar connector that reads events is different from one that creates, modifies, or deletes them. The permission is the capability.

**Designated folder access** means the agent can read every file in a folder you specify. This is where the opening scene failed. The agent read everything it was given access to, including the draft memo that should never have been in scope. The folder is not a neutral container. It is a permission grant. What goes in the folder is a decision the human makes, not the agent.

**Browser access** allows the agent to load and interact with web pages. For knowledge work this usually means research from public sources. The risks are specific: the agent may reach sites you did not intend; web content can contain adversarial instructions that manipulate agent behavior (VPI-Bench, 2026); login portals and form submissions are not appropriate targets for unattended browsing. Browser tasks should specify named trusted sources and explicitly prohibit form submission, login, and purchase.

**Computer use** allows the agent to observe and interact with anything visible on the desktop — any application, any window, any content. This is the broadest access level in the hierarchy and the hardest to scope tightly. The AgentDojo research documents that agents operating across files, browsers, and services face real adversarial risk from instructions injected into documents and web content (AgentDojo). That risk scales with breadth. Computer use warrants the most conservative permission design and the closest output review of any Cowork access type.

<!-- → [TABLE: four rows, one per access type — connector/plugin, folder, browser, computer use — columns: what it exposes or can change, risk level, default rule the human should apply] -->

---

## The Task Packet

Effective Cowork supervision starts before the agent acts. The mechanism is a task packet — the set of decisions the human makes in advance that determines what the agent can see, what it must produce, and where the human will check before the work proceeds.

**Working folder.** Which specific folder, and what is actually in it? The folder should contain copies of approved files, not originals. Files outside the task scope — drafts, confidential data, personal files, anything marked not for distribution — should be removed or kept in a separate location before the task starts.

**Allowed and forbidden files.** If the folder contains anything that should not be read, it should not be in the folder. Relying on the agent to infer that a file is off-limits from a label it might not read consistently is not a supervision strategy.

**Output specification.** What specific file or files should the task produce? A clear specification prevents the agent from generating extra artifacts or overwriting existing ones.

**Source-log requirement.** For any report or summary that will be shared, require the agent to list which files it drew on for each section. Without a source map, checking claims requires re-reading everything. With one, verification is targeted.

**Approval points.** For multi-step tasks — extract, then draft, then format — define where the human reviews before the next step runs. Plan approval is cheaper than output revision.

**Verification checklist.** Before the task begins, decide what specific checks you will perform after it ends. A checklist made in advance is more reliable than a spot-check made under time pressure.

<!-- → [INFOGRAPHIC: task packet as a pre-flight checklist — six items labeled, each with a one-line description of what it prevents] -->

---

## A Briefing Task, Done Right

Here is the opening scene rebuilt with the task packet in place.

The program manager creates a folder called `/project-briefing-working/` and copies only finalized, distributable documents into it: meeting notes from three sessions, two official status updates, and one approved summary memo. The draft memo and anything marked confidential remain in the original location.

Task packet: allowed — all files in `/project-briefing-working/`. Output: `briefing-draft.docx`. Source log required — each section must list its source files. Forbidden: no browser, no external messages, no file deletion. Approval point: review the outline before drafting begins.

Cowork produces a proposed outline. The manager reads it. The outline includes a "pending decisions" section — correct. It does not include a section on the one dissenting view from the project. The manager adds an instruction: include a section on the dissenting position from the March 14 notes. The outline is approved.

The briefing draft arrives. The manager runs the verification checklist.

*Source map.* Each section lists the documents it drew from. The "pending decisions" section cites two meeting notes. The manager checks both and confirms the agent correctly identified one decision as pending rather than made.

*Claims check.* The manager reads the attributed-decision claim. The source note points to a specific meeting note. The manager reads that passage: it was a proposal, not a decision. The claim is wrong. The manager corrects it before sharing.

*Omissions.* The dissenting view is present because the manager added it to the instructions. The manager checks that the draft represents it fairly rather than dismissing it.

*Privacy check.* The source log lists the three meeting notes, two status updates, and the approved memo — all files that were cleared for the folder. No unlisted files appear.

The manager revises the attributed-decision claim, reviews the revision, and shares the corrected briefing.

This is more work than accepting the first output. It is less work than recovering from a wrong claim that has already reached leadership.

<!-- → [DIAGRAM: side-by-side comparison of the unsupervised workflow (folder → agent → share) and the supervised workflow (folder prep → task packet → plan approval → draft → verification checklist → share), with the failure points marked on the unsupervised path] -->

---

## The Research Context: RPA and What Changed

Office automation is not new. Robotic Process Automation systems have handled repetitive document and data tasks since the early 2010s, changing knowledge-worker roles in measurable ways (Lacity, Willcocks, and Craig, 2020). The shift with language-driven agentic AI is in how tasks are specified: instead of programming every step of a workflow, a user describes a goal and lets the agent plan the steps and select the tools.

That flexibility is the capability. It is also the new risk.

A scripted RPA workflow does exactly what it was programmed to do. An agentic workflow improvises when it encounters something unexpected. Improvisation is not always wrong — it is sometimes the whole point of using a flexible agent rather than a rigid script. But improvisation within a broad action surface is where overreach happens, where a draft memo gets read because it was in the folder, where a claim gets attributed to the wrong person because the agent interpolated from context rather than from evidence. The task packet is the mechanism for converting flexible capability into bounded action, and the Cowork research literature frames it in exactly those terms (Kedziora, Siemon, and Kedziora, 2026).

---

## What Cowork Does Well, and What It Doesn't

Knowledge-work tasks that are well-suited to agentic automation share a few properties: they are structurally consistent enough that the output form can be defined in advance, they involve multiple documents or data sources that a human would find tedious to synthesize manually, and the human can verify the result without re-doing the whole task from scratch.

Tasks that are poorly suited share different properties: they require tacit judgment the agent cannot make, involve confidential or regulated data without governance structures, or produce external-facing actions the human has not reviewed.

<!-- → [TABLE: eight task types in rows — report from approved files, spreadsheet extraction from PDFs, cross-document summary, meeting-note synthesis, folder organization on copies, slide deck from source packet, external message sending, sensitive data processing — columns: fit (strong/moderate/poor), main risk, verification method] -->

The reversibility test from Chapter 3 is the reliable discriminator. Reports, summaries, and extractions can be revised before they leave your control. External messages cannot be unsent. Regulated-data actions carry compliance consequences that do not reverse when you correct the document.

---

## Scheduled Tasks and What They Require

Cowork supports scheduled tasks that run without your presence. The agentic properties are unchanged; what compresses is the supervision window. When the task runs unattended, the setup is the only moment available for supervision design.

Before scheduling a recurring task: confirm the task is low-risk and reversible; confirm the output is something you will review before acting on; make the folder and access scope as narrow as possible; build in a regular review cycle where you examine what the scheduled task has been producing, not just the most recent output; and prohibit unattended tasks from sending external messages, modifying records, deleting files, or touching regulated data.

The Anthropic documentation on assigning tasks remotely supports the practical point: task delegation from a distance increases the supervision responsibility at setup time (Anthropic, "Assign tasks from anywhere in Claude Cowork") [verify — current as of writing]. What you cannot supervise in real time, you must constrain in advance.

---

## The Human Gate in Knowledge Work

The Microsoft Research guidelines for human-AI interaction identify transparency, recoverability, and appropriate calibration of trust as principles for AI-assisted work (Microsoft Research, 2019). In the Cowork context, those principles reduce to four concrete moments.

*Before the task:* Define the task packet. Bound the folder. State the forbidden actions. Require the source log.

*Before each major step:* If the task is multi-phase, review the plan and the phase output before the next step begins. Do not authorize drafting before you have reviewed the outline. The plan gate is the cheapest intervention point; it costs nothing to redirect before execution starts.

*After the task:* Run the verification checklist. Check sources, claims, omissions, and privacy before the output is used.

*Before sharing or acting:* The final gate is the moment before the output leaves your control. If you are not certain it is correct, it is not ready to share.

The polished artifact is not the finish line. The finish line is a reviewed, verified output you are willing to put your name on.

---

## The Omission Problem

There is an asymmetry in how errors surface. Incorrect information is something you can point at — a wrong name, a wrong figure, a misattributed claim. Missing information is harder to catch because there is nothing to point at. The dissenting memo that was dropped from the opening scene's briefing was invisible in the output. You would have to know it existed, and know to look for it, to find the gap.

This is a real open problem in knowledge-work supervision. The omission may be the most consequential failure mode and the hardest to see. Source logs help — if the agent lists every file it drew on, you can check whether any expected source is absent. But source logs are only as complete as what the agent chose to note, and the agent that silently skipped a file may not note it.

The partial remedy is to include the expected sources explicitly in the task packet, not just the folder. If the task is to synthesize three specific meeting notes, name them. Then confirm in the source log that all three appear. It is not a complete solution. It is the best available one.

---

## What Would Change My Mind

The verification-first stance here would soften for outputs where the stakes are low and the verification cost is high relative to the harm of a possible error. For internal working documents — draft outlines, exploratory first-pass summaries used as rough inputs to human judgment — exhaustive review before every use is probably not warranted. The chapter's caution is strongest for outputs that will be shared, acted on, or used as the basis for decisions. If your Cowork output stays internal and explicitly tentative, lighter review is defensible. The moment it leaves your control, treat every claim as needing a source.

---

## Still Puzzling

The omission problem I described above does not have a clean solution. How do users reliably detect what an agent has left out? Research on how people supervise agentic office workflows and catch omissions in summaries is still limited. The tools exist to catch wrong claims — you can check a citation. No obvious tool exists to surface what should have been included but wasn't. This feels like the most important open question for knowledge-work supervision, and the field has not answered it.

---

## LLM Exercises

**1.** Build a task packet for a real document task you would delegate to Cowork: a report, a synthesis, an extraction. Write out all six components — working folder, allowed files, forbidden files or actions, output specification, source-log requirement, and verification checklist. Before submitting, ask Claude to identify any ambiguities in your packet that could lead to an unexpected result. Revise based on what it finds.

**2.** Take any AI-generated document — your own, a colleague's, or a public example. Apply a four-part verification: identify every claim that can be checked against the stated sources; find at least one claim that needs source confirmation; identify what the document does not include that a careful reader would expect; check whether any information in the document could have come from a source that should have been excluded. What did the exercise reveal about the difference between reading and verifying?

**3.** Ask Cowork to summarize a set of documents and produce a source log alongside the summary. Then ask it to explicitly flag any section where it relied on inference rather than a direct citation from a source document. Compare the flagged sections to the unflagged ones. What does the distribution tell you about where the agent's confidence is reliable for this type of task?

---

## Sources Used

- Anthropic. "Get started with Claude Cowork." *Claude Help Center*. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic. "Use Claude Cowork safely." *Claude Help Center*. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic. "Let Claude use your computer in Cowork." *Claude Help Center*, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork [verify — current as of writing]
- Anthropic. "Organize your tasks with projects in Claude Cowork." *Claude Help Center*. https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-cowork [verify — current as of writing]
- Anthropic. "Assign tasks from anywhere in Claude Cowork." *Claude Help Center*. https://support.claude.com/en/articles/13947068-assign-tasks-to-claude-from-anywhere-in-cowork [verify — current as of writing]
- AgentDojo. "A Dynamic Environment to Evaluate Prompt Injection Attacks and Defenses for LLM Agents." https://agentdojo.spylab.ai/
- Kedziora, D., Siemon, D., and Kedziora, D. "Identifying and Overcoming Challenges in Intelligent Process Automation." *California Management Review*, 2026. https://journals.sagepub.com/doi/10.1177/00081256261434509
- Lacity, M., Willcocks, L., and Craig, A. "Robotic Process Automation and Consequences for Knowledge Workers: a Mixed-Method Study." 2020. https://pmc.ncbi.nlm.nih.gov/articles/PMC7134300/
- Microsoft Research. "Guidelines for Human-AI Interaction." *CHI*, 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
- VPI-Bench. "Visual Prompt Injection Attacks for Computer-Use Agents." *arXiv*, 2025/2026. https://arxiv.org/abs/2506.02456
