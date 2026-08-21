# Chapter 3 — Connectors, Local Files, and Permissions

## TL;DR

- Cowork can reach your files through several different paths: uploaded files, local folders, cloud connectors, browser access, direct computer use, and plugins.
- Each path carries a different risk profile. Granting the widest access for convenience is how things go wrong.
- The principle of least privilege means giving Cowork only the access the specific task needs — nothing broader, nothing extra.
- Before starting any task, you should be able to answer: what can Cowork read, write, send, delete, or schedule with the access I just granted?

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to:

- Name each type of access Cowork can use and describe its risk profile.
- Choose the narrowest access method appropriate to a given task.
- Write an access-review prompt before starting a Cowork task.
- Know when to refuse a connector or plugin because its scope is too wide.
- Treat permissions as a routine design step, not an afterthought.

---

## Opening Scene

Mariana works in grants management at a regional nonprofit. Tuesday morning she has a deadline: a funder wants a summary of the last eighteen months of program reports. The files are scattered — some in a shared drive folder, some in her local downloads, one set she saved as PDFs from a portal. She opens Cowork, connects her Google Drive account because that is where most of the files are, and types: "Summarize our program reports for the past 18 months."

She does not stop to think about what "connect Google Drive" means in practice. The connector gives Cowork access to everything in her account — grant applications, financial records, a folder shared with a board member, draft correspondence with a funder she has not yet sent. None of that was the task. But it is all now visible to a working agent.

The summary Cowork produces is good. It also, in one paragraph, quotes from a draft email she had stored in Drive that was never meant to be a source document. Mariana catches it before sending. But only barely.

The issue was not Cowork's output. The issue was the access decision she made in thirty seconds without thinking through what it covered.

This chapter is about making that decision deliberately.

---

## The Access Ladder

Cowork can reach work materials through several distinct paths. Think of them as a ladder: each rung gives more capability but also more exposure (Anthropic, "Use Claude Cowork safely"; Anthropic, "Let Claude use your computer in Cowork" [verify — current as of writing]).

| Access type | Best use | Risk |
|---|---|---|
| Uploaded/task files | Bounded source packet for a single task | File contents exposed; batch limits apply |
| Local folder | Multi-file work from a prepared workspace | Extra files included if the folder scope is too broad |
| Cloud connector | Structured access to a specific service | May expose entire account unless scoped |
| Browser assistance | Public web interaction, reference lookups | Untrusted page content; logged-in state risk |
| Computer use | Desktop apps, fallback when no connector exists | Visible desktop exposed; high supervision needed |
| Plugin or MCP server | Extended tools and capabilities | Capability expansion that may not be visible |
| Scheduled task | Recurring low-risk automation | Unattended action; harder to catch errors |

Reading down the ladder is reading toward more power and more exposure. The discipline is to climb only as high as the task requires.

---

## Each Access Type in Plain Terms

### Uploaded and Task Files

The most controlled access. You choose specific files, attach them to a Cowork session, and those files become the working material. Nothing else is included. When the session ends, the access ends.

Use this for: one-time tasks with a clear source set. Prepare the files first (copies, not originals), attach them, and the access surface is as small as it gets.

### Local Folder Access

Cowork can work with a folder you designate on your computer. This is useful for multi-file tasks where Cowork needs to inventory, read, and potentially write files across a set. The risk is that folder scope can be too wide — a folder containing your working files, your archived files, and an unrelated shared project gives Cowork access to all three [verify — current as of writing].

Use the safe-workspace discipline from Chapter 2: a dedicated task folder with copies, not originals, and nothing irrelevant inside it.

### Cloud Connectors

Connectors give Cowork structured access to a service — a cloud drive, a project platform, a document system. They are often more reliable than screen-scraping approaches because the connection is explicit. But "connecting" a service often means connecting your whole account, not just the folder you had in mind.

Before enabling a connector, ask: what does this connector actually access? Is there a way to limit it to a specific folder, project, or document set? If the connector exposes the full account and the task needs three files, the connector scope is too wide. Use uploaded files instead (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

Read permissions sound harmless. They are not. If Cowork can read a document, it can incorporate that document into any output it produces. Sensitive documents, draft communications, financial records, and personnel files can all become source material if they sit inside the connected scope.

### Browser Assistance

Browser access lets Cowork gather information from web pages. It is useful for reference lookups, collecting publicly available data, or interacting with web interfaces that have no connector.

The risk has two parts. First, untrusted page content: a web page can contain instructions that an agent might follow, a technique researchers call prompt injection (OWASP, "Top 10 for LLM Applications 2025"; VPI-Bench, arXiv 2025/2026). Second, logged-in state: if Cowork opens a browser where you are already signed into accounts, it now has access to whatever those accounts contain. A task brief should explicitly prohibit form submissions, purchases, account actions, and access to private portals (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

### Computer Use

Computer use means Cowork can see your screen and interact with applications directly — clicking, typing, reading visible content. It is the most capable access method and the least controlled. Everything visible on the desktop becomes part of the working context, including open documents, notifications, and background apps.

Anthropic recommends treating computer use as a supervised, high-attention mode. Approve each step, not just the overall plan. Keep sensitive windows closed. Close anything you would not want read aloud (Anthropic, "Let Claude use your computer in Cowork" [verify — current as of writing]).

### Plugins and MCP Servers

Plugins bundle additional capabilities: extra tools, connectors, sub-agents, or local server connections. When you enable a plugin, you may be adding access types you did not review individually. A plugin that includes an email connector and a file-write tool is a substantially different permission grant than the base Cowork session.

Before enabling a plugin, inspect what it adds. Ask: what connectors does this include? What tools does it expose? What local capabilities does it create? The Model Context Protocol (MCP) is the technical layer that allows these extensions; users do not need to understand the protocol, but they do need to understand what the plugin enables (Anthropic, "Use plugins in Claude Cowork" [verify — current as of writing]; MCP Documentation, "Understanding MCP servers").

Prefer plugins from sources you recognize and trust. A plugin from an unknown source that requests broad permissions is a risk, not a convenience.

### Scheduled Tasks

Scheduled tasks run without your active supervision. That is their purpose and their hazard. A scheduled task that reads a folder and produces a summary is relatively low risk. A scheduled task that sends messages, submits forms, moves files, or deletes records is high risk: if something goes wrong, it will keep going wrong on schedule until you notice.

Keep scheduled tasks narrow: read-only or read-and-draft operations, with human review before any output leaves your machine. Never schedule deletion, external submission, or irreversible file operations without an explicit approval gate (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

---

## The Principle of Least Privilege

The organizing principle for all of the above is least privilege: grant only the access the task requires, and nothing more (Saltzer and Schroeder 1975). This is one of the foundational rules of information security, established fifty years before agentic AI. It applies here unchanged.

Least privilege is not about distrust. It is about limiting the blast radius if something goes wrong — if Cowork misinterprets an instruction, encounters an unexpected file, or gets manipulated by untrusted content. The smaller the access surface, the smaller the damage.

In practice, least privilege means answering these questions before granting access:

1. What source does this task actually need?
2. Can I use uploaded files instead of a folder or connector?
3. If a connector, can it be scoped to a folder or project rather than the full account?
4. Is browser access necessary, or can the information come from uploaded sources?
5. Is computer use necessary, or is there a connector that handles this?
6. What can Cowork read, write, send, delete, or schedule with this access?

If you cannot answer question 6, you have not finished your access review.

---

## Access Review: A Prompt to Use

Before starting a task, ask Cowork to declare its access requirements. Paste this into your session:

> "Before we begin: list exactly what access you need for this task, why each access type is necessary, what you will not touch, and what actions require my explicit approval before you proceed."

Cowork's response becomes your access agreement for the session. If the list includes access you did not intend to grant — or actions you did not expect — redirect before any files are opened (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

---

## The Human Gate for This Chapter

The access decision is not a technical setup step. It is a judgment call that belongs to you.

Cowork cannot know which files are sensitive, which cloud folder contains draft board communications, or which scheduled task would be embarrassing if it ran during an audit. You know those things. The access review is where that knowledge is applied.

Before any Cowork task, the questions are:

- What is the smallest access surface that still lets the task succeed?
- Have I scoped this to copies, a prepared folder, or a limited connector rather than my full account?
- Do I understand what actions are enabled by the access I am granting?
- Am I comfortable with what Cowork could do if it makes a reasonable error?

If the answer to any of these is no, narrow the access before proceeding.

---

## Common Mistakes

**Connecting the full account because it is faster.** Speed is not a reason to expand access. Prepare a task folder and upload files instead.

**Assuming read-only access is safe.** Read access means read everything in scope. A sensitive document inside a connected folder can become source material.

**Enabling a plugin without reviewing what it adds.** A plugin's capabilities are additive. Inspect what each plugin includes before enabling it.

**Using browser access for tasks that uploaded files would cover.** Browser access exposes logged-in state and untrusted content. Use it only when necessary.

**Scheduling high-stakes tasks without an approval gate.** Unattended automation plus irreversible action is a setup for unnoticed errors.

**Treating an access prompt as a formality.** Approving access without reading it means you have delegated the access decision to chance.

---

## Try This

**Exercise 1 — Access audit before a task.** Think of a Cowork task you might run this week: a document summary, a folder cleanup, a data extraction. Write out the access decision before starting:

- What is the narrowest access that would work?
- What would the broadest convenient access include that you do not actually need?
- What is the smallest scope you could grant?

Then, if you run the task, paste the access-review prompt above and check whether Cowork's stated access requirements match what you intended.

**Exercise 2 — Connector scope check.** If you have a cloud connector enabled (Google Drive, OneDrive, or similar), investigate what it actually accesses. Can you limit it to a folder? Does it include shared drives, archived materials, or documents from other people? Write one sentence describing the gap between what you thought the connector accessed and what it actually accesses. Then decide whether you would scope it differently before the next task.

**Exercise 3 (optional) — Plugin inspection.** If your Cowork session includes any plugins, find one and list the tools, connectors, and capabilities it adds. Could you achieve the same result without the plugin? Is there a plugin whose capability expansion you had not previously noticed?

---

## What Would Change My Mind

This chapter recommends least-privilege access as the default. That recommendation would soften if:

- Cowork developed clear, auditable scope controls that let users grant per-task access with automatic expiry, making broad connector grants genuinely time-limited and inspectable.
- Research showed that nontechnical users who prepare narrow access surfaces are not meaningfully safer in practice than those who use broader defaults — though current evidence on AI agent access and harm strongly favors the caution (OWASP 2025; AgentDojo research).
- Product interfaces made access scope so visible that no additional discipline was needed from the user.

None of those conditions exist today. The discipline remains necessary.

---

## Still Puzzling

- How should nontechnical users interpret access prompts when product language is vague? ("Connect your account" is not an access specification.)
- What organizational policies should govern connector grants in workplace Cowork use? The book cannot answer this for every employer.
- As Cowork adds new integrations, how will users know what capabilities a connector or plugin adds? The research gap here is real (OWASP, "Top 10 for MCP" [verify — current as of writing]).

---

## Bridge to Chapter 4

You now know what Cowork can access and how to limit it. The next question is: what do you actually ask Cowork to do?

A vague instruction over a bounded workspace still produces unpredictable work. Chapter 4 introduces the task brief — the structured document that turns messy intent into an executable plan. With access scoped and a brief written, Cowork has what it needs to produce a plan worth reviewing.

---

## Sources Used

- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic, "Let Claude use your computer in Cowork," Claude Help Center, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork [verify — current as of writing]
- Anthropic, "Use plugins in Claude Cowork," Claude Help Center, April 9, 2026. https://support.claude.com/en/articles/13837440-use-plugins-in-cowork [verify — current as of writing]
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Model Context Protocol Documentation, "Understanding MCP servers." https://modelcontextprotocol.io/docs/learn/server-concepts
- OWASP, "Top 10 for Model Context Protocol." https://owasp.org/www-project-mcp-top-10/ [verify — current as of writing]
- OWASP, "Top 10 for LLM Applications 2025." https://owasp.org/www-project-top-10-for-large-language-model-applications/
- Saltzer, J. H., and Schroeder, M. D. "The Protection of Information in Computer Systems," 1975. https://web.mit.edu/Saltzer/www/publications/protection/
- VPI-Bench, "Visual Prompt Injection Attacks for Computer-Use Agents," arXiv, 2025/2026. https://arxiv.org/abs/2506.02456
- AgentDojo, "A Dynamic Environment to Evaluate Prompt Injection Attacks and Defenses for LLM Agents." https://agentdojo.spylab.ai/

---

## AI Wayback Machine

**Dorothy Denning** developed foundational work in information flow security — tracing how data moves between systems and what happens when that movement crosses an inappropriate boundary. Her framework maps directly onto a Cowork connector: the question is not just whether Cowork can read a document, but where that document's content flows afterward.

**Run this:**

```
Who was Dorothy Denning, and how does information flow security apply to AI agents that can access cloud connectors and local files? Keep it to three paragraphs. End with one question about data flow her work raises that modern AI tools have not yet resolved.
```

→ Search **"Dorothy Denning information security"** for background.

**Now make the prompt better.** Try one of these:

- Ask how her concept of a "security lattice" might apply to Cowork's access ladder: files, connectors, browser, computer use.
- Ask what Denning might say about the difference between access control (who can read) and information flow control (where information goes after it is read).

What changes? What gets more specific? What new risk becomes visible?
