# Chapter 12 — Running the Build: Claude Tasks and Human Tasks

It is Saturday morning. The Boondoggle Score for Phase 1 of the application tracker is on my left monitor. Five rows. Two mine, three Claude's. Every row has a handoff condition. The plan is finished, and it is the most important document in the room.

On the right monitor, Claude Code is blinking inside the project directory. The directory has one file: `types.ts`, which I wrote by hand in row 1 because row 1's labor column says *human*. I read it twice. Row 1 passes. I move to row 2.

Row 2 says: scaffold the HTML page; handoff condition: page loads with empty root, no console errors. I paste a prompt. Claude produces an `index.html`. I open it in the browser. Page loads. Console clean. Row 2 passes.

Row 3 is where the morning gets interesting. Implement `addApplication`, `toggleSubmitted`, `deleteApplication`; handoff condition: three unit tests pass on an `Application[]` of length 0, 1, 3. I paste a prompt. Claude produces three functions and three tests. I run the tests. All three pass. I read the diff. Something is off. `deleteApplication` is removing the entry with `applications.splice(index, 1)` instead of `applications.filter(a => a.id !== id)`. Splice mutates. Filter returns a new array. My SDD did not say *immutable updates only* — the handoff condition I wrote does not catch it. The tests pass with splice. They also pass with filter. The tests are blind to the thing I am suddenly seeing.

This is the moment the chapter is about. I do not have a test that has failed. I have an output that passes the handoff condition I wrote and still fails the condition I should have written. The tests-pass path is fast. The reject-and-respecify path is slow. The reject-and-respecify path is the chapter.

I press Esc twice. The conversation rewinds to before the row 3 prompt. The files Claude wrote are gone. I rewrite the prompt with one added sentence: *Use only immutable updates — return new arrays from `toggleSubmitted` and `deleteApplication`; do not mutate the input.* I paste. Claude produces three functions using spread, map, and filter. The tests pass. Row 3 passes.

That is what running a build feels like. Not pressing *accept all changes* on each output — evaluating, at every row, against the condition I wrote and the condition I should have written; rejecting the ones that fail either test; tightening the spec when the spec was thin. The build is not a series of approvals. It is a series of *audits*, performed by a person who can stop the line.

![Three boxes across the top — Specification, Claude executes, Handoff check — with a side badge labeling the check step as governed by Plausibility Auditing. The check branches into Pass leading to Next step, and Fail leading to /rewind and respecify, which loops back to Specification.](images/12-running-the-build-fig-01.png)
*Figure 12.1 — The build loop: specification, execution, audit, branch*

---

Running a Claude row has four parts, in order.

**Read the row.** Out loud, if you are alone. The row contains a step, a handoff condition, and — implicitly — a scope. Row 3 said *implement `addApplication`, `toggleSubmitted`, `deleteApplication`*. It did not say *implement a `TaskList` component* or *add styling*. The prompt has to honor what the row is silent about as much as what it says.

**Write the prompt from the row.** The specification is already in the row; the prompt assembles the row, the relevant SDD section, and the acceptance criteria into one paragraph. The temptation is to skip the row and prompt from the SDD directly. Resist. The row exists so the prompt is bounded; skip the row, lose the bound.

**Paste, then read what Claude produces.** Not run it. *Read it.* The diff, the new files, the function bodies. You are looking for two things: does the output do the row, and does it do anything *other* than the row? Both are failure conditions. Doing-the-row failure gets a tighter prompt. Doing-more-than-the-row gets a `/rewind` and a scope fence.

**Run the handoff condition.** Not "look at it and decide it is fine." *Run* it. If the condition is *three tests pass*, run the tests. If it is *refresh preserves state across 10 toggles*, toggle ten things and refresh. The condition was written down so that evaluation does not require a judgment call.

Running a human row is different. Human rows are not the easy rows; they are the rows whose output everything else inherits from, which is why they sit at the top of the score. Each human row has a supervisory capacity named in the table. That naming is the binding instruction.

Row 1 named **[PF] Problem Formulation**: deciding what the data model *is*, in writing, before Claude sees it. Row 1's labor is not typing the twelve lines; it is committing to a shape. Row 4 named **[IJ] Interpretive Judgment**: the aesthetic call on visual hierarchy. Three CSS variables — font, primary color, accent — written before Claude touches any style. The handoff condition for row 4 is *three variables named before Claude writes any styles*. The *before* is what Claude inherits. After row 4, Claude does not pick colors. Claude picks within the colors row 4 already chose.

The two rows are short in lines and large in supervisory load. That is the signature of a well-placed human row. If a human row feels like typing, the labor is mislabeled.

---

The condition fails. Tests don't pass, the page doesn't load, the refresh doesn't preserve state. What now.

The wrong move — the move every student makes the first time, and most make the first ten times — is forward correction. Read the failing output, see what is wrong, paste a new prompt: *the toggle function isn't working on an empty array, please fix it.* The new prompt does not replace the old one. It is appended to a conversation that already contains the failed attempt and the misunderstanding that produced it. Claude's next output is conditioned on both. The next output usually fixes the named symptom and introduces a new one, because the underlying misunderstanding has not been touched.

The right move is `/rewind`. In Claude Code as of May 2026, that is the `/rewind` command or Esc-Esc, both of which restore conversation and code to the state before the last prompt. You are not rolling back the symptom. You are rolling back the cause — the prompt that produced the misunderstanding — and re-specifying. It is the keystroke equivalent of pulling Toyota's andon cord: stop the line, fix the cause, restart.

The re-specification is the part that takes thought. The new prompt is not the old prompt plus a complaint. It is the old prompt with the misunderstanding written *out* of it. In row 3, the misunderstanding was that I had not said *immutable*. The new prompt added the sentence that closed the ambiguity. A heuristic: the new prompt should be strictly more specified than the old one in at least one named dimension. If the diff between the two prompts is a complaint sentence, wrong diff. If the diff is a constraint sentence, right diff.

The whole loop — read failed output, identify missing constraint, rewind, paste re-specified prompt, evaluate — is under five minutes once practiced. The first time it takes thirty, because the temptation to forward-correct is enormous and the rewind feels expensive. It is not. It is the cheapest move in the build. Forward correction adds tokens to a context that already contains the misunderstanding, and context past a point is where quality degrades. Kexun Liu and colleagues, in a 2025 paper at EMNLP Findings, showed systematic degradation of LLM output quality as input context grows, even when retrieval is perfect — across five frontier models and three task categories. Forward correction makes the context longer. Rewind makes it shorter. The empirical case for the keystroke is in this literature.

---

Now here is the failure mode I almost missed in row 3. The handoff condition passed. Tests ran green. The output should have shipped. Something in the diff felt wrong, and the wrong feeling was correct.

The capacity Chapter 5 named is **[PA] Plausibility Auditing**: hearing the wrong note before verification confirms it. There is no checklist for the feeling that something is off. There is only the discipline of taking the feeling seriously when it appears.

The discipline has two halves. The first is permission. Most students, when a Claude output passes the condition and feels wrong, override the feeling. The override is rational-sounding: the tests passed, who am I to second-guess. The override is the failure mode. Plausibility auditing is, by definition, the capacity to register wrongness before a test catches it; if you only trust feelings backed by failed tests, you have no plausibility auditing — only post-hoc justification.

The second half is investigation. A wrong-feeling output you cannot articulate is not yet a wrong output; it is a hypothesis. In row 3, the investigation was: what specifically am I seeing? Splice instead of filter. Why does that bother me? It mutates the input array, and mutating shared state in event handlers is a bug class I do not want. What handoff condition would have caught it? A test that calls the function twice and checks the original array is unchanged. The whole investigation took under two minutes and produced a confirmed problem, a sharper handoff condition, and a tighter prompt.

What plausibility auditing does not mean: second-guessing every Claude output until you have verified each line. That makes Claude useless. It means cultivating the specific skill of registering and investigating the *wrong note* — the moment when something compiled, tested, and presentable nevertheless reads off. Most outputs do not produce that feeling. The ones that do are the ones to investigate.

---

Halfway through Phase 1, Claude produced an `index.html` with a `<style>` block in the head containing default styles. The row had asked for *head, single root div*. The row had not asked for styles. Row 4 — *decide visual hierarchy* — had not yet run. Claude had quietly executed half of row 4 inside row 2.

This is scope creep, and it is the most common Claude failure on a Boondoggled build that has otherwise gone well — because Claude is helpful, and helpful means doing more than the row asks. The output reads as a gift: *I also added X while I was here.* The gift is a bug. It moves work from a row whose supervisory capacity has been named to one whose capacity is silent.

The response is `/rewind` plus an explicit scope fence in the re-prompt: *Do not add styles. Row 4 handles styles. Limit output to head, root div, minimal HTML to load.* The `<style>` block disappears. Row 2 does the row and nothing else.

A second pattern, less obvious but more dangerous: Claude proposes a refactor during a feature implementation. *I noticed `addApplication` could be cleaner if we refactored the index lookup into a helper. While I was at it, I made that change.* The refactor may be correct. The problem is not its quality — it is that a change not on the score has been smuggled into a row whose handoff condition does not test for it.

The response is `/rewind`, scope fence, and a STOP block: *Do not refactor surrounding code. Any refactor proposal goes in a top-of-file comment labeled `// REFACTOR PROPOSAL` and is otherwise untouched.* The comment turns the smuggled change into a suggestion — a separate row, evaluated separately, later.

*While I was here* is the phrase that ends Boondoggled builds.

---

The two-rewind rule: the first rewind is normal. The second rewind on the same row for the same kind of failure is information — the problem is no longer in Claude's output but in the spec. Stop. Open the SDD and the Boondoggle Score side by side. Ask: is the handoff condition a test for what I want, or for what I could write down? Is the SDD section this row inherits from thin? Usually one answer is yes, and the fix is in the document, not in the next prompt.

After a third failed rewind, stop the build entirely. The plan is broken; more prompts against a broken plan produce code you will throw away. Walk back through the design gate, thicken the thin section, rewrite the row, restart from the last passing row.

The opposite failure is also real. Some outputs almost pass — the row is done, but one cosmetic thing needs fixing. Push through is right when the fix is faster than re-prompting, does not require re-running the handoff condition, and is small enough not to lose. The line between push-through and rewind is whether the fix changes *meaning* or only *surface*. Variable names and comments are surface. Algorithmic choices, data shapes, mutation versus immutability, error handling — those are meaning. Surface, push through; meaning, rewind.

---

One piece of standing infrastructure changes the verification picture, and it should be built before any Claude row that warrants it.

The infrastructure is a verification mechanism Claude can run against itself — a single bash command whose exit code is the handoff condition's pass/fail signal. In Phase 1 of the application tracker, that is `npm test`. In a Python project it might be `pytest` and `ruff`. The form does not matter. What matters is that the mechanism exists.

Once it does, the prompt for a Claude row changes shape. Not *implement the functions, here are the requirements*. Instead: *implement the functions; run `npm test` after each iteration; iterate until all tests pass.* Claude is now in a feedback loop with a verification mechanism, and the loop closes without the human in the middle. This is not delegation — the human wrote the tests; the tests *are* the handoff condition; the human still audits the final output. What has changed is that Claude is iterating against a feedback mechanism the human installed, not producing single-shot output against a spec.

Sandeep Lahiri and colleagues, in a 2024 study on test-driven interactive code generation, found that LLM coding agents improved pass@1 by roughly 46 percent within five user interactions when iterating against a test signal, with diminishing returns past three to four iterations. That ceiling is also the empirical basis for the two-rewind rule: if the feedback loop hasn't produced the right output in three to four iterations, the spec is wrong, not the model.

The discipline reorders the build. The verification mechanism is row zero — the row before any Claude row that needs it. In the application tracker, row 0 was *write `app-fns.test.ts` with the three unit tests*. Written by hand. The tests were the handoff condition for row 3 before row 3 ran. The row 3 prompt included *the tests in `app-fns.test.ts` must pass*. Claude implemented, ran, iterated, reported all three passing. I read the diff and ran the tests independently. Row 3 closed in one prompt because the test was the spec and the spec ran on every iteration.

This is the most important operational move in the chapter. The tests are not your final inspection; they are Claude's handoff condition. You write the test, Claude works against it, you audit. The labor stack is: human at top, defining correct; Claude in the middle, implementing; verification mechanism at the bottom, providing the binary signal. This is Deming's *quality built in, not inspected in*.

---

After any group of unrelated rows, `/clear`. The command wipes the conversation history while preserving the code on disk. Claude has no memory of previous prompts, outputs, or corrections. The project files still exist; the conversation does not.

Mechanically, `/clear` resets the working context window — the running transcript of prompts, outputs, tool calls, and tool results that Claude re-reads from the top on every turn. That transcript is what Claude "knows" in the moment, and it is finite. Everything in it does two things: it consumes the budget, and it conditions the next output. After `/clear`, the transcript is empty. The accumulated, now-irrelevant conversation stops eating the budget, and — just as important — it stops *biasing* the model. Claude is no longer pattern-matching against the dead ends, the abandoned approaches, and the corrections that filled the prior session. It starts the next row from a clean slate, with only the project files on disk to look at.

When to reach for it. After you finish a discrete task and move to an unrelated one — the seam between two groups of rows is the natural `/clear` point. When the context has filled with material that no longer earns its place: a long string of failed attempts, an abandoned design you talked yourself out of, a thousand lines of test output or a stack trace you have already acted on. And when you notice Claude *referring back* to stale decisions — citing a constraint you dropped two rows ago, reusing an approach you rejected, reasoning from a version of the plan that no longer exists. That last symptom is the clearest tell. When the model starts arguing from a past you have moved beyond, the past is in the context window, and `/clear` is how you remove it.

When *not* to. Not mid-task, and not when the accumulated context is still load-bearing. If the current row depends on a chain of reasoning, a set of constraints, or a worked-out understanding that lives only in the conversation, `/clear` throws it away — and unlike code on disk, the conversation does not come back. The test is whether the context is still doing work. If the last twenty turns are why the next output will be correct, keep them. If they are residue from a task that is finished, clear them.

How it differs from the neighbors. `/clear` is not compacting. Compacting summarizes the conversation so far and replaces the full transcript with the summary — it preserves the gist while shrinking the budget, which is what you want when the history still matters but is getting long. `/clear` keeps nothing; it is the right move precisely when the gist is *not* worth preserving. And both differ from simply continuing, which keeps the entire transcript and pays its full cost on every subsequent turn. Continue when the context is load-bearing; compact when it is long but still relevant; clear when it is finished and in the way.

Between unrelated rows: the history from row 5 — the failed first attempt, the test output, the corrected version — is irrelevant to row 6. Worse than irrelevant: it is active interference. The context-rot literature shows output quality degrading monotonically with context length, even when retrieval is perfect. The row 5 conversation is making row 6's output worse. `/clear` is hygiene.

After repeated correction on the same issue: the reformulated spec should be pasted into a cleared conversation, not the polluted one. Clear first. Then paste the rewritten spec.

The reason `/clear` is safe — the reason you can throw the conversation away without losing the build — is that the durable instructions do not live in the conversation. They live in CLAUDE.md. The persistent project context survives a `/clear` because Claude reloads it; the stack, the conventions, the negative constraints, the things-never-to-improvise-about are reread from the file at the start of the next turn. The transient chat does not survive, and it does not need to. This is the division of labor the whole book has been building toward: anything that must persist is written down in CLAUDE.md, so the conversation is free to be disposable. `/clear` is only cheap because the important things were never in the chat to begin with.

There is an emotional component worth naming. `/clear` feels expensive. The conversation feels like progress; clearing it feels like losing progress. But the conversation is not the progress — the code on disk is, and the instructions in CLAUDE.md are. The conversation is infrastructure that has accumulated rot. If you have corrected Claude more than twice on the same issue, the context is polluted. Clear and rewrite the spec with the misunderstanding written out.

---

What follows is the full build session for Phase 1. Five rows, plus row 0. One rejection, one rewind, one re-specification.

**Row 0** — Write the verification mechanism (Human). Before any Claude row, I wrote `app-fns.test.ts` by hand: three tests, one per function, including immutability assertions on the input arrays. Ran `npm test`. Three failures with "module not found" — `app-fns.ts` does not exist yet. Correct. Row 0 passes.

**Row 1** — Define the data model (Human, **[PF]**). Written by hand into `types.ts`: one interface, three fields. Handoff: file exists, one interface, fields match the SDD. Passes.

**Row 2** — Scaffold the HTML page (Claude, **[TO]**). Prompt included explicit scope fence: *no inline styles, no `<style>` block; row 4 handles all styling.* Claude returned a clean `index.html` — head, root div, nothing else. Opened in the dev server. Loads clean. Passes.

**Row 3** — Implement the three application functions (Claude, **[PA]**). First prompt: implement the three functions, run `npm test`. Claude returned implementations using `splice` and in-place assignment. Two immutability tests failed; one passed. Read the diff: mutations throughout. Wrong-feeling confirmed by test results.

Esc-Esc. Rewind. Re-specified prompt: one sentence added — *use only immutable updates; use spread, `map`, and `filter`; do not use `push`, `splice`, or in-place property assignment.* Pasted into clean context. Claude returned spread, map, filter throughout. `npm test`: three passed. Read the diff: no mutations. Row 3 passes on the second attempt.

**Row 4** — Decide visual hierarchy (Human, **[IJ]**). Written by hand: three CSS variables — `--font`, `--primary`, `--accent` — and nothing else. Handoff: three variables present before Claude writes any styles. Passes.

**Row 5** — Wire localStorage with corruption guard (Claude, **[EI]**). Prompt included full behavior spec: storage key, mutation hook, parse-failure dialog, private-browsing fallback, STOP block for any change to `app-fns.ts`. Claude returned a `main.ts` with nested try/catch handling all three cases. Verified by hand: toggled ten items and refreshed; manually corrupted the stored JSON and confirmed both dialog branches. Row 5 passes.

Phase 1 closes. One rewind. One re-specification. All five Chapter 5 capacities engaged across five rows — **[PF]** at rows 0 and 1, **[TO]** at row 2, **[PA]** at row 3, **[IJ]** at row 4, **[EI]** at row 5 — in roughly two hours.

What I have at the end is a small, ugly, working application tracker. Five files. About a hundred and twenty lines of code. Every line either written by me or evaluated against a written handoff condition. There is no decision Claude made without me either writing the specification for it or rejecting its first attempt. The artifact is small. The capability behind it is the chapter.

---

## 🕰️ AI Wayback Machine

**Donald Knuth** (1900–1993) spent the 1950s in postwar Japan, where his lectures on statistical quality control became the philosophical foundation of what the West later called the Japanese economic miracle. His *Out of the Crisis* (MIT Center for Advanced Engineering Study, 1986) organized those lectures into 14 points and the PDSA cycle — Plan, Do, Study, Act — a four-step iterative process for improving any system that produces an output and a way to measure it. Point 3 reads: *cease dependence on inspection to achieve quality; eliminate the need for inspection on a mass basis by building quality into the product in the first place.* The handoff condition is exactly this principle applied to a Claude Code build sequence: quality is the test that lives between two rows, not the audit at the end. The Boondoggle Score sequences verification into every step because every step is the only step where verification is cheap.

**

![Donald Knuth, 1970s. AI-generated portrait based on a public domain photograph.](../images/donald-knuth.jpg)
*Donald Knuth, 1970s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:**

```text
Read Deming's Point 3 from Out of the Crisis (full text available
via the Deming Institute, deming.org). Quote the point verbatim in
fewer than 30 words.

Then translate it from manufacturing language into a one-sentence
operational rule for running a Claude Code build.

Then look at the Boondoggle Score from your Chapter 11 exercise:
identify the row whose handoff condition is the weakest — the row
most likely to require post-build inspection rather than in-loop
verification. Rewrite the handoff condition so that it builds quality
in, in Deming's sense, instead of inspecting it in. Show both versions.
```

---

**Links:** [boondoggling.ai](https://boondoggling.ai) · [irreducibly.xyz](https://irreducibly.xyz)

---
