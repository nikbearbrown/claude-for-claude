# Chapter 9 — Subagents: Keeping the Build Clean


## TL;DR

- This chapter gives a working overview of Subagents: Keeping the Build Clean, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What a subagent is, Subagents vs. Skills vs. /clear, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> A subagent is a Claude session that runs in its own context window and reports back a summary. When your grading tool's research starts filling the session with file reads, delegate the research. Keep the main build clean.

---

## Learning outcomes

1. **(Understand)** Explain what a subagent is and how it differs from a Skill and from the main session.
2. **(Apply)** Delegate a research task to a subagent and verify the main session's context did not grow during the research.
3. **(Apply)** Define a custom subagent for a specialized classroom task.

---

## Opening

The teacher's grading-tool session had been running for an hour. The narrowing principle from Chapter 8 was in place — Claude was detecting patterns; the teacher would write feedback from the flags. The Skill from Chapter 6 was active. The Hooks from Chapter 7 were enforcing the never-rules.

The teacher needed one more thing before drafting per-student feedback: a quick investigation of how the school's existing tools handled late submissions. The school had a policy. The policy lived in a Google Doc, was implemented partially in the LMS, and had three undocumented exceptions the department had agreed on in November. To write feedback that referenced the policy correctly, the teacher needed Claude to read the doc, parse the LMS export, and summarize the actual operative rules.

So they asked Claude — in the same session — to investigate late-submission handling. Claude read the doc. Read the LMS export. Read the November meeting notes the teacher pointed it at. Read three policy revisions. Read the section of the syllabus that referenced the policy. Read a related school-board document. Returned a summary.

The summary was useful. The session was now full.

`/context` showed the conversation history at 78% of the window. The build itself had taken 30% of the window. The research had taken 48%. The remaining 22% was barely enough to draft feedback for one student before the session would start degrading. The teacher had two options: `/clear` (lose the build's context and rebuild it) or continue and watch the quality drop.

There was a third option. The teacher had not used it.

The third option is a **subagent**: a Claude session that runs in *its own* isolated context window, reads what it needs to read, and returns only a summary to the main session. The main session never sees the policy doc, the LMS export, the meeting notes, or the policy revisions. It sees three sentences of summary. The main session's context stays clean. The build proceeds.

This chapter closes Act Two. Subagents are the discipline of keeping the main build clean by delegating context-heavy work. By the end you will have a pattern-analysis subagent for your grading tool and a working understanding of the Writer/Reviewer pattern that catches what same-context review misses. Gate 2 closes after the chapter when the full Act Two framework is in place.

![Main session vs](images/10-subagents-fig-01.png)
*Figure 10.1 — Main session vs*

---

## What a subagent is

A subagent is a Claude session spawned by the main session. It has:

- **Its own context window.** Independent of the main session. The subagent can read 30 files; the main session sees only the subagent's final summary.
- **Its own system prompt.** Specialized for the subagent's role (a researcher, a reviewer, a pattern analyzer). The system prompt lives in the subagent's definition file.
- **Its own allowed tools.** The subagent's permissions can be tighter than the main session's. A research subagent might have Read, Grep, Glob — and no Write or Edit. The narrower permissions are a safety layer.
- **A short summary as its output.** The main session prompts the subagent; the subagent runs; the subagent returns a structured summary. The main session integrates the summary; it does not see the subagent's intermediate work.

Configuration lives in `.claude/agents/<agent-name>.md` (project-scoped) or `~/.claude/agents/` (user-scoped, available across projects).

Minimal example, for the `pattern-analyzer` subagent the grading-tool needs:

```markdown
---
name: pattern-analyzer
description: Analyze student submissions for common misconceptions; return a structured pattern report.
tools: Read, Grep, Glob
---

You analyze student submissions for patterns across a cohort.

Inputs (provided when invoked):
- The directory containing submissions (one subdirectory per student).
- The assignment's required sections (from rubric.md).

Your task:
1. Read all submissions in the provided directory.
2. Identify the top 5 common misconceptions, with example excerpts (anonymized — student name redacted).
3. Identify outliers: submissions that do not match the cohort patterns.
4. Compute coverage: for each of the top 5 misconceptions, how many submissions exhibit it.
5. Return a structured report:
   - Section 1: top 5 misconceptions (one paragraph each).
   - Section 2: outliers (list with one-sentence characterization each).
   - Section 3: coverage table.

Do NOT generate per-student feedback. Do NOT compute grades. Do NOT modify any files.
Return the report only.
```

To invoke from the main session: *"Use the pattern-analyzer subagent on `submissions/recursion-assignment/`. Return the pattern report."* Claude spawns the subagent; the subagent reads all 25 submissions in its own context; the subagent returns the three-section report. The main session's context grows by the size of the report (a few hundred words), not by the size of the submissions (potentially many thousands).

---

## Subagents vs. Skills vs. /clear

Three context-management tools, three roles.

**Subagent.** Delegates work to an isolated context. Use when the task has a clear input/output shape and the per-task work would otherwise bloat the main session. The subagent runs *separately*; its work does not enter the main context.

**Skill.** A workflow Claude follows in the *same* context. Use when the work is a defined procedure but it does not need isolation. The skill's content enters the main context when invoked; the workflow executes there.

**`/clear`.** Wipes the main context. Use when the current session's work is complete and you are starting something unrelated. Cheap; resets the slate.

For the chapter-opening dilemma — late-submission research on top of the grading build — the right tool is the subagent. The research has a clear input (the docs to read) and a clear output (a summary). It does not need to live in the main context after the summary is delivered. The pattern-analyzer for the grading workflow is the same shape — read many submissions, return a pattern report.

The heuristic: *if the task reads more than the main session needs to see, it is a subagent task.*

---

## The four most useful subagent patterns for teachers

### Pattern 1: research subagent

Investigate something. Return findings only.

Example: *"Use a research subagent to investigate how to handle late submissions across the school's late-submission policy doc, the LMS export, and the November meeting notes. Return a three-paragraph summary of the operative rules including the documented exceptions."*

Claude spawns the subagent. The subagent reads everything. The subagent returns three paragraphs. The main session integrates; the build continues.

### Pattern 2: pattern-analysis subagent

Analyze a corpus. Return structured patterns.

The chapter's worked example. For the grading tool: read all submissions for an assignment, return the misconception pattern report. Per-submission reads stay in the subagent; the report comes back.

### Pattern 3: review subagent (the Writer/Reviewer pattern)

A separate Claude reads completed work from a clean context and reviews it without the implementer's biases.

For the grading-tool code itself: the main session writes the tool. A `reviewer` subagent reads the final code (the subagent has never seen the writing process — no history, no decisions, no rationale). The reviewer reports issues. The writer (main session) responds.

Why this matters: the writer-session's context contains the writer's reasoning. Same-context review tends to validate the reasoning. A clean-context reviewer applies fresh judgment — finds bugs, finds unclear naming, finds tests that should exist. Even though the underlying model is the same, the context difference matters.

The Writer/Reviewer pattern is the chapter's recommended discipline for high-stakes code (the grading tool, the simulation launcher in Act Three).

### Pattern 4: specialized worker subagent

A subagent with narrow allowed tools, used for a specific role.

Example: a `linter-runner` subagent with only Bash access to the project's lint commands. The main session asks the subagent to run linters and report errors. The subagent's narrow permissions are a safety boundary — the subagent cannot accidentally modify files.

This pattern is power-user-adjacent; most teachers will not need it in their first semester. Mentioned for completeness.

---

## A worked example: pattern analysis on the grading workflow

Walk through the full pattern from the grading-tool's perspective.

**Step 1: write the subagent definition.** The `pattern-analyzer.md` above goes in `.claude/agents/`. Commit to git.

**Step 2: invoke from the main session.**

*"Use the pattern-analyzer subagent on `submissions/recursion-assignment/`. The required sections from `rubric.md` are: a working factorial function, a working fibonacci function, a main block, and at least one docstring."*

**Step 3: the subagent runs.** Independently. The teacher sees a brief notification that the subagent is working; the subagent's intermediate reasoning is invisible to the main session.

**Step 4: the subagent returns the report.** Three sections; a few hundred words.

```
PATTERN ANALYSIS — recursion-assignment (25 submissions)

Top 5 misconceptions:
1. Base case omitted in factorial (8 submissions). Students wrote
   `def factorial(n): return n * factorial(n-1)` with no n==0 case.
2. Off-by-one in fibonacci's base cases (5 submissions). Students
   returned fib(1) = 0 or fib(2) = 2; multiple variants.
3. Use of global state in helper functions (4 submissions). Students
   defined module-level variables modified by recursive calls.
4. Missing docstrings on helper functions (12 submissions). The
   assignment required docstrings on all functions; helpers were skipped.
5. Iterative implementation labeled "recursive" (2 submissions).
   Students wrote loops with self-referential variable names.

Outliers:
- Submission s-12: implemented memoization unprompted. Strong work.
- Submission s-19: used tail recursion correctly with explicit accumulator.
- Submission s-04: implementation correct; docstrings exceptionally clear.

Coverage:
| Misconception | Count | %
|---|---|---|
| Base case omitted | 8 | 32%
| Fibonacci off-by-one | 5 | 20%
| Global state | 4 | 16%
| Missing docstrings | 12 | 48%
| Iterative-labeled-recursive | 2 | 8%
```

**Step 5: the teacher integrates.** The main session has the report. The teacher uses the report to write per-student feedback flags. The narrowing principle is preserved (Claude detected patterns; the teacher writes feedback). The context cost was small — the report, not the per-submission reads.

---

## Teacher build: a subagent for your grading workflow

Apply the chapter's discipline to your grading tool.

1. **Write the `pattern-analyzer` subagent.** Use the worked example above; adapt for your subject and rubric. Commit to git.

2. **Invoke for your current assignment's submissions.** The first run is the test. Verify the subagent stays inside its permissions (Read, Grep, Glob — no Writes).

3. **Compare context usage with and without the subagent.** Run a short session inline (read three submissions in the main session); compare `/context` before and after. Then run via subagent; compare `/context` before and after. Document the difference.

4. **Optionally: write a `reviewer` subagent for the Writer/Reviewer pattern.** Used to review your grading-tool code (or other build code) from clean context. Configure narrow permissions (Read, Grep — no Write).

5. **Add to CLAUDE.md.** A short note about which subagents the project uses, when to invoke each, what they return.

Total time: 45–60 minutes for the first subagent. The pattern-analyzer becomes part of every grading cycle from this point forward.

---

## Gate 2: Act Two acceptance

Act Two closes here. The acceptance criteria:

1. **Working grading tool** built through the framework: Chapter 4 handoff conditions, Chapter 5 capacity prep, Chapter 6 Skill, Chapter 7 Hooks, Chapter 8 narrowing principle, Chapter 9 subagent.

2. **The five supervisory capacities** can be labeled in your build transcripts. You can name when PA, PF, TO, IJ, EI fired.

3. **The dangerous middle has been experienced and instrumented against.** The narrowing principle is in your workflow; equity handoff conditions are in CLAUDE.md; the `[TEACHER-REVIEWED]` hook is in place.

4. **CLAUDE.md spans two build tiers.** Class-website (Act One) plus grading tool (Act Two). The Lessons Learned section has at least three entries from the second build.

5. **One classroom activity per Act Two concept** designed: capacities (Chapter 5), Skills (Chapter 6), Hooks (Chapter 7), dangerous middle (Chapter 8), subagents (Chapter 9). Five activities. Each maps to the students book.

If all five are true: Gate 2 is met. Act Three begins.

If any are not: stop. Act Three (the simulation build with the three-file system) compounds the discipline of Acts One and Two. Going forward without Gate 2 closed produces the same failure mode the chapters teach against — the build proceeds before the discipline is in place.

---

## Classroom activity: students design and use a subagent

For your students' classroom:

Each student writes a subagent definition for a specific task in a project they are working on:

- A research subagent for a topic they need to investigate.
- A pattern-analysis subagent for a dataset.
- A linter or test-runner subagent with narrow permissions.

The constraints:

- Frontmatter with name, description, allowed tools.
- System prompt that names the input, the workflow, the output format.
- Explicit *do not* list (do not modify files; do not generate text outside the structured report; etc.).
- Used once on a real instance, with `/context` checks before and after to verify isolation.

Pair students. Each peer evaluates the other's subagent for: scope (clear input/output?), safety (appropriate permissions?), and the *do not* list (are the prohibitions specific enough?).

The pedagogical move: students learn that subagents are *separation-by-design* — the tool's value is what it does not pull into the main context. The peer review surfaces the difference between a subagent that protects the main session and one that just renames an inline operation.

This activity maps to the student book's chapter on Subagents. Your design here is the worked example.

---

## Common misconceptions

**"Subagent = another Claude tab."** No. The subagent is spawned from the main session; it runs in its own context; only the summary returns. There is no second window to manage.

**"Subagents are for complex builds only."** Even a single grading-tool session benefits from delegating the pattern-analysis. The criterion is *would this work otherwise bloat the main context?*

**"More subagents = better."** No. Each adds a configuration file and a maintenance load. Use subagents for tasks that meet the criteria; do not invent them for tasks that are fine inline.

**"The Writer/Reviewer pattern is overkill."** For routine code, yes. For high-stakes code (the grading-tool's never-rules, the simulation launcher's deployment), the clean-context review catches issues the same-context review validates. Use it where the stakes warrant.

**"Auto memory across subagents is magic."** It is useful and has stale-context risk. Used for recurring research, the subagent accumulates findings across sessions. Used badly, it accumulates outdated assumptions. The chapter's recommendation: enable when you are tracking the subagent's behavior; default off.

---

## Exercises

1. **(Apply)** Delegate a research task to a subagent in your grading project. Compare main-session `/context` before and after. Verify the context did not grow during the research.

2. **(Apply)** Define a custom subagent for a specialized task in your work: peer review, pattern analysis, lint running. Specify allowed tools, system prompt, output format. Commit.

3. **(Analyze)** Compare a session where you researched inline versus a subagent session for the same research. Document the difference in context growth, time elapsed, and the main session's responsiveness in the work that followed.

---

## What would change my mind

The chapter's strong claim is that **subagents materially improve build outcomes** when work would otherwise bloat the main context. If a controlled comparison found no measurable difference in build quality or speed between subagent-delegated and inline research, the chapter's discipline becomes optional. The chapter would still recommend the Writer/Reviewer pattern; the context-management case softens.

I expect the difference to be substantial because the context-degradation pattern (session quality drops as the window fills) is real and observable, and the subagent is the only Claude Code mechanism that lets the main session do isolated reading without paying the context cost.

---

## Still puzzling

- **Auto memory.** Subagents can accumulate auto memory across runs. Useful for recurring research; risk of stale assumptions. The chapter's working answer: enable when the subagent's behavior is being actively tuned; default off.

- **Agent teams.** The 2026 emerging pattern: multiple subagents coordinated by a main agent, each specialized for a role. Powerful for complex builds; out of scope for student-scale work. Mentioned for completeness; not recommended for first-semester adoption.

- **Subagent security.** Each subagent's allowed tools are a permissions surface. The teacher should review tool grants for each subagent before committing the definition file. Narrow permissions are the default; broaden only with reason.

---

## Links

- [Claude Code Subagents documentation](https://code.claude.com/docs) — definition format, frontmatter, allowed tools.
- [The Writer/Reviewer pattern](https://code.claude.com/docs) — the 2026 recommended pattern for high-stakes code review.

---

##  AI Wayback Machine
 **Herbert Simon** (1916–2001) — American polymath, Nobel laureate in economics, Turing Award winner, founding figure of artificial intelligence and cognitive science, whose 1962 paper *"The Architecture of Complexity"* formalized the principle of **nearly decomposable systems**: that large complex systems are best understood and operated upon as *clusters of subsystems* that are tightly connected internally and loosely connected externally.[^1] Simon's argument was that the cognitive trick that makes complexity tractable is the boundary between the inside and the outside of a subsystem — the inside is detailed; the outside is summary.

A subagent is Simon's nearly decomposable subsystem applied to Claude Code. The pattern-analyzer's inside is detailed: 25 submissions read, parsed, scored, compared. The pattern-analyzer's outside — what the main session sees — is a summary. The summary is the *external interface* of the subsystem; the per-submission work is the *internal detail*. The main session operates on the summary; it does not need the detail. Simon's claim was that this boundary is what makes complex systems thinkable; the chapter's claim is that this boundary is what makes complex builds tractable.

Simon's deeper insight was that nearly-decomposable architecture is *not* an arbitrary design choice — it is the architecture that complex systems converge toward because the alternative (every part connected to every other part) does not scale. The subagent in Claude Code is the design choice that makes long sessions possible; the main-session-only design hits a context wall that no amount of skill or hook configuration can solve. Decomposition is the architecture; subagents are the operational form.

The chapter recommends single subagents for student-scale work; Simon's framework anticipates the agent-team future where multiple subagents are coordinated by a main agent, each handling one nearly-decomposable subsystem of a larger build. The principle is the same. The complexity it makes tractable scales with the practitioner.

---

## Bridge

Act Two is closed. The teacher has the framework: CLAUDE.md, specifications, handoff conditions, capacities, Skills, Hooks, the narrowing principle, subagents. The grading tool is working. Chapter 10 opens Act Three with the Bridge — the moment the build stops being about productivity and starts being about what your classroom can have that did not exist before.

---

[^1]: Simon, H. A. "The Architecture of Complexity." *Proceedings of the American Philosophical Society* 106, no. 6 (1962): 467–482. Later expanded as Chapter 7 of *The Sciences of the Artificial*, MIT Press, third edition 1996.
