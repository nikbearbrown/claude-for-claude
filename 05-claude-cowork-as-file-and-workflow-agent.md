# Chapter 5 — Claude Cowork as File and Workflow Agent

## Opening Scene

You have eight PDFs, a spreadsheet with gaps, two folders of meeting notes, and a deadline in three hours. You need a status report. You upload two of the PDFs to Claude chat. Claude produces a polished summary. You upload two more. Claude produces another summary. You paste the summaries together. Something is missing. You cannot tell what the spreadsheet is missing without comparing it against the PDFs you already closed. The sources are scattered, the formatting is inconsistent, and you have burned ninety minutes doing what felt like coordination work rather than thinking work.

This is the gap Claude Cowork is designed to close.

Claude Cowork is not "more powerful chat." It is a different kind of Claude — a file and workflow agent that can access your local files, use connected services, operate a browser, run code in an isolated environment, and carry multi-step task state across a session (Anthropic, "Get started with Claude Cowork") [verify — current as of writing]. The report that took ninety minutes of fragmented chat becomes a single task: "Read the PDFs in this folder, extract action items, compare against the tracker, fill the gaps, and save a formatted report."

This chapter teaches you when Cowork is the right tool, how to scope the work it does, and what you must verify before you use its output.

---

## What This Chapter Lets You Do

After this chapter you will be able to:

- Decide when a task belongs in Cowork versus standard Claude chat
- Specify a Cowork task with clear inputs, outputs, and permission boundaries
- Understand the access ladder: connector, browser, screen interaction
- Identify the verification evidence for common Cowork outputs
- Know which kinds of work Cowork should not handle — and what stop conditions warrant immediately ending a session

---

## The Core Concept: Delegation with Boundaries

The research on robotic process automation and intelligent process automation has documented a consistent finding: automation changes the human role from direct task performance to task framing, exception handling, monitoring, and final decision (Lacity, Willcocks, and Craig 2020; Kedziora, Siemon, and Kedziora 2026). Cowork makes this shift visible at the individual level.

When you delegate work to Cowork, you are not removing yourself from the workflow. You are moving from doing to supervising. That is a different skill. It requires knowing:

- What you are delegating
- What access you are granting
- What the deliverable should look like
- What evidence proves the workflow succeeded
- What data should never enter the session

If you cannot answer those five questions before you start a Cowork session, you are not ready to delegate. You are guessing — and the consequences of a guessing mistake are larger when an agent is acting on your files and services rather than just generating text.

---

## What Claude Cowork Actually Is

Cowork is a mode of Claude Desktop for complex, multi-step work that benefits from local file access and tool use (Anthropic, "Get started with Claude Cowork") [verify — current as of writing]. It is different from standard Claude chat in three ways:

**1. It can access local files.** You decide which folders Cowork can read. Files inside those folders are available as source material. Files outside are not.

**2. It can use connected services.** Through connectors, Cowork can interact with services like calendars, cloud storage, and productivity apps in a structured, limited way. Connectors are the preferred access method because they have defined, predictable behavior (Anthropic, "Let Claude use your computer in Cowork") [verify — current as of writing].

**3. It can operate your computer.** When a connector does not exist for a service, Cowork can use a browser or, as a fallback, directly interact with your screen through computer use. Each step up this ladder brings more capability and more risk.

---

## The Access Ladder

Think of Cowork's access capabilities as a ladder. Each rung gives Claude more reach — and each rung requires more supervision.

| Rung | What it is | Use when | Supervision level |
|---|---|---|---|
| **Chat only** | No local access | Quick questions, drafts, explanations | Standard |
| **File folder** | Selected local files | Document-heavy work with a defined source set | Check file scope |
| **Connector** | Structured service access | Work with apps that have a connector | Verify connector permissions |
| **Browser** | Web/app page interaction | No connector available; source is trusted | Inspect sources; check terms of service |
| **Computer use** | Direct screen interaction | No browser path; desktop app required | Highest; treat as sensitive |
| **Scheduled/remote task** | Action while you are away | Low-risk recurring summaries only | Audit the output before acting on it |

The principle: **prefer the lowest rung that completes the task.** A task that can be done with files in a folder does not need browser access. A task that can be done with a connector does not need screen interaction. Each step down the ladder toward computer use also steps toward more brittleness, more prompt-injection exposure, and less predictable behavior (Anthropic, "Let Claude use your computer in Cowork"; VPI-Bench 2025/2026) [verify — current as of writing].

---

## Worked Workflow: Assembling a Status Report

Here is what a Cowork task packet looks like in practice.

**Situation:** A project manager has twelve weekly notes from three team leads, a shared task tracker in CSV format, and a folder of linked deliverables. She needs a status report by end of day.

**Step 1 — Define the task packet:**

> "Read the twelve meeting notes in the `/weekly-notes` folder and the `tracker.csv` file in `/project-data`. Extract: (1) completed items this week, (2) items that appear in the tracker but are not mentioned in any note (possible gaps), (3) action items with owners not yet reflected in the tracker. Format the output as a three-section markdown report. Save it as `status-report-[date].md` in `/project-data/outputs`. Do not access any other folders. Do not send or share anything."

**What this packet specifies:**
- Inputs named: the two folders, the CSV
- Folder access limited: two specific paths
- Sensitive files excluded: by naming only the folders with project material
- Connectors/tools: file read and write only
- Forbidden actions: no sending, no sharing
- Output file specified: name and location
- Review criteria implicit: three-section structure, completeness check against tracker

**Step 2 — Run and monitor:**
Watch the task log as Cowork works. If it tries to access a folder you did not specify, if it opens a browser for no stated reason, or if it asks for permissions you did not anticipate, stop the session and ask what it was trying to do.

**Step 3 — Verify before use:**
- Open the saved report. Does it have all three sections?
- Spot-check five tracker items against the notes. Did Cowork read the files correctly?
- Check the "gap" list against your own knowledge of the week. Does it match?
- Read the action items list. Are the owners right? Are any sensitive personnel details exposed that should not appear in the report?

Only after this review is the report ready to share.

---

## Task Fit Table

| Task | Cowork fit | Why | Human gate |
|---|---|---|---|
| Summarize one short article | Weak | Chat is enough | Check summary against source |
| Assemble report from a folder of PDFs | Strong | Multi-file, multi-step, saved output | Verify sources, claims, formatting |
| Clean and standardize a CSV | Strong if reversible | File transformation with audit path | Row counts, formulas, spot checks |
| Gather data from trusted websites | Possible | Useful when connector is absent | Source log, terms of service, spot checks |
| Delete old files | High risk | Irreversible action | Manual deletion; explicit approval only |
| Handle health, finance, or legal data | Avoid | Sensitive data + high consequence | Human-only or governed enterprise process |
| Send messages or make purchases | Avoid or strict gate | External consequences | Human final action |
| Fill a slide deck from existing material | Possible | Multi-source, formatted output | Narrative order, data labels, overclaiming |

---

## The Safety Frame

Cowork's official safety documentation is explicit: safeguards are imperfect, access is real, and user judgment is the last line of defense (Anthropic, "Use Claude Cowork safely") [verify — current as of writing].

Three risks deserve specific attention in this chapter:

**1. Prompt injection.**
When Cowork reads documents, web pages, or email content, that content can contain hidden or misleading instructions that try to redirect what Cowork does (AgentDojo; VPI-Bench 2025/2026). The mitigation is to limit trusted sources to files and connectors you control, and to inspect any output that involves reading external or untrusted content.

**2. Scheduled and remote tasks.**
Cowork can accept task assignments from your phone and execute work on your desktop resources without you present (Anthropic, "Assign tasks from anywhere in Claude Cowork") [verify — current as of writing]. This is convenient and expands the risk boundary significantly. Remote task assignment is appropriate only for low-stakes, reversible, well-specified recurring work. It is not appropriate for anything that touches sensitive data, makes external communications, or takes irreversible actions.

**3. Local app computer use.**
When Cowork operates your screen directly, it can see whatever is on your screen — including open files, notifications, browser tabs, and visible system data outside the task scope. Treat this access level the way you would treat giving a contractor unsupervised time in your office. The task should be tightly scoped and the session time should be short (Anthropic, "Use Claude Cowork safely") [verify — current as of writing].

---

## The Stop Condition

If any of these happen during a Cowork session, stop immediately and inspect:

- Cowork accesses a folder or file you did not specify
- Cowork opens a browser for a reason not in the task packet
- Cowork requests permissions for apps or services outside the task scope
- The task scope appears to be expanding without your direction
- An output file is created in a location you did not name

Stopping is not failure. Stopping is supervision. Cowork is a capable agent, not an autonomous employee. The NIST AI Risk Management Framework (NIST 2023) frames AI risk governance around four functions: map, measure, manage, and govern. At the individual workflow level, "stop and inspect" is your manage function.

---

## What Cowork Should Not Handle

Even with careful scoping, some categories of work should not go to Cowork:

- **Sensitive personal data.** Health records, personnel files, financial account data, legal case details.
- **Credentials and secrets.** Passwords, API keys, tokens, private keys.
- **Regulated data.** Anything subject to HIPAA, FERPA, GDPR, or sector-specific compliance requirements unless your organization has explicit governance for it.
- **High-consequence irreversible actions.** Sending communications on your behalf, making purchases, deleting files, modifying production systems.
- **Final editorial or ethical judgment.** Whether the report is accurate, fair, and appropriate to share is your call, not Cowork's.

The research on intelligent process automation is consistent here: automation that handles unstructured data in high-consequence environments requires organizational controls, audit trails, and governance policies that individual users cannot provide on their own (Kedziora, Siemon, and Kedziora 2026).

---

## Common Mistakes

**1. Treating Cowork as chat with a bigger context window.**
Cowork's value is not longer answers. It is the ability to act on files and services. If your task does not require that action, chat is enough and simpler.

**2. Granting folder access without reviewing what is in the folder.**
Before any Cowork session, check the folder. Is there anything in there you did not intend to include — old contracts, personal files, sensitive drafts? Remove or exclude those before starting.

**3. Using computer use before trying a connector.**
Computer use is the fallback, not the default. Check whether a connector covers your service first. Connectors are more reliable, more predictable, and less exposed to prompt injection.

**4. Scheduling tasks that involve sensitive actions.**
A scheduled summary of a public folder is low risk. A scheduled action that touches personnel data, sends messages, or modifies a production database is not appropriate for unsupervised execution.

**5. Treating a finished file as a verified file.**
Cowork can create a well-formatted output that is wrong. Row counts, spot checks, source tracing, and editorial review are all required before the output leaves your hands.

**6. Confusing "Claude can access" with "Claude should access."**
Just because you granted folder access does not mean every file in that folder should be part of the task. Specify inputs. Exclude the rest.

---

## Try This

**Exercise 1 (15 minutes): Scope a task packet.**

Think of a repetitive knowledge work task you do weekly or monthly — a status report, a literature summary, a data cleanup, a presentation assembly. Write a Cowork task packet:
- Inputs named (specific folders or files)
- Allowed access level (file only, connector, browser — no higher than needed)
- Forbidden actions
- Output file name and location
- Three verification checks you will run before using the output

You do not have to run the task. The value of this exercise is in writing the packet and discovering what is ambiguous.

**Exercise 2 (hands-on): Minimum viable delegation.**

If you have access to Claude Cowork, assign a small, reversible, file-only task — for example, "Read the three documents in this folder and produce a one-page summary table saved as `summary.md` in the same folder." Before running it: check the folder for anything sensitive. After running it: verify the output against the originals. Note what the task log showed.

**Exercise 3 (reflection): Draw the stop conditions.**

Return to the task you defined in Exercise 1. Write down three things that, if you saw them in the task log, would cause you to stop the session immediately and inspect. This is your personal stop condition list for this task type.

---

## What Would Change My Mind

This chapter takes a conservative stance on Cowork's appropriate scope, especially for sensitive data and irreversible actions. If Anthropic or independent researchers published strong evidence that Cowork's governance features (permission prompts, deletion protection, content classifiers) reliably prevent unauthorized access and data leakage in realistic knowledge-work settings, I would soften the advice on scope restriction.

I would also revise the access-ladder framing if the connector ecosystem matured to cover most common services, making browser and computer use genuinely rare fallbacks rather than common workarounds. [verify — current as of writing]

---

## Still Puzzling

The empirical research on how nontechnical professionals supervise file and workflow agents in real work settings is thin. The RPA literature (Lacity et al. 2020) and intelligent-process-automation management research (Kedziora et al. 2026) are useful but predate the generative, tool-using agent design that Cowork represents. What supervision habits actually work — and what failure modes emerge — in realistic Cowork deployments at the individual and small-team level is still an open empirical question.

---

## Bridge to Chapter 6

Chapters 3, 4, and 5 have established the three Claude surfaces: chat, Code, and Cowork. Each has different access, different capabilities, and different verification requirements. Chapter 6 brings those threads together with the question that runs beneath all of them: how do you actually know the output is right? The human gate is not an afterthought. It is the competence that makes the whole ecosystem trustworthy.

---

## Sources Used

- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork
- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely
- Anthropic, "Let Claude use your computer in Cowork," Claude Help Center, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork
- Anthropic, "Assign tasks from anywhere in Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13947068-assign-tasks-to-claude-from-anywhere-in-cowork
- Anthropic, "Use plugins in Claude Cowork," Claude Help Center, April 9, 2026. https://support.claude.com/en/articles/13837440-use-plugins-in-cowork
- Mary Lacity, Leslie Willcocks, and Andrew Craig, "Robotic Process Automation and Consequences for Knowledge Workers: A Mixed-Method Study," 2020. https://pmc.ncbi.nlm.nih.gov/articles/PMC7134300/
- Damian Kedziora, Dominik Siemon, and Joanna Kedziora, "Identifying and Overcoming Challenges in Intelligent Process Automation," *California Management Review*, 2026. https://journals.sagepub.com/doi/10.1177/00081256261434509
- VPI-Bench, "Visual Prompt Injection Attacks for Computer-Use Agents," arXiv, 2025/2026. https://arxiv.org/abs/2506.02456
- AgentDojo, "A Dynamic Environment to Evaluate Prompt Injection Attacks and Defenses for LLM Agents." https://agentdojo.spylab.ai/
- NIST, "Artificial Intelligence Risk Management Framework (AI RMF 1.0)," 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10

---

*Tags: #claude-cowork #workflow-agent #file-agent #delegation #verification #human-gate #prompt-injection*
