# Chapter 3 — Tools, Permissions, and the Action Surface

You ask an AI agent to clean up a folder.

The folder is your primary working directory. It contains a current tax return, signed client contracts, a folder of unedited personal photos, and two years of draft documents. The task sounds reasonable. The agent is capable. Within a few minutes it has sorted, renamed, and in some cases deleted items it classified as duplicates or clutter. The tax documents are where they were. The client contracts are not.

No malice. No error in the ordinary sense. The agent did what "clean up" suggested, bounded only by what you put in front of it. The failure belongs to the person who handed it the whole directory.

This is the central idea of this chapter: an agent's real power is not defined by what it knows, but by what it can touch. That surface — everything the agent can observe, modify, call, delete, or send — is something the human designs before the work starts. It cannot be fixed after overreach.

---

## The Action Surface

Every agent operates inside a boundary. That boundary is not determined by the task description or the agent's good intentions. It is determined by the access you grant.

Call this the **action surface**: the complete set of things the agent could affect if its reasoning, plan, or context led it there. The action surface is not the same as what the agent will do. It is what it could do. That distinction matters because agent errors — and adversarial manipulations of agent reasoning — expand harm in proportion to the surface, not in proportion to the intended task.

The access types available in modern agentic systems form a rough hierarchy by risk. Chat-only is at one end: the agent reads what you paste and produces text; it cannot reach anything outside the conversation. At the other end sits computer use — the agent can observe and interact with anything visible on the desktop, any running application, any field, any button. Between them are uploaded files, local folder access, connectors and MCP servers, browser sessions, and terminal commands, each step extending the range of what can go wrong.

The pattern is stable regardless of which specific products are involved: the more the agent can touch, the larger the blast radius of any error.

<!-- → [TABLE: Access Type | What It Can Expose or Change | Risk Level | Default Rule — rows for Chat only, Uploaded file, Local folder, Connector/MCP, Browser, Terminal/API, Computer use, Scheduled task] -->

---

## Least Privilege

Jerome Saltzer and Michael Schroeder named the governing principle in 1975: every program and every user should operate using the least set of privileges necessary to complete the job (Saltzer and Schroeder, 1975). The security world has refined the language. The concept has not changed.

For agentic AI, least privilege means: grant the minimum tools the task requires, restrict folders to those the task needs, exclude connectors the task has no business using, and never grant production credentials, billing access, or deletion authority for routine work.

The temptation runs in the other direction. A broader action surface appears to make the agent more capable and less likely to need help. In practice, broad access makes errors harder to reverse, makes prompt injection more dangerous, and makes audit harder. OWASP names excessive agency as a core risk in LLM applications: an agent with over-permissive tooling can perform actions or acquire resources beyond what the task requires, enabling downstream harm that would not have been possible on a narrower surface (OWASP LLM Top 10, 2025).

The operating rule is to stand on the lowest rung that lets the task proceed.

---

## The Access Ladder

Think of permissions as a ladder. The cost of being wrong increases at every rung.

**Chat only.** The agent sees what you paste and returns text. No reach outside the conversation. Appropriate for drafting, summarizing content you provide, and thinking through options. This is the default for tasks where you have not specifically thought about access.

**Uploaded file.** The agent can read a specific file you attached. It cannot reach your disk. Appropriate for analyzing a document, checking calculations, or reviewing a draft. Use a copy, not an original containing sensitive data.

**Local folder.** The agent can read, create, and modify files in a designated folder. This is where the opening scene went wrong. The safe version uses a dedicated working folder containing only the files the task needs. Source files stay in their original location; copies go into the working folder.

**Connector or MCP server.** The agent can call an external service — a calendar, database, project tool, or API — through a defined interface. Before enabling a connector, the right questions are: what data can it read, what actions can it take, can it send messages or modify records? OWASP's MCP Top 10 identifies excessive permissions, tool poisoning, and command injection as specific risks when connectors are enabled (OWASP MCP Top 10, 2025).

**Browser.** The agent can load and interact with web pages and applications. Content on those pages can contain instructions that redirect the agent's behavior — a class of attack called visual prompt injection, documented in recent benchmark work (VPI-Bench, 2026). Browser access belongs on trusted public sources; login portals, form submissions, purchases, and message sending require explicit human approval before the agent touches them.

**Terminal or API with commands.** The agent can run shell commands or call APIs with side effects. Packages can be installed, files deleted, external systems modified. Every command should require explicit approval or a carefully constructed policy that logs all actions.

**Computer use.** The agent can observe and interact with anything visible on the desktop. This is the broadest common surface. Because the scope of possible action is hardest to bound at this level, it should be preferred only when connectors and browser access cannot accomplish the task [verify — current as of writing] (Anthropic, "Let Claude use your computer in Cowork," 2026).

**Scheduled tasks.** The agent acts without a human present. The same permission rules apply with stricter defaults: low-risk, reversible work only, with human review of outputs before any consequential downstream action.

---

## Reversibility and Blast Radius

Every tool decision involves two questions, and they are worth asking explicitly.

The first is whether the action is reversible. Moving a file to a temporary folder is reversible. Deleting permanently is not. Drafting an email is reversible. Sending it is not. Editing a copy is reversible. Overwriting an original is not. The harder an action is to undo, the more it deserves a human approval gate before it executes.

The second is the blast radius: if the agent's plan is wrong, or if untrusted content in a document or web page manipulates the agent's reasoning, how much damage can result? An agent with read access to one folder and write access to one output file has a small blast radius. An agent with broad folder access, terminal access, and an active connector to an email service has a large one. Scale the approval friction to the blast radius, not to the plausibility of the plan.

Recent work on tool risk mitigation for agentic AI formalizes this intuition. The AgenTRIM approach proposes least-privilege tool filtering and validation of tool calls as a risk-reduction layer, on the grounds that tool selection is itself a security surface: the agent must not be able to reach what it does not need (AgenTRIM, 2026).

---

## Plugins and Permission Bundles

A plugin bundles tools, connectors, and sometimes sub-agents into a single installable package. The convenience is real: one installation and the agent gains multiple capabilities. The risk is that users evaluate plugins by their advertised function, not by their permission footprint.

Before enabling a plugin, ask the same questions you would ask about any tool: what can it read, what can it write or send, what external systems can it touch. Every plugin is a permission bundle. A plugin that sounds like a calendar assistant may have read access to email, the ability to send invites, and a connection to a task management service. The advertised function is calendar. The actual surface is larger.

The same logic applies to MCP servers. The protocol is designed to give agents controlled access to external capabilities. Controlled is a design goal, not a guarantee. Every new server is a new action surface that must be evaluated before it is enabled.

---

## Permission Design Before the Task

The action surface is the human's responsibility. The agent uses what it is given. Before any agentic task that involves more than chat, these questions belong at the start:

Does the task actually require this access type, or is a lower rung sufficient? Is the folder, connector, or API access as narrow as it can be while still enabling the task? Are credentials, personal information, client records, financial data, and regulated data outside the working scope? If the agent makes an error, can it be undone without data loss or external consequence? For irreversible or external-facing actions, is there a step where a human reviews and approves before execution? Will the agent record what it did — what files it changed, what commands it ran — so that review is possible? What will confirm that the action was correct: row counts, before-and-after snapshots, test results, a source log? If the agent encounters something unexpected, does it pause and surface the question rather than improvising with broader access?

NIST's AI Risk Management Framework describes the need for documented controls and residual risk for AI systems in deployment (NIST AI RMF 1.0, 2023). These questions are the practitioner version of that discipline for single-user agentic work.

---

## The Plan Is Not a Permission

One misconception is worth naming directly. When an agent produces a detailed, plausible plan, that plan can feel like evidence of careful judgment. It is not a reason to expand access.

The plan describes what the agent intends to do within the access you have given it. It does not reflect what it would do with more. It also does not guarantee that its intentions will survive contact with real files, real error messages, and edge cases that were not in the plan. Approving access because the plan looks reasonable is a form of motivated reasoning. The access question should be answered before the plan exists, on the basis of what the task requires — not on the basis of how confident the agent sounds.

---

## What the Opening Scene Required

Go back to the clean-up task. The failure was not that the agent was given the task. It was that the agent was given the wrong scope. The correct version of that delegation might have been: create a dedicated working folder, copy a specific subset of documents into it, describe the organization rule in writing, let the agent work within that folder, review what it produced before touching the originals.

Nothing in that approach prevents the task from being done. It prevents the task from being done wrong on a surface too large to recover from.

The action surface is the boundary you draw. Draw it before the agent starts.

---

<!-- → [TABLE: Permission Design Checklist — two columns: Question | What a Good Answer Looks Like — rows for tool needed, scope limited, sensitive data excluded, reversibility, approval point, action log, verification evidence, stop condition] -->

---

*LLM Exercise: You are setting up an agent to extract key dates and deliverables from a folder of twelve project contracts and populate a tracking spreadsheet. Define the action surface: list every access type required, state whether each is read or write, identify which actions are irreversible, and specify one approval gate and one verification check for this workflow.*

---

## Sources

- Saltzer, J. H. and Schroeder, M. D. "The Protection of Information in Computer Systems." *Proceedings of the IEEE*, 1975. https://web.mit.edu/Saltzer/www/publications/protection/
- OWASP. "Top 10 for LLM Applications 2025." https://owasp.org/www-project-top-10-for-large-language-model-applications/
- OWASP. "Top 10 for Model Context Protocol." https://owasp.org/www-project-mcp-top-10/
- Anthropic. "Use Claude Cowork safely." *Claude Help Center*. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic. "Let Claude use your computer in Cowork." *Claude Help Center*, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork [verify — current as of writing]
- Anthropic. "Use plugins in Claude Cowork." *Claude Help Center*, April 9, 2026. https://support.claude.com/en/articles/13837440-use-plugins-in-cowork [verify — current as of writing]
- AgenTRIM. "Tool Risk Mitigation for Agentic AI." *arXiv*, 2026. https://arxiv.org/abs/2601.12449
- VPI-Bench. "Visual Prompt Injection Attacks for Computer-Use Agents." *arXiv*, 2025/2026. https://arxiv.org/abs/2506.02456
- NIST. *Artificial Intelligence Risk Management Framework (AI RMF 1.0)*. 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10

---

*Tags: #claude #agentic #ai #permissions #least-privilege #action-surface #supervision #Medhavy*
