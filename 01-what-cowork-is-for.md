# What Cowork Is For

## Capability Built

By the end of this chapter you will be able to decide whether a task belongs in Cowork, Claude chat, Claude Code, or human-only judgment — before you start working.

---

## Four Tasks Walk Into Your Inbox

It is Tuesday morning. You have four things on your list.

**First:** A colleague sent you a draft memo and asked you to make the tone crisper. One file, one pass, done.

**Second:** You need a weekly operations packet — a compiled summary pulling together field notes from three staff members, a budget snapshot from a spreadsheet, and a status table from last week's meeting notes. Everything exists somewhere. Nothing is in the same place.

**Third:** Your organization's website is broken. A developer flagged a script error in the repository and asked if you can look at it.

**Fourth:** A request arrived to pull together a summary of student accommodation records to help a committee decide on an appeal.

These four tasks look superficially similar — all involve reading something, thinking about it, and producing something. But they belong on four completely different surfaces, and routing each to the wrong place wastes time at best and causes real harm at worst.

This chapter gives you a routing model. You will use it every time you consider opening Cowork.

---

## The Four Routes

The book's central routing question: Is this task mainly conversation or thinking? Does it require files, folders, and saved artifacts? Does it require codebase action or command execution? Is it sensitive, regulated, irreversible, or judgment-heavy in a way that requires a human to stay in the chair?

Four routes follow:

**Chat** is for thinking, drafting, explaining, critiquing, brainstorming, and short source-grounded tasks where you stay close to the output and no files are needed beyond what you paste directly. The memo rewrite is chat. You paste it, describe the tone change, and review the reply.

**Cowork** is for file-based, multi-step, document-heavy tasks where the output needs to be saved, the sources are in a folder, and the work has more than a step or two. The operations packet is Cowork. The sources are real files, the output is an artifact, and the task would be tedious to coordinate by hand.

**Claude Code** is for codebase work: repositories, scripts, tests, diffs, command execution, and debugging that requires looking at and changing code files in context. The website script error is Claude Code territory. It requires understanding the codebase, not just the document (Anthropic, "Claude Code overview").

**Human-only** is for sensitive, regulated, irreversible, external-facing, or ethically judgment-heavy work where delegation is inappropriate or has not been approved through a formal governance process. The student accommodation records are human-only, or require a formally approved and governed system — not a casual Cowork session (Anthropic, "Use Claude Cowork safely").

---

## What Makes a Task Cowork-Shaped

Cowork fits tasks with the following signals (Anthropic, "Get started with Claude Cowork"):

- Multiple source files that all need to be read
- An output that must be saved rather than just displayed
- More than one or two steps in sequence
- A result you can define in advance and verify afterward
- Source materials in a bounded, accessible folder

When those signals are present, Cowork earns its setup cost. When they are absent, the overhead — defining a workspace, writing a task brief, reviewing a plan — is not worth it.

One useful test: Could you hand this task to a capable temporary assistant with the files and written instructions, and know what good output looks like? If yes, it is probably Cowork-shaped. If you would have to sit with the assistant for an hour explaining context, organizational history, and unwritten norms, that invisible context is the human gate, and the task needs more preparation or more human judgment than Cowork replaces.

---

## The Routing Matrix

| Task signal | Best route | Why |
|---|---|---|
| Need ideas, draft text, or critique | Chat | Conversation is enough |
| Need files assembled into an artifact | Cowork | Workspace and saved-artifact task |
| Need code changes, tests, or a diff | Claude Code | Engineering workflow, repo context |
| Need final judgment on a person's situation | Human-only | Accountability stays human |
| Need to send or submit something externally | Human final action | Consequence is outside the workspace |
| Work involves regulated or sensitive data | Approved governed workflow | Policy or legal constraint |
| Task is repetitive but data is sensitive | Human-only or governed | Frequency does not reduce sensitivity |

This matrix is a starting point, not a formula. Parasuraman, Sheridan, and Wickens (2000) developed a model for types and levels of human interaction with automation that makes a similar point: the right level of automation for a task depends on context, reliability, consequences, and the human's ability to detect errors. Higher automation is not always better automation.

---

## The Ironies of Routing Wrong

Bainbridge's classic 1983 paper "Ironies of Automation" identified a trap that applies directly to Cowork routing: the more you delegate to automation, the more critical your own skill and attention becomes when the automation fails or reaches its limits. If you route a task to Cowork when it is not ready for Cowork — vague sources, ambiguous goal, no verification path — you do not get a free result. You get a plausible-looking artifact that may be wrong in ways that are hard to catch.

Routing a task to chat when it should be Cowork wastes your time on manual assembly. Routing a task to Cowork when it should be human-only creates risk. Routing a task to Claude Code when it belongs in chat is overpowered. Getting the route right is the first part of the work.

Dell'Acqua et al. (2023) found in a study of knowledge workers that AI assistance helped on some tasks and could hurt on others — specifically where workers over-trusted outputs on tasks outside the tool's actual competence. The "jagged frontier" of AI capability means some tasks that look similar perform very differently. Your routing model needs to be robust.

---

## Thinking Through the Four Cases

Return to Tuesday morning's four tasks.

**Memo rewrite:** Chat. Paste the memo, describe the tone, review the reply. If the memo is one document and the task is one pass, there is no workspace to prepare, no artifact to save, and no multi-step plan to review. Cowork would be setup without payoff.

**Operations packet:** Cowork. The task has three source files in separate locations, a structured output format, and a recurring weekly shape that rewards a saved project. You will write a task brief, review a plan, and verify that the output accurately represents the source data. This is the chapter's canonical Cowork task.

**Website script error:** Claude Code. The issue lives in a repository. Understanding the error requires seeing the code context, running tests, and producing a diff — not summarizing documents. Non-developers should either hand this to a developer or learn Claude Code separately. Cowork is not the right surface.

**Student accommodation records:** Human-only. Student records are regulated, private, and identity-linked. A committee decision based on these records carries institutional accountability. Even if Cowork could produce a summary, the question of whether AI should touch this data at all requires formal authorization, not a quick session. The answer here is not "use a smaller folder" — it is "this task requires a governed process or a human decision-maker."

---

## When Chat Is Enough — And That Is Good

A common mistake is treating Cowork as the more advanced, therefore better, surface. It is not. It is a different surface for a different task shape.

Chat is excellent for:
- Drafting short documents from materials you paste directly
- Brainstorming, critiquing, and explaining
- Summarizing a single document you can share
- Answering questions with reasoning or analysis
- Revising text with feedback

Chat can also create and edit files in some workflows [verify — current as of writing], which means the boundary between chat and Cowork is not always the presence of a file (Anthropic, "Create and edit files with Claude"). The cleaner distinction is task shape: workspace plus saved artifact plus multi-step plan signals Cowork; conversation with direct review signals chat.

Using chat when it is sufficient is not a failure of ambition. It is good judgment.

---

## Human-Only Is Not Failure

The fourth route — human-only — deserves its own defense. In workplaces under pressure to use AI everywhere, it is easy to read "human-only" as "stuck in the past" or "not using the tool well." That reading is wrong.

Human-only is the appropriate designation for:
- Decisions with real consequences for real people: personnel, accommodations, benefits, legal status
- Regulated information: health records, student records, financial filings, legal documents
- Irreversible actions: deleting files permanently, sending external communications, making commitments on behalf of others
- Ethically ambiguous judgment: situations where values, context, and relationships matter more than pattern matching

Microsoft's Guidelines for Human-AI Interaction (2019) frame appropriate human control as a design goal, not a failure state. NIST's AI RMF (2023) places human oversight at the center of responsible AI use, not as an add-on for compliance.

Saying "this task belongs to a human" is a sign of routing competence. It is the right answer to certain tasks, and recognizing those tasks is a skill this book treats seriously.

---

## The Human Gate in This Chapter

The routing decision is itself a human judgment. Cowork cannot decide whether a task should be in Cowork. You can.

Before opening any session, ask:
1. Is this task mainly conversation, or does it require files and a saved artifact?
2. Does it require codebase action?
3. Does it involve sensitive, regulated, or irreversible consequences?
4. Is there a clear verification path — could I check whether the output is right?

If you cannot answer question four, stop. A task without a verification path is not ready for Cowork regardless of its other signals.

---

## Common Mistakes

**"Cowork is always better than chat."** Cowork has setup cost — workspace, brief, plan, review. For a simple task, that cost exceeds the benefit.

**"The most powerful surface is the right surface."** Routing by power rather than fit is how work gets slower, not faster.

**"A repetitive task is automatically safe to delegate."** Repetitive means the risk is repeated, not reduced. Student records produced weekly are still student records.

**"Code work is just another document task."** Code in a repository requires code context — version history, tests, dependencies — that Cowork does not have and does not need.

**"Human-only is a failure to use AI."** It is a judgment call. Some tasks require human accountability not because AI could not produce an output, but because producing the output is not the point — being responsible for the decision is.

---

## Try This

**Exercise 1 — Route your queue.** Take your actual task list for the week. For each item, apply the four-question routing test. Assign each to chat, Cowork, Claude Code, or human-only. Note any tasks where you are uncertain about the route — those are worth examining before you start.

**Exercise 2 — Find the operations packet in your work.** Most knowledge workers have at least one recurring document or report that pulls from multiple sources: a weekly status update, a monthly summary, a grant progress report, a board briefing. Identify one. Write down where the source files currently live. That task is likely Cowork-shaped. You will build a full brief for it in Chapter 4.

---

## What Would Change My Mind

The four-route model depends on the current state of AI tools. If Claude chat gains reliable multi-file workspace capabilities indistinguishable from Cowork, the chat/Cowork boundary will need revision. If Claude Code becomes accessible to non-developers without a terminal, the code boundary will shift. If Cowork gains formal governance capabilities for regulated data, the human-only boundary for some tasks may move.

The principles — fit by task shape, verification path required, accountability stays human — are stable. The specific routing calls should be revalidated as products evolve. All product claims in this chapter are current as of writing; re-check before using this book as an onboarding guide.

---

## Still Puzzling

- How should teams document routing decisions so that colleagues use the same model?
- As AI tools converge in capability, does the routing model collapse into fewer categories or more nuanced ones?
- What happens when organizational IT policy restricts which surfaces employees may use? Should the routing model include a "governed only" fifth route?

---

## Bridge to Chapter 2

You now know when Cowork belongs on a task. The next question is what the workspace must look like before Cowork can act safely. Chapter 2 teaches the practical discipline of preparing a bounded, intentional workspace — because the folder Cowork can see and the folder that exists on your machine are two different things, and closing that gap is your job before the task begins.

---

## Sources Used

- Anthropic, "Claude Cowork," product page. https://www.anthropic.com/product/claude-cowork [verify — current as of writing]
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic, "Create and edit files with Claude," Claude Help Center. https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude [verify — current as of writing]
- Anthropic, "Claude Code overview," Claude Code Docs. https://code.claude.com/docs [verify — current as of writing]
- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Bainbridge, L., "Ironies of Automation," Automatica, 1983. https://doi.org/10.1016/0005-1098(83)90046-8
- Dell'Acqua et al., "Navigating the Jagged Technological Frontier," Harvard Business School Working Paper, 2023. https://www.hbs.edu/faculty/Pages/item.aspx?num=64700
- Microsoft Research, "Guidelines for Human-AI Interaction," CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
- NIST, "Artificial Intelligence Risk Management Framework (AI RMF 1.0)," 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
- Parasuraman, R., Sheridan, T.B., and Wickens, C.D., "A Model for Types and Levels of Human Interaction with Automation," IEEE Transactions, 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
