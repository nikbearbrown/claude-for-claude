# Chapter 1 — Chatbot, Assistant, Agent
*Three words for three different promises about what happens after you press send.*

Here is a fact that took me too long to take seriously: two systems can produce identical text in response to an identical question, and still be fundamentally different things. Not different in quality. Different in kind. One produces a sentence you can use or discard. The other has already changed something in the world before you read the sentence at all.

That distinction is what this chapter is about. Not which AI tool is better. Not which interface is more polished. The question is: what did the system *do*, and when?

---

## One Task, Three Scopes

Take something simple. A developer has a Python function that should return an empty list. It's returning `None`. She wants help.

In the first scenario, she opens a chat interface and types the question in plain English: why would a Python function return `None` instead of an empty list? The system explains — missing return statement, early return path, implicit `None` at a branch end — and shows a corrected example. She reads it, goes to her editor, applies the fix herself, runs her tests. Nothing changed until she changed it.

In the second scenario, she pastes the function into the conversation along with her test file and asks for a rewrite. The system produces a corrected version of the function. She copies it into her editor, runs her tests. The output was more concrete, more directly usable — but the same principle held: nothing changed until she moved it.

In the third scenario, she opens a different tool, points it at her repository, and describes the bug. The system reads the relevant file, identifies the function, proposes an edit, asks for her approval, makes the change, runs the tests, reports the result. Before she reviewed the diff, the codebase had changed.

Same surface task. Radically different action scope. In the first two cases the system produced text that the human acted on. In the third, the system acted, and the human reviewed the action.

That gap — not in output quality, but in what happened in the world and when — is the distinction this chapter builds around.

---

## The Spectrum, Not the Boxes

I want to resist the temptation to offer a clean taxonomy with three sealed categories. The literature on AI agents uses "chatbot," "assistant," and "agent" as useful shorthand, but practitioners who treat these as hard categories get into trouble quickly. The real question is not which label applies but where a system sits on a single continuous axis: **external state change**.

At one end: the system produces text that the user may or may not act on. The world after the conversation is identical to the world before, unless the user decides to do something with what they read.

At the other end: the system takes autonomous multi-step actions — reads files, runs tools, contacts external services, modifies data — that persist after the conversation closes. The world after is different from the world before, and the difference happened before the human reviewed anything.

Between those poles are positions that blend. A system that creates a file you explicitly requested is doing something more than producing text, but far less than running an unsupervised multi-step plan. A system that searches the web and returns a summary has acted in the world, but in a limited and generally reversible way. A system that submits a form on your behalf has taken a consequential external action with potentially no undo.

The useful diagnostic is not "which category is this" but three questions you ask before you start:

1. **What can it see?** — the observation surface
2. **What can it decide?** — the planning and selection scope  
3. **What can it do?** — the action surface

These three questions will return in every chapter of this book. They are the frame for deciding what supervision a given task requires. Let me be concrete about what each question is really asking.

<!-- → [DIAGRAM: Three-column visual showing the see/decide/do framework — each column names the question, gives the chatbot answer, assistant answer, and agent answer; designed to show how the action surface widens progressively toward the agent end] -->

---

## What Chatbots Actually Are

A chatbot, in the operational sense I use throughout this book, is a system whose primary output is text, and that text does not change anything outside the conversation by default. This is not a judgment about sophistication. A chatbot can reason carefully, explain complex concepts with precision, draft polished prose, compare technical options, summarize hundred-page documents. None of that makes it agentic.

What makes it a chatbot is the human's role: interpretive. You read the output. You assess its accuracy. You decide what to do with it. You are the one who emails it, commits it, posts it, or files it. The system generates; you execute.

The observation surface of a chatbot is typically bounded by the conversation context — what you have typed, uploaded, or attached in the current session. It does not, by default, read your file system or see your connected applications. It sees what you have given it.

The decision scope is the response itself: what to say, how to structure it, which angle to take. The system is not choosing between paths that affect external systems. It is choosing between sentences.

The action surface is narrow: text output, and in some interfaces a file download you explicitly trigger. Nothing happens in the world until you make it happen.

Verification for a chatbot is content verification. Is this accurate? Does it match what I know independently? Does it omit something important? Is the source reliable? The error modes are content errors — hallucination, misrepresentation, confident oversimplification. You are not looking for action errors because there were no actions.

---

## What Assistants Add

An assistant occupies the middle of the spectrum. It helps produce an artifact — often working with uploaded files, prior drafts, structured inputs — and may create files or formatted outputs in some interfaces. The user remains close to each step and typically performs external actions manually.

The observation surface is broader: uploaded documents, pasted content, structured inputs across a task. But it is still defined by what the user has explicitly provided. The assistant does not reach out on its own to gather context you did not give it.

The decision scope includes structuring, drafting, extracting, transforming, summarizing. The assistant selects how to organize the work, which facts to surface, how to format the output. It does not autonomously choose to gather more information or extend the task scope.

The action surface is where the boundary begins to blur. A system that creates a file at your explicit request is doing something more than pure text production. A system that creates a file you did not explicitly direct is doing something slightly more than assistance. This is an honest ambiguity, not a failure of the framework. The useful response is to track the three questions rather than force a label.

Verification for an assistant shifts toward structure and fidelity: does the draft accurately represent the sources? Are the facts correct? Does the structure match the intended use? Is anything missing that should be there? You are checking what was produced, not what was done — because the human still did the doing.

---

## What Agents Can Do

An agent is a system that uses tools to act in an environment, observes what happens, and adjusts course accordingly. What the system does persists after the conversation ends.

The research literature on large-language-model-based agents identifies the components: context or observation, planning, tools or actions, memory or state, feedback, and reporting (Tang et al., 2023; Li et al., 2024; Liang et al., 2024). For practitioners, what matters is simpler: the agent can do things you did not manually do, and those things leave marks in the world.

Claude Code is one example. It can inspect a repository, read source files, modify code, run tests, and report results. The user describes an issue; the agent observes the relevant files, proposes a fix, requests approval, makes the change, verifies through test output. Changes persist in the file system.

Claude Cowork is another. It can read documents, assemble reports, transform spreadsheets, search the web, and operate applications. When working in computer-use mode, it can see and interact with the desktop. Each capability extends the action surface and, with it, the supervision requirement.

The observation surface for an agent is potentially broad: the file system, connected applications, browser sessions, APIs, MCP-connected services. This is precisely why scope definition matters before the agent starts. An agent that can see more than you intended can include information you did not mean to share and act on context you did not authorize.

The decision scope includes multi-step planning, tool selection, priority ordering, adaptive revision when steps fail. The agent does not just produce a response. It chooses a path through the work, and that choice is not always visible to the human until after the path has been taken.

The action surface demands the most careful attention. Agents can read files, write files, execute code, send requests to external services, and in some configurations use applications as a human would. Each action type has different reversibility, different blast radius, different verification requirements. File reads leave no mark. File writes change state that persists. Browser submissions and API calls may be irreversible and consequential. For each action type, you need a prior decision: does this require my approval before it happens, or do I trust the agent's judgment within defined bounds?

<!-- → [TABLE: Supervision matrix — rows: chatbot, assistant, agent; columns: produces text output, uses task context, uses tools, changes files or applications, needs permission design, needs action log; cells: No/Sometimes/Yes; caption: use this as a design checklist before delegating a task, not a classification rubric] -->

---

## The See/Decide/Do Frame in Use

Return to the three questions. They are diagnostic tools, not classification criteria. The right moment to ask them is before a task begins, not while auditing what went wrong.

**What can it see?** List what the system has access to: which folder, which connected service, which files, which applications. If you cannot answer this, define it before you start. An agent that can see more than you intended can incorporate information you did not authorize.

**What can it decide?** Describe what the system can choose autonomously — which tools to invoke and in what order, how to structure the output, whether to retry a failed step. The wider the decision scope, the more important the plan review. If the system can decide to extend the task scope on its own initiative, you need an explicit boundary in the prompt, not in your expectations.

**What can it do?** Enumerate the actions and their reversibility. This is the hardest step because it requires you to look past the conversational interface and ask what is underneath it. A system with a friendly chat surface and file-write access is carrying more supervision responsibility than it appears to carry.

The NIST AI Risk Management Framework organizes AI governance around mapping, measuring, managing, and documenting risk (NIST, 2023). The see/decide/do framework is a practitioner's version of the mapping step, applied at the individual task level before delegation begins.

---

## The Ambiguous Middle, Honestly

Some cases do not fit neatly, and I would rather be honest about that now than let it become a problem in Chapter 5.

A chat session where Claude creates a formatted document at your explicit request is closer to assistant than agent — you directed the creation, the scope is clear, the output is a file you review before using. A tightly constrained Claude Code run that can only edit one file and only runs pre-specified tests is far less agentic in practice than its tool access might suggest. An MCP-connected assistant that can read a database is acting with more reach than most users expect from a "chat" interface.

The labels blur because the products blur. Vendors will categorize their systems inconsistently, and the same underlying capability will appear under different names across different products. What matters is not the label but the answer to the three questions. When you have answered them honestly, you know what supervision design the task requires.

Lisanne Bainbridge identified a principle that applies here with uncomfortable directness: automation can increase monitoring and intervention demands, not reduce them (Bainbridge, 1983). Her observation was about industrial automation, but the structure is identical. A system that looks like a chat assistant but has file-write access is carrying more supervision responsibility than it appears to carry. The gap between apparent scope and actual scope is where errors enter without anyone noticing — not dramatically, but gradually, across a dozen small decisions no one reviewed.

---

## Why More Agency Requires More Readiness

There is a persistent misconception I want to name explicitly, because it shapes how people approach agentic tools and shapes it badly: more capability means better results.

It does not. It means more action surface, which means more ways for errors to propagate before they are caught.

A chatbot that misidentifies a bug produces a wrong answer. The user reads it, disagrees, discards it. Cost: a few minutes and some mild frustration.

An agent that misidentifies the same bug, edits files, reruns tests, gets an ambiguous result, patches around the failing test, and reports completion has done more. Whether it has done *better* depends entirely on whether the human read the plan, reviewed the diff, and verified the test result before accepting the report. If the human did not — if they treated the completion report as a proxy for correctness — the bug is now a different bug, living in changed code, wrapped in a test suite that learned to ignore it.

More agency requires the user to be more ready, not less. The Microsoft Research Guidelines for Human-AI Interaction, produced to shape how AI systems should support rather than replace human judgment, frame this as a design responsibility: systems should support appropriate levels of user control, uncertainty disclosure, and recoverability (CHI 2019). For agents, those properties must be designed in before use, not discovered after the first significant error.

Parasuraman, Sheridan, and Wickens make the underlying architecture explicit: levels of human interaction with automation differ by what the system selects, recommends, executes, and confirms (2000). As the system takes on more of those functions autonomously, the human's role shifts from executing to supervising. But the supervisory demands increase in kind. This is not a paradox. It is the fundamental structure of delegation: you hand off the doing, and you take on the auditing. The auditing is not easier than the doing. In some respects it is harder, because the auditor must understand enough to catch errors they did not make.

<!-- → [DIAGRAM: Error propagation comparison — two parallel timelines showing chatbot error (wrong answer → user discards) vs. agent error (wrong plan → file edit → test patch → completion report → downstream problem); caption: the issue is not the error itself but when it surfaces relative to human review] -->

---

## What the Labels Are Really For

The chatbot/assistant/agent framework is not a grading system. A chatbot is not worse than an agent; an agent is not a more advanced form of chatbot. They are different scopes of action, appropriate for different tasks with different supervision requirements.

A chatbot is the right tool when the value is in the text — an explanation, a draft, a comparison — and the human needs to apply judgment before anything happens in the world.

An assistant is the right tool when the work is artifact production — a document, a structured output, a transformation of something you provided — and you want the system to handle the labor of production while you retain the decision about where the artifact goes.

An agent is the right tool when the task involves multi-step action in an external environment — a codebase, a file system, a connected service — and you have done the preparation to define scope, review plans, and verify actions. Not before. After.

The question this chapter leaves you with is not which category your current tool belongs to. The question is: the next time you hand a task to an AI system, do you know what it can see, what it can decide, and what it can do? If the answer is "not exactly," that gap is what Chapter 2 is for.

---

## LLM Exercises

**Exercise 1.** Open a large language model chat interface. Ask it: "What can you see in this conversation? What tools do you have access to? What can you change outside this conversation?" Compare what it reports to what you know about the interface. Note any gap.

**Exercise 2.** Describe a workflow from your own work that you are considering delegating to an AI system. Write a one-paragraph answer to each of the three diagnostic questions — what it can see, what it can decide, what it can do — for that specific task. Then identify the one action in the workflow that should require your explicit approval before it executes.

**Exercise 3.** Find two AI tools that use different interface metaphors (a chat tool and an agentic tool, or two products from different vendors). Ask each the same question. Compare not the answers but the action surfaces — what did each system do to produce its response? Were there any external state changes? What supervision design would each require if you delegated a consequential task?

---

## Graduated Exercises

**Warm-up**

1. *Recall and identify.* A colleague tells you they used "an AI assistant" to reorganize their file system automatically while they stepped away. Based on the see/decide/do framework, which position on the spectrum does this describe, and what one supervision step should have been in place before the task ran?

2. *Label the observation surface.* You paste a PDF into a chat interface and ask for a summary. Describe the observation surface in one sentence. Does this interaction require permission design? Why or why not?

**Application**

3. *Apply the supervision table.* You are evaluating a tool that can read your email inbox, draft replies, and — with your approval — send them. Fill in the supervision table for this tool. Which row does it most closely match, and which cell creates the most supervision responsibility?

4. *Identify the gap.* A system presents a conversational interface but has underlying access to a connected database and can run read/write queries based on your natural-language requests. What gap exists between its apparent scope and its actual scope? What one question would you ask before delegating a data-entry task to it?

5. *Reversibility audit.* List three actions an agent might take in a software development workflow (e.g., reading a log file, editing a configuration file, deploying to a production server). For each, characterize its reversibility and state what approval checkpoint you would want before it executes.

**Synthesis**

6. *Design a supervision plan.* You want an agent to help you prepare a weekly status report: gathering data from three connected sources, drafting a summary, and emailing it to your team. Using the see/decide/do framework, write a brief supervision plan: what scope limits you would set, what you would review before the email sends, and what you would verify after.

7. *Reframe the misconception.* A manager says: "We should use the most capable agent available — more capability means better results." Write a two-paragraph response that uses the Bainbridge principle and the error-propagation logic from this chapter to correct this assumption without dismissing the value of capable agents.

**Challenge**

8. *Edge case analysis.* Consider a system that can search the web and insert citations into a document you are writing, but only when you explicitly ask it to insert a specific citation. Does this system belong closer to the assistant end or the agent end of the spectrum? Make the argument for both positions, then state which framing is more useful for supervision design and why.

---

## Sources Used

- Anthropic, "Claude Code overview," Claude Code Docs. https://code.claude.com/docs
- Anthropic, "Create and edit files with Claude," Claude Help Center. https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork
- Anthropic, "Let Claude use your computer in Cowork," Claude Help Center. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork
- Bainbridge, Lisanne. "Ironies of Automation." *Automatica*, 1983. https://doi.org/10.1016/0005-1098(83)90046-8
- Li, Xinzhe et al. "A Review of Prominent Paradigms for LLM-Based Agents: Tool Use, Planning, and Feedback Learning." arXiv, 2024. https://arxiv.org/abs/2406.05804
- Liang, Wenliang et al. "Understanding the Planning of LLM Agents: A Survey." arXiv, 2024. https://arxiv.org/abs/2402.02716
- Microsoft Research. "Guidelines for Human-AI Interaction." CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
- NIST. "Artificial Intelligence Risk Management Framework (AI RMF 1.0)." 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
- Parasuraman, R., Sheridan, T. B., and Wickens, C. D. "A Model for Types and Levels of Human Interaction with Automation." 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
- Tang, Xiangru et al. "A Survey on Large Language Model based Autonomous Agents." arXiv, 2023. https://arxiv.org/abs/2308.11432
