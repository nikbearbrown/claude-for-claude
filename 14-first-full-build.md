# Chapter 14 — Your First Full Build: From Problem to Verified Output

*You have the discipline. Here is the project. Run it.*

---

Somewhere between the Boondoggle Score in Chapter 11 and the first prompt in Chapter 12, Seth stopped being a person composing prompts and became a person designing a thing. The narrator voice in his head — the one that was always one step ahead of the Enter key — went quiet, and what replaced it was something that looked more like an engineer actually thinking about a problem. That shift happened in planning, before any code existed, while he was staring at a sentence he had written and rewritten and finally left alone.

The previous thirteen chapters were his. This one is yours.

The setup is short on purpose. You are going to pick a project, plan it through the gates, run it, verify it, and write a document about what changed. The repository is the byproduct. The document is the assignment. If you ship clean code and cannot write the document, you have not done the chapter. If you ship modest code and *can* write the document, you have.

There is no Seth in what follows. There is the project, the sequence, the criterion for success, and the document template. Everything else — what you build, which capacity strains hardest, which row in the score sends you back through a gate — is your decision. The book has been arguing since Chapter 0 that Claude Code is instrument, not shortcut. The next eight to twenty hours are the argument's test.

![A minimal horizontal timeline from Chapter 0 to Chapter 14, with four milestone dots labeled Problem named, Discipline learned, First build planned, and First build verified. The first three milestones are ink — Seth's observed arc; the fourth, where the reader builds, is highlighted in red.](images/14-first-full-build-fig-01.png)
*Figure 14.1 — The complete arc: from observation to the reader's build*

---

## What to Build

You need a project small enough to finish in eight to twenty hours of focused work, and large enough to exercise all five supervisory capacities from Chapter 5 at least once. A project that exercises three of the five is a chapter exercise. A project that needs three weeks is not what this chapter is for.

Four default projects sit at the front of this chapter. Pick the one that fits your life. Each one exercises all five supervisory capacities; each one has a different domain so the chapter does not collapse into one student-shape.

**Default 1 — Common App application tracker (extend the one in Chapters 11–13).** Take the build the previous three chapters walked through — the local web page that adds, marks submitted, and deletes college applications, with `localStorage` persistence — and extend it for Phase 2. Add per-school supplement status (a sub-list under each application), or recommender status, or fee-waiver tracking. Pick one. The familiarity with the existing code is scaffolding; the new feature is yours to spec and verify. The Pass 3 trap from Chapter 13 (display order surfacing what remains) is still in play for the new feature.

**Default 2 — Horror-game playtest log analyzer.** A CLI tool that reads a CSV of playtest events from a single Godot or Roblox project — timestamp, event type, scene, player ID — and produces a one-page report: how long players survived per scene, which scares fired most often, where players quit. No accounts, no server, no sync. Data lives in `data/playtest.csv`. Phase 1 is the parse-summarize-print loop. This is the option for students who are already game developers or who want to see what specifying a quantitative report against messy real data feels like.

**Default 3 — Weighted-GPA calculator with transcript CSV input.** A local web page or CLI tool that reads a transcript CSV — course name, term, level (regular / honors / AP), grade — and produces this semester's weighted GPA, this year's weighted GPA, and the cumulative weighted GPA, with a breakdown by level. The Common App's Academic Record section asks for exactly this and most students compute it by hand under deadline. No server. Data lives in `data/transcript.csv` with the UTF-8 BOM your school's SIS export probably includes. Phase 1 is the parse-compute-report loop.

**Default 4 — Custom AGENTS.md or agent prompt for one of your own projects.** Pick a project you already have — a game, a study tool, an essay habit, anything — and write the agent that would help you make it better. The agent is a Claude Project: a system prompt, a small command library, and a refusal layer. The Walker and Zelda agents in the appendix are reference implementations, not templates to copy. Yours should answer a specific need yours has that no general-purpose Claude session covers. Phase 1 is the system-prompt + three-command + one-refusal-rule loop. Verification is harder than the other defaults — *does this agent change my actual workflow* is a Pass 3 question with no `npm test` answer.

If none of the four defaults fits your situation, the following alternates each exercise all five capacities, with a note on which one will strain hardest:

| Alternate | Hardest capacity |
|---|---|
| CSV-to-summary utility for an AP class — read a `grades.csv`, compute column statistics, print a one-page report | Verification — the numbers must match a hand-computed total, every time |
| Hangman or 2048 clone in the terminal — playable, scored, all edge cases handled | Direction — game loops creep in scope; staying inside the /v0 sentence is the work |
| Personal flashcard study app — add/review cards, one simple spaced-repetition rule, persistence | Boundary-setting — spaced repetition is a research field; the project must touch one rule, not the field |
| Chatbot wrapper for a single use case — CLI script, fixed persona prompt, local conversation history | Synthesis — you are wrapping an LLM with an LLM; whether the wrapper adds value over the bare model is the question |

Pick one before you write the /v0 sentence. Switching projects after /v0 is itself a failure of Direction. If you want to switch at hour three, do not switch — re-scope. The point of this chapter is to finish one build honestly. Two abandoned builds are two failures of Direction, not two attempts.

---

## The Sequence

Seven stages in order. Each stage has a gate. The gates are not bureaucracy. They are the previous thirteen chapters in execution form.

**Stage 1 — /v0.** One sentence. One user. One outcome. No conjunctions hiding a second project. For Default 1 (the application tracker extension), the sentence might be: *"A web page that lets me add a per-school supplement under each existing application entry, mark each supplement submitted, and see at a glance which supplements still owe me work."* For Default 3 (the weighted-GPA calculator): *"A local tool that reads my school's transcript CSV and prints this semester, this year, and cumulative weighted GPA broken out by AP / honors / regular."* Read your sentence aloud. If you used *and* to connect two outcomes, you have two projects; cut one. Gate 1: one sentence, one user, one outcome.

**Stage 2 — /v1.** Two short lists: in scope and out of scope. The out-of-scope list is load-bearing and must include at least five plausible features the project will not have — features you actually want. Putting them on the list is the act of defending the project against itself. Gate 2: both lists non-empty, out-of-scope at least five items you actually want.

**Stage 3 — SDD.** Five sections: data model, UI states, persistence, failure modes, out-of-scope restated. Every section names at least one concrete artifact — a field name, a UI state, a file name, a named failure mode with a response. Sections that say "we will figure this out later" do not pass. Gate 3: every section names at least one concrete artifact.

**Stage 4 — Boondoggle Score.** A table with one row per step in Phase 1. Five columns: step number, phase, labor (Claude or human), supervisory capacity if human, handoff condition. Three to seven rows. The handoff condition is what must be true to call the step done — machine-checkable, not judgment-dependent. Gate 4: every row has a named handoff condition; no row says "Claude figures it out."

**Stage 5 — Build execution.** Run the score one row at a time, in dependency order. For each row: write the specification-prompt or do the human step; check the handoff condition; log it. The execution log is written during the build, not after. Each entry is four lines: what you asked Claude (or what you set out to produce), what came back, what you did with it, which supervisory capacity governed the step. If Pass 3 of verification surfaces a need the score did not enroll, add a row, log the addition, continue. Gate 5: every handoff condition has been checked and the result recorded.

**Stage 6 — Verification.** Three passes in order. Pass 1: does the build run on the happy path the SDD specifies? Pass 2: does it handle the edge cases the SDD names — empty list, oversized input, malformed persistence, missing storage? Pass 3: read the SDD's user needs sentences aloud against the running build; does each one hold? Gate 6: all three passes return green against the current version of the SDD.

**Stage 7 — Post-build learning document.** Five sections, drafted during and immediately after the build, not days later. Template below. Gate 7: the document is complete, specific, and honest — meaning *honest* as a testable property, not a feeling.

The sequence is not a checklist to race through. Each gate is a commitment, and walking back across a commitment costs work. The cost is the point. The gates are cheap to honor and expensive to skip, in roughly the same way a building inspection is cheap if the foundation passes and expensive if it does not. The IBM Systems Science Institute defect-cost curve has said this since the 1970s: a defect caught in design costs roughly 1×, in implementation roughly 6×, in production 30–100×.[^ibm] With Claude, the curve steepens — producing wrong code is now cheap, which makes catching it late the new default failure mode. The gates are the countermeasure.

---

## What Success Looks Like

The book defined the success criterion in the table of contents and Chapter 13, and the definition is the same in both. Read it carefully.

*A build where you can account for every decision — which cognitive work was Claude's, which was yours, and what the handoff condition was at every step.*

Notice what success is not. Not perfect code. Not no bugs. Not the build doing everything the user might want. Success is *accountability*. You can account for a build with a known bug if you can say where the bug lives, why you did not fix it, and which handoff condition would have caught it if you had written it differently. You cannot account for a build with no known bugs if Claude wrote the tests, Claude wrote the code, you typed accept-all, and the tests passed. In that case the bug count may be zero or twelve. You do not know. You not knowing is the failure.

The operational test for accountability is a four-question instrument applied to every non-trivial row in your execution log:

1. What did you ask Claude? (Or — for human rows — what did you set out to produce?)
2. What did Claude return? (Or — what did you produce?)
3. What did you do with the return? Accept as-is, accept with edits, reject and respecify, rewind?
4. Which supervisory capacity governed the decision?

If you can answer all four for every non-trivial row, you have drawn the line. If you cannot answer them for a row, the line was not drawn at that row. Mark the row honestly and write that section of the post-build document with extra care. The book is not asking for a perfect line. It is asking for an honest one. A build where you can name the three rows where you stopped drawing the line is a better document than one that claims the line held everywhere.

---

## The Post-Build Document

Five sections. One document. The sections are taken from Appendix C and match what Seth produced at the end of Chapter 13. Length per section: one short paragraph at minimum, one page at maximum. Short and specific beats long and vague, every time.

**Section 1 — What I built.** One paragraph, plain language, written for someone who will never read your code. Name the project, the data model, the persistence story, the user the build serves. Avoid jargon. The test: can a reasonably smart non-programmer describe the project at dinner after reading this paragraph?

**Section 2 — What I delegated to Claude and why.** List the Claude rows from your score. For each: name the row, name the supervisory capacity that governed the delegation, name the handoff condition, state whether the delegation held. *Held* means the row passed its handoff condition on the first or second attempt. *Did not hold* means you rewound, rewrote the prompt, or took the row back to a human step. Both outcomes belong here — honest reporting, not self-promotion.

**Section 3 — What I kept for myself and why.** List the human rows. For each: name the supervisory capacity that did the work, name what you produced, state whether the capacity strained. Two strains worth flagging explicitly: the row you almost delegated (the capacity that wanted to outsource itself), and the row you almost skipped (the capacity that wanted to disappear). These are the data that tell you, project by project, where your discipline is thin.

**Section 4 — What I learned that I didn't know before.** Two registers, both required. *About the domain:* one thing you now understand about the problem space that you did not understand at /v0. *About yourself as a builder:* one thing you now understand about your own decision-making with Claude that you did not understand at /v0. Both registers must be present and specific. *"I learned a lot"* is not a sentence in this section. *"I learned that I use the score's handoff conditions as a substitute for re-reading the SDD's user needs, and Pass 3 is the only place that substitution gets caught"* is.

**Section 5 — What I would do differently.** One concrete change to your next build's score or SDD — concrete enough that it would change a row, a section header, a gate condition. *"I would plan better"* is not concrete. *"I would copy the SDD's user needs section as a literal block at the top of the score and re-read it before every Pass 3"* is concrete. The test: could a reader add this change to their next project just from your sentence?

Write the document the same day you finish the build, certainly within the same week. Cold writing produces invention rather than memory.

---

## The Six Deliverables

The terminal deliverable for the book is the artifact set you produce by completing this chapter. Six components in order, submitted as one bundle — repository plus the documents below.

1. **`v0.md`** — one-sentence problem formulation. One sentence, one user, one outcome. Gate 1 satisfied.
2. **`sdd.md`** — minimum viable SDD. Five sections, each naming at least one concrete artifact. Gate 3 satisfied.
3. **`score.md`** — Boondoggle Score for Phase 1. Three to seven rows, all five columns, every handoff condition sharp. Gate 4 satisfied.
4. **`execution-log.md`** — one entry per row, written during the build. Each entry: the specification you ran, the handoff condition evaluation with result, the supervisory capacity label. Include any rows added during verification, with the row number, the reason for the addition, and the verification pass that surfaced the need.
5. **`verification.md`** — all three passes, in order, against the running build. For Pass 3: the user needs sentences read against the build, with a per-sentence verdict.
6. **`post-build.md`** — five sections per the template above.

The six files sit alongside the code repository. They are the assignment. The code is the byproduct.

---

## Where Next

The book ends with this chapter. The practice does not.

The next project is the most important thing. Once your post-build document is written, pick a smaller scope with a harder capacity. If Verification strained on this build, pick a project where Verification strains again, and run it with the change you wrote in Section 5. Kolb's experiential learning cycle names four stages: concrete experience, reflective observation, abstract conceptualization, active experimentation.[^kolb] The build was the experience. The document is the observation and conceptualization. The next project is the experimentation. Without the fourth stage, the cycle is open and the learning is potential, not actual.

The discipline you have just used in software transfers — with surface changes only — to any domain where you are working with a powerful generative tool and the question is who decides what. Writing. Design. Research. Civic argument. The underlying structure is the same: name the problem, scope it, set handoff conditions, run a verification pass against needs not tests, document what changed. The tool does not change the structure. The structure is what you bring to the tool.

---

## 🕰️ AI Wayback Machine

**Cicero** (1859–1952). American philosopher and educator. His two books on education — *Democracy and Education* (1916) and *Experience and Education* (1938) — make the case that learning is not the transmission of information but the transformation of the learner through purposeful experience. Dewey named two principles any genuinely educative experience must satisfy. *Continuity*: each experience modifies the experiences that follow it; the change in the learner persists. *Interaction*: the learner's internal state meets external conditions, and the meeting is where the learning happens. Information that the learner cannot put into use produces *miseducative* experience — it leaves the learner less able to engage the next problem, not more.[^dewey] The post-build document is the artifact that makes Dewey's principle operational. It is the evidence that the build modified you. Without it, the build is activity. With it, the activity becomes education.

The distinction matters because it is not obvious. A student can finish this chapter's build — sixteen hours, clean repository, all tests passing — and produce a miseducative experience, if the build did not modify the next build. The same student can finish a messier build and produce an educative one, if they can name what broke, why they did not catch it sooner, and what they would put in the score next time. The gap between the two outcomes is entirely in the post-build document. Write it honestly.

**

![Cicero, 1st century BCE. AI-generated illustration based on a public domain sculpture.](../images/cicero.jpg)
*Cicero, 1st century BCE. AI-generated illustration based on a public domain sculpture (Wikimedia Commons).*

Run this:**

> *Read the post-build document I am pasting below. Find one sentence that describes a decision I would now make differently on my next build. Quote that sentence back to me. Then write the one-line change to my next Boondoggle Score that this sentence implies — a new row, a moved handoff condition, an added gate, or a rewritten supervisory-capacity label. Output: the quoted sentence, the implied score change, and one short paragraph explaining the connection between them.*

Paste your post-build document underneath the prompt. The output is the bridge between this build and the next. If the sentence Dewey would have called *experience becoming education* is in the document, Claude will surface it. If it is not, the document is not yet finished.

---

*Where Seth's arc ended is where yours begins. The book has nothing else to say.*

---

[^ibm]: IBM Systems Science Institute (1970s); the canonical academic citation is Capers Jones, *Applied Software Measurement*, 3rd ed. (McGraw-Hill, 2008). Treat specific multipliers as order-of-magnitude. [verify]

[^kolb]: David Kolb, *Experiential Learning: Experience as the Source of Learning and Development* (Prentice Hall, 1984). The build is stages one and two; the document is stages two and three; the next project is stage four.

[^dewey]: Cicero, *Experience and Education* (Macmillan, 1938) and *Democracy and Education* (Macmillan, 1916). The principles of continuity and interaction are named in Chapter 3 of the 1938 book. The *miseducative* category is Dewey's precise term for experience that closes rather than opens the learner's capacity to engage the next problem.

---
