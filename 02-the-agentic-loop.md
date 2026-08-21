# Chapter 2 — The Agentic Loop
*What happens between "go" and "done" is the whole problem.*

Here is a thing that happened. A project manager asked an agent to compile a competitive analysis from five industry reports in a shared folder. The agent started immediately. It read two files, drafted a structure, filled in the remaining sections from its training data rather than the three files it had not yet opened, generated fluent summaries, and reported completion.

The output looked finished. The formatting was clean. Several competitor names were correct. Two sections described companies not present in any source document. One figure was invented. One competitor had been assigned the wrong product line entirely.

The manager forwarded it to her director.

Nothing about that sequence required the agent to be stupid or malicious. It just ran. And running — without a structure that forces the right things to happen in the right order — is how you get confident-sounding output that quietly misrepresents its sources. The problem wasn't accuracy. It was architecture. The agent had no loop worth the name.

I want to teach you to see that loop. Once you can see it, you can supervise it. Once you can supervise it, you can use agentic tools — Claude Code, Cowork, any orchestrated workflow — with the kind of control that makes the work actually reliable.

---

## Five Stages and Why the Order Matters

The agentic loop is a cycle with five stages. Simple tasks go around it once. Complex tasks go around it multiple times, each iteration using the results of the last to refine the next.

**Observe.** The agent takes stock of what is actually available: the task instructions, the files in scope, the output of a previous tool call, the error message from a step that failed, the partial result of one that succeeded. This is the foundation. An agent that observes badly — reads the wrong file, works from stale context, misses a constraint buried in the instructions — will plan badly regardless of what the planning step is capable of. Garbage in at the observe stage propagates forward. The whole downstream structure is built on it.

**Plan.** The agent forms a sequence of steps: which tools, in what order, with what inputs. This is where the human should see what is about to happen before it happens. The plan is a proposal. It is not a commitment, and it is not the work. It is the thing you read and approve or correct.

<!-- → [DIAGRAM: linear five-stage loop — Observe → Plan → Act → Check → Report — with a back-arrow from Report to Observe showing the cycle, and human intervention markers at Plan and Report] -->

**Act.** The agent executes one step: reads a file, writes one, runs a command, calls a tool, fetches a page. Each action changes something in the world — or tries to. The action surface is where real consequences appear. It is also where silent failures happen: the tool returns an error the agent does not flag, the file write partially succeeds, the command runs without the permissions it needed.

**Check.** The agent examines what happened: did the tool return expected output? Does the draft match the sources it claimed to draw from? Does the row count in the extracted table match the document? I will spend time on this stage separately, because it is the one most commonly mistaken for something stronger than it is.

**Report.** The agent describes what it did, what changed, what succeeded, and where it is uncertain. A good report is evidence. It includes the specific files touched, the tests run and their results, the sources cited, and the steps that were ambiguous or incomplete. A report that says "completed successfully" without any of that is not a green light. It is a gap in the audit trail.

---

## The Research Behind the Cycle

Two lines of work shaped how practitioners think about this.

Yao et al., in the ReAct framework (ICLR 2023), introduced interleaved reasoning and action. Instead of planning once and executing, the agent alternates: it reasons, acts, observes the result, reasons again about what just happened, acts again. This is more like how a skilled researcher actually works — adjusting the approach based on what each step reveals — than a pipeline running a predetermined sequence. The key result was that connecting reasoning to real-world feedback improved performance substantially over either pure reasoning (chain-of-thought, which can plan well but execute badly) or pure reaction (which acts but cannot recover from missteps).

Shinn et al., in Reflexion (NeurIPS 2023), extended this to the check stage. An agent that receives feedback from a failed attempt can turn that feedback into language and use it as context for the next attempt. An agent that failed a test and can articulate why it failed is in a better position than one that retries blindly. The limit is visible here too: the agent's self-critique is only as good as its understanding of the failure. An agent that misdiagnoses why a test failed will write a revised plan that fixes the wrong thing.

Both frameworks are embedded in how Claude Code and Cowork operate today (Anthropic, "How Claude Code works") [verify — current as of writing]. The practical consequence for users is this: the loop exists whether you designed it or not. The question is whether you have built in human intervention points, or whether the loop is running unattended.

---

## A Bug Repair, Traced

Here is what the loop looks like in a Claude Code workflow (Anthropic, "Claude Code overview").

The user describes a bug: a function that intermittently returns wrong values when the input list is empty. 

*Observe.* Claude Code reads the relevant source file, locates the function, reads the associated test files.

*Plan.* Claude Code proposes: inspect the function's branch logic, identify the path that handles empty input, draft a one-line fix, run the associated test suite.

*Human gate.* The user reads the plan. Is the function it identified actually the source of the bug? Are the tests it plans to run the right ones? Does the scope include any files that should not be touched? The user approves, redirects, or corrects before anything has been changed.

*Act.* Claude Code edits the function. One line changes.

*Check.* Claude Code runs the test suite. Three tests pass. One test that was previously flaky now reliably passes. No new failures.

*Report.* Claude Code presents a diff — the exact change, the line numbers, before and after — along with test output. It notes that the intermittent failure appears resolved based on the test run, but that production behavior should be verified with a broader test case.

*Human verification.* The user reads the diff. The change is what was proposed. The test output matches. The user decides whether to accept the change, add another test, or investigate further before committing.

The loop ran once for a simple bug. For a more complex issue, it runs multiple times — each cycle using the result of the last to refine the approach. The structure is the same. The stages are the same. The human gates are the same.

<!-- → [DIAGRAM: the bug repair loop traced as a concrete instance of Observe → Plan → Human Gate → Act → Check → Report, with specific content at each stage from the example above] -->

---

## A Document Assembly, Traced

The same five stages in a Cowork knowledge-work workflow (Anthropic, "Get started with Claude Cowork").

The user asks Cowork to assemble a section-by-section summary from eight source documents in a designated folder.

*Observe.* Cowork reads the folder listing, opens each document, notes which files it can access and which are empty or unreadable.

*Plan.* Cowork proposes a structure: an introduction pulled from the first two documents, three analytical sections corresponding to themes in the middle group, a conclusions section from the final report. It lists which source documents map to which sections.

*Human gate.* The user reviews the proposed structure. Are the sections the right ones? Does the source mapping make sense? The user may correct misattributions or redirect the structure before Cowork begins drafting.

*Act.* Cowork drafts each section, drawing specific passages from the identified sources. It creates an output document in the user's designated folder.

*Check.* Cowork reviews the draft against its source list: are citations present? Are page or paragraph references included? Does the section on topic X actually draw from the document identified as the source for topic X?

*Report.* Cowork presents the draft with a source list — which document contributed to which section — and flags two sections where source documents had conflicting numbers, noting that a human should review which figure is authoritative.

*Human verification.* The user opens two or three source documents and spot-checks specific figures against the draft. The user reads the flagged conflict sections and makes a judgment call. The user checks whether any sensitive information from a confidential document was included in a section that will be distributed publicly.

This is supervised delegation working as intended. The agent does the execution. The human does the judgment. Neither is adequate without the other.

---

## Where Errors Actually Enter

The loop reveals a failure architecture. Errors do not appear randomly — they enter at specific stages, and they compound as the loop continues.

*At observe:* The agent reads the wrong file. It works from an earlier version of a document. It cannot open one of the five required source files and proceeds without flagging the gap. It misreads an instruction and thinks the task scope includes files it should not touch. These errors propagate into the plan before anything has been done. They may never be caught if the report is read at the summary level rather than the evidence level.

*At plan:* The agent proposes steps in the wrong order, creating a dependency failure later. It selects a tool that is not suited to the data format. It breaks a complex task into too few steps and misses a case. These errors are catchable at the human gate — which is why plan review exists.

*At act:* The tool returns an error the agent does not notice or report. The file write partially succeeds, leaving a corrupted file. The command runs with insufficient permissions and silently fails. The browser request times out and the agent substitutes a guess. These errors may or may not surface in the check stage.

*At check:* The agent self-checks against its own output rather than against the source. It finds no inconsistencies because it is the same system that generated both. It marks a step complete when it succeeded conditionally. It does not check the cases the user most needs checked.

*At report:* The agent presents a confident summary that omits the two steps that failed. It reports test passage without noting that one test was skipped. It says "completed" without listing the files it touched or the sources it used. The user reads the summary and assumes verification has been done.

<!-- → [TABLE: loop stage in column 1, failure mode in column 2, human intervention that catches it in column 3 — five rows, one per stage] -->

The planning survey literature organizes these failures under task decomposition, plan selection, tool use, self-reflection, and memory — and treats each as a research problem (Liang et al., 2024; Li et al., 2024). For practitioners the takeaway is more direct: read the plan before action begins, read the report for evidence not summary, and do not assume the check stage was adequate.

---

## The Distinction That Matters Most

Self-checking is not independent verification. I want to be precise about this because it is the most commonly misunderstood point in the whole architecture.

When an agent reviews its own draft, runs internal consistency tests, reflects on whether the plan was followed — that is self-checking. It is genuinely useful. It catches some errors.

It is not independent verification. An agent checking its own output is working from the same context that produced the output: the same interpretation of the sources, the same understanding of the task, the same implicit assumptions. Reflexion showed that self-critique improves performance on some benchmarks. It also showed the limit: an agent that misunderstood the task will produce self-critique that misdiagnoses the problem.

Independent verification means checking output against something external to the agent's reasoning: the actual source documents, the test results from an independent test suite, the row count in the original data, the expert judgment of a human who knows the domain.

The Microsoft Research Guidelines for Human-AI Interaction (CHI 2019) frame this as a design principle: systems should support user verification, not substitute for it. A report that includes citations, diffs, test outputs, and uncertainty flags supports user verification. A report that says "all done" does not.

---

## The Human Gate: Where to Actually Stand

The loop has natural intervention points. The human's job is to be present at them — not to watch passively while the agent runs to completion.

*Before the plan is approved* is the highest-leverage point. The human reads the proposed steps, checks the scope, confirms the tool selection, decides whether to proceed. A bad plan stopped here costs nothing. A bad plan discovered at the report stage costs the entire run — plus whatever state the agent has already changed in the world.

*During multi-step execution*, for complex tasks that cycle multiple times, the human should be able to interrupt between cycles. If an intermediate result is wrong — the wrong theme was identified, the wrong file was prioritized — correcting context before the next cycle begins is far cheaper than unwinding after it ends.

*At the report*, the human reads not the summary but the evidence: the diff, the source list, the test output, the flagged uncertainties. The report is not the end of the process. It is the information the human needs to judge whether the work is complete.

Parasuraman, Sheridan, and Wickens (2000) describe this as the intervention architecture of automation: humans can intervene at plan selection, execution approval, monitoring, and result evaluation. Each point has different leverage and different cost. The plan stage is cheap to intervene in and high-leverage. The report stage is expensive to undo if actions have already been taken.

NIST's AI Risk Management Framework (2023) treats this at the organizational level: map the risks, measure the impacts, manage with controls, document the process. The loop framework translates that into operational practice: map the stages, identify the human gates, manage through them, document through the report.

---

## What an Interrupted Loop Looks Like

Not every loop should run to completion.

The user asks Claude Code to refactor a module to improve performance. Claude Code observes the module, reads its tests, and proposes a plan that includes deleting six functions it has identified as unused.

The user reads the plan. Three of those six functions are used in a configuration file Claude Code did not read — they were outside the scope it was given. Deleting them would break a production workflow.

The user does not approve the plan. The user corrects the context: tells Claude Code about the configuration file, asks it to re-observe with that file included, requests a revised plan.

Claude Code re-observes. The revised plan reduces the six deletions to two. The user approves. The refactor proceeds safely.

The interruption cost ten minutes. The uninterrupted version would have broken production. The loop is designed to be stopped. Stopping it is not a failure of the tool. It is the tool working as intended.

---

## What Would Change My Mind

This chapter argues that the observe-plan-act-check-report cycle is the right mental model for supervising agentic work. That framing would need revision in a few circumstances.

If better transparency mechanisms emerged — not just reasoning traces but genuinely inspectable action logs that allowed users to verify loop execution in real time — the report would matter less. If the loop became fully auditable continuously, the bottleneck shifts from the report to the audit tooling.

If self-checking improved substantially — specifically, if agents demonstrated reliable detection of their own context gaps and planning errors at rates exceeding current human review — the balance between self-check and external verification would shift. Current evidence does not support this conclusion, but the research is active.

If agent architectures changed in ways that broke the sequential cycle — massively parallel sub-agents working independently, for instance — the linear five-stage model might need to become a graph rather than a loop. The principles would survive; the specific structure might not.

Until then, the five-stage cycle is the right frame for practitioners.

---

## Still Puzzling

How much of the loop should be visible to the user? Some users benefit from seeing every tool call; others are overwhelmed by it. There is probably a reliable way to calibrate the right transparency level for different users and tasks, but I do not know yet what it is.

Reflexion-style self-correction is useful but fallible. Are there types of tasks or error patterns where agent self-correction reliably works, versus types where it reliably fails, and can practitioners use that distinction in practice? The answer matters for where you place your human gates.

As multi-agent systems become more common — where one agent orchestrates others — how does the loop model need to extend? This chapter describes single-agent loops. Orchestration may require a different supervision architecture entirely. That is the open question the field has not answered cleanly.

---

## LLM Exercises

**1.** Paste a task description you have used with Claude Code or Cowork into Claude. Ask it to produce an explicit observe → plan → act → check → report breakdown before beginning any work. Compare the proposed plan to what you expected. Identify any scope assumptions you would correct before approving.

**2.** Take a completed AI-assisted task and reconstruct the loop in retrospect. At which stages did you actually see evidence of what the agent was doing? At which stages were you working from its summary? What would you need to have seen at those stages to verify the work independently?

**3.** Ask Claude to perform a multi-step task and explicitly instruct it to flag, at the check stage, every result that it could not verify against an external source — labeling those as "unverified." Compare the flagged versus unflagged output. What does the distribution tell you about where self-checking is reliable for this task?

---

## Sources Used

- Anthropic, "Claude Code overview," Claude Code Docs. https://code.claude.com/docs
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork
- Anthropic, "How Claude Code works," Claude Code Docs. https://code.claude.com/docs/en/how-claude-code-works
- Li, Xinzhe et al. "A Review of Prominent Paradigms for LLM-Based Agents: Tool Use, Planning, and Feedback Learning." arXiv, 2024. https://arxiv.org/abs/2406.05804
- Liang, Wenliang et al. "Understanding the Planning of LLM Agents: A Survey." arXiv, 2024. https://arxiv.org/abs/2402.02716
- Microsoft Research. "Guidelines for Human-AI Interaction." CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
- NIST. "Artificial Intelligence Risk Management Framework (AI RMF 1.0)." 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
- Parasuraman, R., Sheridan, T. B., and Wickens, C. D. "A Model for Types and Levels of Human Interaction with Automation." 2000. https://pubmed.ncbi.nlm.nih.gov/11760769/
- Shinn, Noah et al. "Reflexion: Language Agents with Verbal Reinforcement Learning." NeurIPS 2023. https://papers.nips.cc/paper_files/paper/2023/hash/1b44b878bb782e6954cd888628510e90-Abstract-Conference.html
- Yao, Shunyu et al. "ReAct: Synergizing Reasoning and Acting in Language Models." ICLR 2023. https://arxiv.org/abs/2210.03629
