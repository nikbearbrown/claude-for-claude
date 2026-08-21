# Chapter 0 — The Claude Ecosystem

## Opening Scene

A policy analyst at a mid-size nonprofit needs to produce a grant progress report. She has twelve months of meeting notes in a folder, a budget spreadsheet, and three PDFs of program data. She opens Claude.ai, types "write me a grant report," and pastes three paragraphs of notes from her last meeting.

The result is clean, well-structured, and useless. The language sounds like a grant report. The numbers are invented. The outcomes described are plausible-sounding generalizations, not the program's actual data. She has not given Claude her files. Claude has given her fluency in exchange for her context.

She revises, re-prompts, copies in more text, and eventually produces something she then has to verify sentence by sentence anyway.

The problem was not the prompt. The problem was the surface. This was Cowork-shaped work — files, assembly, multi-step synthesis — being squeezed into a chat window.

This book begins where that session goes wrong.

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to identify the three Claude work surfaces — Claude AI, Claude Code, and Claude Cowork — and explain why they serve different kinds of work. You will also be able to name one task that belongs primarily in each surface, and articulate why "human-only" is a legitimate fourth routing option.

That routing judgment is the first discipline in this book. Every chapter that follows depends on it.

---

## The Ecosystem, Named

Claude is not one tool. It is a family of work surfaces built on the same underlying models but designed for different job types.

**Claude AI** (also called Claude chat, or simply Claude at claude.ai) is the conversational surface. It is where you talk to Claude: asking questions, drafting documents, exploring ideas, analyzing uploaded files, or building a research packet in a Project workspace. The human and Claude take turns. The human remains close to every output.

**Claude Code** is the engineering surface. It lives in your terminal or development environment and works inside a software repository. It can read files, run commands, propose code changes across multiple files, and wait for your review before committing anything. The human remains in the approval seat, but the work touches live code, tests, and sometimes production systems. [verify — current as of writing]

**Claude Cowork** is the file and workflow surface. It brings agentic capabilities to desktop and knowledge work: it can work with files on your machine, use connected applications, execute multi-step task plans, and produce assembled documents from multiple sources. It requires explicit approval steps and careful permission boundaries before it acts on real systems. (Anthropic, "Get started with Claude Cowork," Help Center [verify — current as of writing].)

A fourth position on the routing diagram is not a product at all. It is **human-only** work: sensitive decisions, ethical judgments, final accountability, and any task where the cost of delegation outweighs the benefit of assistance.

---

## Why This Distinction Matters

Parasuraman, Sheridan, and Wickens (2000) identified a fundamental problem in automation: users tend to delegate at the level of convenience rather than at the level that actually fits the task. When automation is nearby and capable-seeming, it gets used. The question "which tool belongs here?" gets crowded out by "can the tool do this?"

Claude is not exempt from this pattern. Chat is the most familiar surface, so many users route everything through it — including work that actually needs file access, codebase context, or multi-step planning. The result is what the policy analyst experienced: polished text that cannot be trusted because the right context was never provided.

Bainbridge's (1983) "Ironies of Automation" adds a sharper warning: automation can increase the demand on human monitoring precisely because it handles the routine parts well. The rare exceptions — the moment when the output is wrong, the file goes to the wrong place, the claim is fabricated — are harder to catch when the surface of the work looks reliable.

Both cautions point in the same direction: you need a mental model of each surface before you can supervise it well.

---

## The Three Surfaces at a Glance

| Surface | Best for | What you provide | Human gate |
|---|---|---|---|
| Claude AI / chat | Thinking, drafting, explanation, critique, document analysis | Clear question, context, uploaded documents, examples | Verify claims and citations externally; own the final output |
| Claude Code | Codebase-wide work, scripts, tests, diffs, technical documentation | Repository, issue description, acceptance criteria | Review plan, inspect diff, confirm test results, approve changes |
| Claude Cowork | File-heavy tasks, document assembly, multi-step workflows, folder operations | Folder, task packet, permission scope | Approve plan before action; review output and source manifest |
| Human-only | Sensitive, irreversible, ethical, final decisions | Human judgment and professional responsibility | Human owns the decision and its consequences |

This table is a routing guide, not a capability list. The real question is not "can Claude do this?" but "which surface fits the work, given what the work touches and what can go wrong?"

---

## One Task, Three Routes

Consider a research report on a new public health intervention. Here is how the same project routes differently depending on the task stage:

**Stage 1: Orienting the literature.** The researcher does not yet know which bodies of research to engage. This is a Claude AI conversation — exploratory, reversible, no file access needed. The output is a list of candidate literatures, authors, and search terms. The human verifies every lead.

**Stage 2: Writing analysis code.** The researcher has a dataset and needs a script to clean it and produce summary statistics. The dataset is not sensitive. This is Claude Code territory — the task involves a file, a script, tests, and a diff to inspect.

**Stage 3: Assembling the final report.** The researcher has meeting notes, program data files, a draft abstract, and a funder template. This is Cowork-shaped work: multi-file, document-heavy, output-oriented, with a verification pass on every assembled claim.

**Stage 4: Deciding whether to submit.** The intervention findings are preliminary and policy-consequential. No Claude surface owns this. The researcher makes the call.

The same project, four different routing decisions. Claude helps at three of the four stages — but on different surfaces, with different permissions, and with different verification requirements at each.

---

## The Operating Loop

This book teaches one repeating loop:

1. **Diagnose the work.** What is the output? Where is the context? What can go wrong? How will you verify success?
2. **Choose the surface.** Chat, Code, Cowork, or human-only.
3. **Specify clearly.** Give Claude the context and constraints it needs. A vague instruction produces vague work.
4. **Supervise actively.** Stay in the loop. Review plan before action, output before use.
5. **Verify.** Check claims, sources, calculations, and file changes against what you know.
6. **Own the result.** Claude produces; you decide. The artifact, the decision, and the consequence belong to you.

The Microsoft Research guidelines for human-AI interaction (Amershi et al., CHI 2019) ground this loop in human factors research: AI systems require explicit expectation management, support for correction, and maintained user control. The guidelines were written before agentic surfaces existed; they apply more urgently now that Claude can act on files and systems, not just produce text.

---

## The Human Gate

Each chapter in this book ends with a human gate — the specific point at which you must not delegate, and the verification discipline that keeps the work honest.

The human gate for the ecosystem chapter is the routing decision itself. No Claude surface will tell you that you are using the wrong surface. A chat session will produce polished text even when the task needed file access. A coding agent will stay in its lane only if you gave it the right lane. The routing judgment is yours before the first word is typed.

This is not a failure of the technology. It is the design of the human-AI relationship. The technology executes; the human diagnoses, chooses, and holds the frame.

---

## Common Mistakes

**Routing by habit.** Using chat for everything because it is familiar. The result is underpowered work: you get the output of a conversation partner when you needed a file agent.

**Routing by capability.** Choosing a surface because it can technically do the task, not because it fits. A powerful model with computer access can do many things. Whether it should depends on risk, reversibility, and your verification path.

**Conflating fluency with correctness.** The output sounds authoritative. The output may be wrong. These are independent properties. Fluency is not a validity signal.

**Ignoring the permission surface.** Claude Cowork and Claude Code can touch real systems. Granting broad permissions when you need narrow ones is the AI equivalent of giving a contractor keys to the whole building for a one-room repair job.

**Skipping the human-only row.** Some tasks are not routing problems. They are human responsibilities. The routing table has four rows because that fourth row is not a fallback — it is an essential category.

---

## Try This

**Exercise 1 — Surface Sort.** Take five tasks from your own work this week. For each one, decide: Claude AI, Claude Code, Claude Cowork, or human-only? Write one sentence explaining why the task fits that surface, and one sentence explaining what you would need to verify before using the output.

**Exercise 2 — Wrong Surface.** Describe a task you have given to Claude AI/chat that would have been better handled by a different surface or left to human judgment. What did the mismatch cost you — in effort, quality, or verification time?

---

## What Would Change My Mind

If empirical task-routing research showed that non-specialist knowledge workers achieved better outcomes by treating all Claude surfaces interchangeably, I would revisit the routing framework. The current framework rests on automation and HCI research developed before modern agentic AI, and while it transfers logically, direct evidence about Claude-specific routing decisions is sparse. The Parasuraman et al. (2000) and Bainbridge (1983) foundations are durable, but Claude-specific validation is worth watching.

---

## Still Puzzling

The boundaries between Claude AI, Claude Code, and Claude Cowork are shifting. As of this writing, chat can create and edit files in some contexts; Cowork can run code; Code can do non-code command-line work (Anthropic, "Create and edit files with Claude," Help Center [verify — current as of writing]). The principle — route by work type, context source, risk, and verification path — is stable. The exact feature map is not. This book flags product-specific details where they appear and recommends checking official Anthropic documentation before committing to a workflow.

---

## Bridge to Chapter 1

Knowing that three surfaces exist is not the same as knowing how to choose between them. Chapter 1 gives you a routing checklist — five questions that classify any task before you open a Claude window. The goal is a decision that takes less than a minute and saves you from the wrong surface before you get invested in the wrong output.

---

## Sources

Amershi, S., Weld, D., Vorvoreanu, M., Fourney, A., Nushi, B., Collisson, P., Suh, J., Iqbal, S., Bennett, P., Inkpen, K., Teevan, J., Kikin-Gil, R., and Horvitz, E. "Guidelines for Human-AI Interaction." CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/

Anthropic. "The AI for Problem Solvers." Claude product page. https://www.anthropic.com/product

Anthropic. "Claude Code." Product page. https://www.anthropic.com/product/claude-code

Anthropic. "Claude Code overview." Claude Code Docs. https://code.claude.com/docs

Anthropic. "Claude Cowork." Product page. https://www.anthropic.com/product/claude-cowork

Anthropic. "Get started with Claude Cowork." Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork

Anthropic. "Let Claude use your computer in Cowork." Claude Help Center, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork

Anthropic. "Create and edit files with Claude." Claude Help Center. https://support.anthropic.com/en/articles/12111783-create-and-edit-files-with-claude

Bainbridge, L. "Ironies of Automation." *Automatica*, 19(6), 775–779, 1983. https://doi.org/10.1016/0005-1098(83)90046-8

Parasuraman, R., Sheridan, T. B., and Wickens, C. D. "A Model for Types and Levels of Human Interaction with Automation." *IEEE Transactions on Systems, Man, and Cybernetics*, 30(3), 286–297, 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
