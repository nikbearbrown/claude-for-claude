# Chapter 0 — The Agent Arrives in Ordinary Work

You almost sent it.

Forty-three PDFs, one afternoon, a deadline bearing down. You handed the task to an agent — describe what you need, let it run, come back in twenty minutes. There was a table. It looked right. The formatting was clean. The numbers appeared reasonable. You felt the particular relief of watching a machine do in twenty minutes what would have taken you three hours.

Then you noticed a figure that didn't add up. You pulled the source document. The agent had read the correct file, found the correct column, and misread a row — a subtotal taken for a grand total. Every downstream figure in the table was wrong.

The output was fluent. The work was wrong.

This is not primarily a story about AI failure. The agent did what agents do: it executed a delegated task within the access it was given and reported completion. No part of that sequence was broken. What was broken was the supervision design. Nobody had built a step to catch a misread row before the table went out.

This book is about building that step. Every chapter adds to one answer: what does a user need to do differently when the AI can act, not just answer?

---

## The Change That Matters

For most of AI's public life, the interaction was conversational. You asked; it answered. The system produced text, and you decided what to do with that text. If the answer was wrong, the cost was whatever you lost acting on it — but the AI itself had not moved anything. Its reach ended at your screen.

That has changed.

Modern AI systems can now be given tools. A tool, in this context, is not a metaphor — it is a capability that lets the system act on something outside the conversation: read a file, write a file, run a command, search the web, populate a spreadsheet, open a browser, operate an application. When a system has tools, it is no longer producing text for you to evaluate. It is operating in an environment. It can change the state of things.

When something can change the state of things, your relationship to it changes fundamentally. You are no longer reading a response. You are supervising a process that is reaching into your files, your connected services, and potentially your colleagues' work.

The research community calls this an **agent**: a system that can observe a context, form a plan, use tools to act, check the results, and cycle back (Tang et al., 2023; Li et al., 2024). The observation-action-feedback loop is what separates an agent from an answering machine. It is also what makes the stakes different.

Claude Code can inspect a codebase, run tests, edit files, and report what changed. Claude Cowork can move across documents, spreadsheets, and browser sessions — gathering, transforming, and producing artifacts in a file system you own [verify — current as of writing]. Both systems can do things that persist after the conversation ends. That persistence is the operative difference. A wrong answer in a chat window stays in the chat window. A wrong number written into a spreadsheet, a file deleted, a form submitted — those changes are in the world.

---

## The Analogy Is Delegation, Not Magic

The word *agent* can mislead. It sounds like the system is self-directing, operating on its own initiative, pursuing goals you set and then left alone. That is not what a useful agent is.

A useful agent is a system that executes delegated work within defined boundaries, with human supervision at key points.

The analogy that holds up is delegation to a capable colleague, not deployment of an autonomous system. When you delegate meaningful work, you do not hand over the task and disappear. You describe the scope. You say what they can and cannot touch. You agree on when they should check with you if something unexpected comes up. You review the result before it leaves the office. The colleague's capability does not reduce your responsibility — it changes the form your responsibility takes. The same transfer applies exactly to an agent.

Lisanne Bainbridge described this dynamic in 1983, writing about automated industrial systems, before anyone was thinking about AI assistants. Her observation was precise: the more capable the automated system, the more demanding the supervisory role, not less (Bainbridge, 1983). More automation creates new monitoring demands, new failure modes, new intervention responsibilities. Parasuraman, Sheridan, and Wickens formalized it further: autonomy is a design variable, and the human role changes at every level but does not disappear (Parasuraman et al., 2000). What they documented in process control and aviation applies directly here. The AI agent does not reduce your role. It relocates it — upstream into design, and at checkpoints into verification.

---

## Three Concepts That Run Through Everything

This book builds on three ideas. They appear in every chapter. They apply to every agentic workflow. They are the answer to the question: what does a user actually need to do differently?

**Scope** is the boundary you draw before the agent starts. What files can it read? What can it write? What services can it touch? What is outside the task entirely? Scope is not only about safety — it is about quality. An agent given too broad a scope will attempt things it is not equipped to handle, and the resulting errors will be harder to trace. An agent given a clear scope can be supervised more precisely because you know what it was supposed to do and can check whether it did that.

**Approval** is the checkpoint where you decide whether the planned action should proceed. This happens before the first action, between major steps, or both, depending on the stakes. It is not a formality. You read the plan, assess whether the proposed steps match the task as you understand it, and decide: proceed, revise, or stop. This is not final polish. It is part of the control system.

**Verification** is the discipline of checking whether the output is correct, complete, and trustworthy — not just whether it looks finished. A verified output has been checked against its sources, tested for accuracy across at least a sample, and compared against the expected result. Verification is not the same as reading something over. It is the discipline of asking: what would have to be true for this to be wrong, and did I check that?

Scope. Approval. Verification. Everything else in this book is elaboration on one of these three.

---

## Where Agents Work in Ordinary Practice

Agentic AI is not arriving primarily in specialized technical work. It is arriving in the ordinary work of professional life: report assembly, data extraction, file organization, research compilation, document drafting, code maintenance. Four examples appear throughout this book:

**Code repair.** The agent observes failing tests, edits the relevant files, reruns the tests, and reports the diff. You defined what the issue is, approved the proposed changes, and now review whether the fix works without breaking something else.

**Report assembly.** The agent reads source documents in a designated folder, extracts relevant facts, and builds a draft memo. You confirmed which sources are authoritative, checked for omissions, and are now verifying figures before the memo goes anywhere.

**Spreadsheet extraction.** The agent reads a set of PDFs and populates a data table. You checked that the row count matches the source documents, sampled a subset of cells against the originals, and confirmed the extraction schema was applied correctly.

**Browser research.** The agent opens pages from a defined list of trusted sources to gather information. You specified which domains were in scope, confirmed that no external-facing actions were taken, and checked the source list in the output.

Each workflow involves the agent doing real work that saves real time. Each requires the human to design scope before the work starts and to verify the output before it is used. The agent's value is in execution. The human's contribution is judgment: defining the boundary, reading the plan, and verifying the result.

There is also a boundary on the other side — work that should not be delegated to an agent. The agent should not send legal advice, delete production data, submit a grant application, or touch protected health information without explicit governed approval from someone accountable for those decisions. This book is about supervised delegation. Supervision includes the decision not to delegate.

---

## The Plan Is Not the Work

One misconception deserves to be named before the first chapter. When an agent presents a plan — a sequence of steps, a proposed tool sequence, a structured outline of what it intends to do — that plan can look like evidence that the agent understands the task. It is not.

The planning research literature is clear on this point: planning in LLM-based agents is a useful output to inspect and can catch errors before they occur, but it is not a guarantee of execution quality (Liang et al., 2024). An agent can produce a plausible plan and still read the wrong file, misapply a formula, skip a step under an unusual condition, or confidently report completion when it failed partway through. The plan is a proposal. It is not a proof.

This means: do not approve a plan because it sounds reasonable. Read the plan against the actual scope. Check whether the steps match the task as you defined it. Ask what would happen if one of the middle steps returned something unexpected. The plan is useful as a checkpoint — it is the right place to catch a misalignment before action — but it does not substitute for verification after the work is done.

Lucy Suchman's foundational work on situated action captures this precisely: plans are schematic approximations of action, not fully determined scripts (cited in Tang et al., 2023). The situated details of real files, real error messages, and real edge cases will not have been in the plan. That is not a flaw in the plan — it is the nature of plans. It is why verification after action is irreplaceable.

---

## What the Rest of This Book Does

Chapter 1 draws the taxonomy: what makes a system an agent rather than a chatbot or a workflow tool, and how that distinction changes what you need to do as a user.

Chapter 2 works through scope in detail — how to define it, how to communicate it to an agent, and what happens when it is defined poorly.

Chapter 3 covers approval gates: when to use them, how many to use, and how to read a plan critically rather than just acknowledging it.

Chapter 4 addresses verification: not as a final step, but as a discipline that runs through the work, with specific techniques for specific output types.

Chapter 5 examines the surfaces where agents operate — file systems, code, spreadsheets, browsers, connected services — and what each surface requires in terms of the control triad.

Chapter 6 closes with what does not change: the human decisions that are not delegatable, and why.

Every chapter returns to the same three concepts. Scope before action. Approval at consequential checkpoints. Verification before output is used. These are not safety reminders appended to a workflow. They are the workflow.

---

The table with the wrong grand total almost went out because there was no step designed to catch it. This book is the design of that step.

---

<!-- → [TABLE: Control Triad Summary — three columns: Concept | When It Happens | What It Asks — rows for Scope, Approval, Verification] -->

---

*LLM Exercise: You are given a partial agentic workflow description — a task, a set of tools, and an output format. Write a one-paragraph scope statement, name the approval gates you would place, and describe a specific verification check you would run on the output. The task is: "Summarize key financial figures from twelve quarterly reports into a single comparison table."*

---

## Sources

- Bainbridge, Lisanne. "Ironies of Automation." *Automatica*, 1983. https://doi.org/10.1016/0005-1098(83)90046-8
- Li, Xinzhe et al. "A Review of Prominent Paradigms for LLM-Based Agents: Tool Use, Planning, and Feedback Learning." arXiv, 2024. https://arxiv.org/abs/2406.05804
- Liang, Wenliang et al. "Understanding the Planning of LLM Agents: A Survey." arXiv, 2024. https://arxiv.org/abs/2402.02716
- Parasuraman, R., Sheridan, T. B., and Wickens, C. D. "A Model for Types and Levels of Human Interaction with Automation." 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
- Tang, Xiangru et al. "A Survey on Large Language Model based Autonomous Agents." arXiv, 2023. https://arxiv.org/abs/2308.11432

---

*Tags: #claude #agentic #ai #supervision #scope #verification #delegation #Medhavy*
