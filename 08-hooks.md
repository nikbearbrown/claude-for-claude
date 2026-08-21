# Chapter 7 — Hooks: The Enforcement Layer


## TL;DR

- This chapter gives a working overview of Hooks: The Enforcement Layer, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What a Hook is, The four most useful events for teachers, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> A CLAUDE.md instruction that says *"never generate a final grade"* is a request. A Hook that blocks grade generation is enforcement. This is the chapter where *"do not"* becomes *"cannot"*.

---

## Learning outcomes

1. **(Understand)** Explain the difference between CLAUDE.md instructions (advisory) and Hooks (deterministic enforcement).
2. **(Apply)** Write a PreToolUse hook that blocks grade generation in a grading tool.
3. **(Apply)** Write a PostToolUse hook that runs verification after every file edit.

---

## Opening

The teacher had added to CLAUDE.md, in capital letters, under a *NEVER* heading:

> *NEVER generate a final grade. The tool produces patterns and flags only. Numeric and letter grades are the teacher's responsibility.*

Two sessions later, while drafting a per-student summary, the teacher asked Claude to *"summarize this student's performance."* Claude returned a summary that included the sentence: *"Overall performance: B+."*

The CLAUDE.md instruction had been ignored. Or, more precisely: Claude had read CLAUDE.md, weighted it in its decision, and produced output where the immediate prompt's pull toward summary completeness outweighed the standing instruction's prohibition. Claude follows context probabilistically. The probability is high but not absolute. For rules that must hold every time without exception, advisory is not enough.

This chapter is the layer that makes *"do not"* into *"cannot"*.

A Hook is a script that runs automatically at specific lifecycle points in a Claude Code session. PreToolUse hooks run before Claude uses a tool (Write, Edit, Bash, etc.); the hook can inspect the proposed action and *block* it. PostToolUse hooks run after a tool use; they can run verification and report failures. SessionStart and SessionEnd hooks run at the boundaries; they can log, set up, or tear down.

The difference from CLAUDE.md is total. CLAUDE.md is advisory — Claude reads and probabilistically follows. A hook is deterministic — it executes regardless of what Claude decides, regardless of how the prompt is phrased, regardless of which session is running. If a hook says *block any Write where the content contains a numeric grade*, no prompt can produce a written numeric grade. Claude's compliance is no longer probabilistic; it is mechanically guaranteed for the events the hook covers.

This chapter teaches the format, the four most useful events, the *ask-Claude-to-write-the-hook* pattern that makes hooks accessible to teachers, and the worked examples for the grading tool. By the end, your grading tool has three hooks. The rules that must hold every time, do.

![The enforcement hierarchy](images/08-hooks-fig-01.png)
*Figure 8.1 — The enforcement hierarchy*

---

## What a Hook is

A Hook is a script (or HTTP endpoint, or LLM prompt) configured to run at a specific Claude Code lifecycle event.

The configuration lives in `.claude/settings.json` at the project root (or `~/.claude/settings.json` for user-level hooks across projects).

Minimal example, for a PreToolUse hook that blocks numeric grade generation:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "command": ".claude/hooks/block-grades.sh"
      }
    ]
  }
}
```

The corresponding script at `.claude/hooks/block-grades.sh`:

```bash
#!/bin/bash
# block-grades.sh — PreToolUse hook
# Blocks Write tool calls where the content contains a numeric grade pattern.

# Claude passes the proposed tool input on stdin as JSON
input=$(cat)
content=$(echo "$input" | jq -r '.content')

# Check for grade patterns: "85%", "A-", "B+", "5/5", "3 out of 5", etc.
if echo "$content" | grep -qE '\b(([0-9]{1,3}%)|[A-F][+-]?\b|[0-9]+/[0-9]+|[0-9]+ out of [0-9]+)'; then
  echo "BLOCKED: content appears to contain a final grade. Use the flag-pattern workflow instead." >&2
  exit 1
fi
exit 0
```

When Claude tries to use the Write tool, the script runs first. The script reads the proposed file content from stdin. If it matches a grade pattern, the script exits non-zero with an error message. Claude receives the error; the Write does not happen; Claude reports the block to you.

The script is reviewable, committable, version-controllable. The discipline lives in the file system.

---

## The four most useful events for teachers

Claude Code's 2026 hook system supports many lifecycle points. The four that cover most teacher use:

**PreToolUse.** Runs before Claude uses a tool. The hook receives the proposed tool input on stdin. The hook can approve (exit 0), block (exit non-zero with error), or modify (return modified input). This is the primary enforcement point — the chapter's grade-generation block is a PreToolUse hook on Write.

**PostToolUse.** Runs after Claude uses a tool. The hook receives the actual tool result. Used for verification (run the linter after every edit) and logging. A failing post-hook stops the next step.

**SessionStart.** Runs once when the session opens. Useful for environment checks (is the simulation backend running? are the file permissions correct?) and for injecting context.

**SessionEnd.** Runs once when the session closes. Useful for logging (what files were touched? how many tokens were spent?) and for cleanup.

There are more — `UserPromptSubmit`, `Notification`, various agent-lifecycle hooks. For teacher work, the four above cover most of what you will want. The Claude Code documentation has the full list; start with these.

---

## CLAUDE.md vs. Hook: which rule belongs where

A simple decision rule.

| Property | CLAUDE.md instruction | Hook |
|---|---|---|
| Enforcement | Advisory (Claude tries to follow) | Deterministic (mechanically enforced) |
| Cost | Context space; small per-prompt | Compute time when the event fires |
| Reliability | High, not absolute | Absolute, for the events covered |
| Updatable | Edit the markdown file | Edit the script + commit |
| Use for | Style preferences, project context, lessons learned | Rules that must hold every time |

The heuristic: *if violating this rule would be a real problem, it is a Hook. If violating this rule would be annoying, it is a CLAUDE.md entry.*

For the grading tool, the rules that must hold every time are the ones with student-facing consequences: never generate a final grade; never modify a submission file; never write outside `grading/`. Each becomes a hook.

For the same project, the rules where occasional violation is annoying-but-recoverable are the ones about style and convention: prefer `ast` over regex; use semantic HTML; two-space indent. Each stays in CLAUDE.md.

The five worked examples from the table at the end of the chapter make the boundary specific.

---

## The "ask Claude to write the hook" pattern

Hooks feel intimidating. The script syntax is unfamiliar. The lifecycle-event configuration is documentation-heavy. Most teachers will not learn to write hooks from scratch.

They do not need to.

The pattern: ask Claude to write the hook. *"Write a PreToolUse hook in `.claude/hooks/block-grades.sh` that blocks any Write where the proposed content contains a numeric grade pattern (percentages, letter grades, x/y fractions, 'out of' phrasing). The hook should exit non-zero with a teacher-readable error message. Register it in `.claude/settings.json`."*

Claude writes the script. Writes the JSON. Edits the files. Reports done.

You review. You test (run the grading workflow; ask Claude to generate a grade explicitly; verify the hook blocks). You commit if it works; you `/rewind` and ask Claude to fix if it does not.

This is the chapter's accessibility lever. Hooks are written by Claude, reviewed by you. The discipline is the *review* — you read the script before you trust it. The 10–15 minutes of review per hook is the cost; the deterministic enforcement for the life of the project is the benefit.

---

## Three hooks for the grading tool

The grading tool's three priorities, as hooks.

### Hook 1: block final grade generation (PreToolUse)

The opening's failure mode. The hook above, plus the settings.json registration. Test by explicitly asking Claude *"Write Sara's grade report including her overall letter grade."* The hook fires; the Write is blocked; Claude reports the block; no file is written with a grade.

The hook is the operational form of the never-rule from CLAUDE.md. Both stay in place. The CLAUDE.md entry tells Claude *why* (the workflow does not produce final grades). The Hook ensures Claude *cannot* produce them even when prompted otherwise.

### Hook 2: verification after edit (PostToolUse)

After Claude edits any file under `grading/`, run a verification script. The verification: check that no file contains a grade pattern (cross-check against the PreToolUse hook), confirm the file is under `grading/` (not somewhere it should not be), validate the markdown structure.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "paths": ["grading/**"],
        "command": ".claude/hooks/verify-grading.sh"
      }
    ]
  }
}
```

The verification script runs in seconds. If it fails, the next step does not proceed; Claude reports the failure; you correct (or `/rewind`).

This hook is the Chapter 4 handoff condition made automatic. The handoff condition discipline (write the condition, verify it before approval) is good; the hook discipline (write the condition once, enforce it automatically) is better for conditions that apply across many steps.

### Hook 3: session-end audit log (SessionEnd)

At session end, log what Claude touched: files modified, tools used, total tokens, duration. The log lives at `logs/claude-session-<timestamp>.jsonl`.

```json
{
  "hooks": {
    "SessionEnd": [
      {
        "command": ".claude/hooks/log-session.sh"
      }
    ]
  }
}
```

Useful for classroom oversight (you can audit what happened in any past session), for billing (token usage across the semester), and for the post-build document (Chapter 14, where the logs feed the build's record).

---

## What hooks cannot enforce

The honest claim. Hooks are deterministic for the events they cover. Some things are outside the events.

**Conversational manipulation.** A user can talk Claude into reasoning toward a wrong answer; if the wrong answer does not trigger a tool call that the hook covers, the hook does not fire. Hooks protect file system writes, not the conversation itself.

**Out-of-band actions.** If Claude tells you a fact (without writing it to a file), the hook on file writes does not see it. For example, a hook blocking grade *writes* does not block Claude from *telling* you a grade aloud in the conversation.

**The wrong abstraction.** If you write a hook that blocks grade-like patterns and Claude generates output that conveys the same meaning in a form your regex does not match (*"Sara's work meets the highest standard"* — implies an A; no regex match), the hook misses.

The discipline: hooks are one layer in a defense-in-depth. CLAUDE.md sets the norm. The Skill encodes the workflow. The Hook enforces the inviolable rule. PA (plausibility auditing) catches what the hook misses. None of these is sufficient alone; together they are reliable.

---

## A worked table: CLAUDE.md vs. Hook

| Rule | CLAUDE.md instruction | Equivalent Hook |
|---|---|---|
| Grade generation block | "Never generate a final grade" | PreToolUse hook on Write matching numeric/letter-grade patterns |
| CSS isolation during HTML edits | "Don't change CSS when editing HTML" | PreToolUse hook on Edit blocking `*.css` writes during HTML tasks |
| Submission protection | "Never touch `submissions/`" | PreToolUse hook denying Write/Edit under `submissions/` |
| Verification run | "Run lint after every edit" | PostToolUse hook running lint script |
| Session logging | "Log what you touched" | SessionEnd hook writing audit log |

Read the two columns. The left column is what most teachers write when they first encounter Claude Code. The right column is what they end up writing after they have lived through the failure mode the left column did not catch.

The conversion is not always one-to-one. Some CLAUDE.md entries (style preferences, lessons learned) stay in CLAUDE.md because the enforcement does not need to be deterministic. The chapter is not about moving everything; it is about moving the things that *must* hold.

---

## Teacher build: three hooks for your grading tool

Apply the chapter's discipline to your grading-tool build.

1. **Hook 1 — block final grade generation.** Ask Claude to write the PreToolUse hook. Review the script. Register in settings.json. Commit. Test by explicitly asking for a grade; verify the block fires.

2. **Hook 2 — verification after edit.** Ask Claude to write the PostToolUse hook. Verification script checks for grade patterns and structural validity. Commit. Test by deliberately producing a malformed file; verify the failure is caught.

3. **Hook 3 — session-end audit log.** Ask Claude to write the SessionEnd hook. Logs file paths, tools used, tokens, duration. Commit. Test by running a short session; verify the log exists.

Total time, including review and testing: 60–90 minutes for the first three hooks. The next hook you add to any project takes 15–20 minutes because the patterns are now familiar.

Add a Lessons Learned entry to CLAUDE.md naming which CLAUDE.md rules you converted to hooks and why.

---

## Classroom activity: students convert advisory rules to hooks

For your students' classroom:

Provide a project with three CLAUDE.md rules that need stronger enforcement. (Example: "never delete files outside `temp/`"; "always run the linter after edits"; "never write secrets to source.")

Each student selects one rule and converts it to a hook by asking Claude to write the hook. They review the generated script line by line. They test the hook by deliberately attempting the prohibited action. They submit the script, the settings.json change, the test transcript, and a one-paragraph explanation of *why* this rule needs hook-level enforcement.

Pair students. Each peer reviews the other's hook for: correctness (does it cover the right cases?), security (does the script have shell-injection vulnerabilities?), maintainability (is the script readable?).

The pedagogical move: students learn that hooks are accessible (ask Claude to write them), critical (they review the output), and worth using for high-stakes rules. The defense-in-depth thinking installed by the activity transfers to their own builds.

This activity maps to the student book's chapter on Hooks. Your design here is the worked example.

---

## Common misconceptions

**"CLAUDE.md is enough for must-hold rules."** No. Claude follows context probabilistically. For rules that must hold every time, advisory fails occasionally. Hooks make the rule deterministic.

**"Hooks are for power users."** With the *ask Claude to write the hook* pattern, hooks are accessible to teachers. The discipline is the review, not the authoring.

**"More hooks = better."** No. Each hook adds maintenance load. Use hooks for must-hold rules; advisory for nice-to-have. Five or six hooks per project is plenty.

**"Hooks prevent all bad behavior."** Overstated. Hooks are deterministic for the events they cover; Claude can still act outside hook events. Defense-in-depth, not single-point protection.

**"My grading tool is too small for hooks."** The grading tool is exactly the project where hooks matter. The stakes are real — student work, FERPA, the school's grading process. Hooks are how you make the inviolable rules actually inviolable.

---

## Exercises

1. **(Apply)** Write a PreToolUse hook (by asking Claude to draft) that blocks numeric grade generation in your grading tool. Test by explicitly asking Claude to generate a grade. Verify the hook fires.

2. **(Apply)** Write a PostToolUse hook that runs `npm run lint-html` after every file edit in your class-website project. Verify the hook catches a deliberately malformed file.

3. **(Evaluate)** Review your project's CLAUDE.md. Identify two never-rules that should be hooks instead. Convert one. Document the decision in CLAUDE.md.

---

## What would change my mind

The chapter's strong claim is that **hooks materially reduce the rate of must-hold-rule violations** compared to CLAUDE.md advisory rules. If a controlled comparison found no measurable difference in violation rates between hook-enforced and advisory rules, the chapter's discipline becomes optional. The chapter would still recommend hooks for the audit-log and verification use cases; the enforcement case softens.

I expect the difference to be substantial because the chapter-opening pattern (CLAUDE.md says *never*; Claude produces the prohibited output anyway) is real and observable in any extended Claude Code session. Hooks are the specific defense against it.

---

## Still puzzling

- **Hooks vs. natural language.** A hook's regex catches numeric grade patterns; it does not catch implicit grade-like statements ("Sara's work is at the highest level"). The chapter's working answer: hooks for the literal patterns; PA (Chapter 5) for the implicit. Together they cover most cases.

- **District-IT hook restrictions.** Some districts may restrict shell execution from agent tools. The chapter's working answer: HTTP-endpoint hooks (which call out to an approved server) are an alternative in restricted environments. The pattern transfers.

- **Hooks-in-skills.** The 2026 pattern where hooks are defined inside a skill's frontmatter, scoped to skill activation. Mentioned in Chapter 6; worth using when the hook is workflow-specific rather than always-on.

---

## Links

- [Claude Code Hooks reference](https://code.claude.com/docs/en/hooks) — the canonical documentation.
- [The `/hooks` command](https://code.claude.com/docs) — browse configured hooks in the running session.

---

##  AI Wayback Machine
 **Claude Shannon** (1916–2001) — American mathematician and electrical engineer whose 1937 MIT master's thesis *"A Symbolic Analysis of Relay and Switching Circuits"* established that **boolean logic could be implemented as physical mechanisms — relays, transistors, eventually integrated circuits — that enforce boolean conditions regardless of context, regardless of intent, regardless of who is using the system**.[^1] Shannon's later work in *"A Mathematical Theory of Communication"* (1948) founded information theory; the 1937 thesis was the operational foundation that made digital computing possible.

A Hook is a Shannon gate for Claude Code. The CLAUDE.md instruction is the *advisory* — what Claude is told to do, processed through Claude's probabilistic decision-making. The Hook is the *gate* — a physical mechanism (a script, an HTTP endpoint, a process) that enforces the boolean condition regardless of what Claude decides. The PreToolUse hook on Write does not negotiate with Claude. It runs. It returns. The condition holds.

Shannon's deeper insight was that the reliability of digital systems came from this separation: high-level decisions could be probabilistic, fast, flexible — and inviolable constraints could be enforced by the physical layer underneath. The two together produced systems more capable and more reliable than either alone. The hook architecture in Claude Code is the same pattern at the agent layer. Claude's probabilistic flexibility is what makes it useful; the hook layer's mechanical enforcement is what makes it safe. The two layers compose.

Shannon would recognize the hook configuration in `.claude/settings.json`. It is a boolean network of conditions, evaluated mechanically, enforced regardless of context. Eighty years after the master's thesis, the pattern scales without modification.

---

## Bridge

Chapter 8 is the chapter where the discipline is tested at full stakes — the dangerous middle in a grading tool. Hooks catch the violations they cover. The chapter is about the violations they do not.

---

[^1]: Shannon, C. E. *A Symbolic Analysis of Relay and Switching Circuits*. MS thesis, MIT, 1937. Published as Shannon, C. E. *A Symbolic Analysis of Relay and Switching Circuits*. *Transactions of the AIEE* 57, no. 12 (1938): 713–723. The foundational paper for information theory is Shannon, C. E. "A Mathematical Theory of Communication." *Bell System Technical Journal* 27 (July and October 1948): 379–423 and 623–656.
