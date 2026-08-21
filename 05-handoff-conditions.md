# Chapter 4 — Handoff Conditions: The Gate Between Steps


## TL;DR

- This chapter gives a working overview of Handoff Conditions: The Gate Between Steps, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What a handoff condition is, Strong vs. weak: the worked table, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> Not "looks good." A specific, testable condition that must be true before the next step begins. This is what separates conducting from approving.

---

## Learning outcomes

1. **(Understand)** Define a handoff condition and explain why "looks good" fails as one.
2. **(Apply)** Write handoff conditions for three steps in the class-website build.
3. **(Analyze)** Identify the dangerous middle — steps where Claude's output requires specific verification not in the obvious checklist.

---

## Opening

The teacher reviewed Claude's output. The new About page rendered. The CSS looked right. The navigation included the new link. The teacher approved the commit and pushed.

Three days later, a student emailed: *the link to the syllabus from the About page does not work.*

The teacher checked. The About page existed. The link existed. It pointed to `syllabus.html`. The file lived at `src/syllabus.html`. The link path was correct *relative to the project root* — and wrong *relative to where the page was being served*. The school server served pages from `src/`; the About page was at `/about.html`; the link should have been `/syllabus.html`. The page-relative link had been `syllabus.html` (no leading slash). On the teacher's local dev server, the relative path worked. On the school server with its outdated mod_rewrite, it did not.

The teacher had reviewed the output. It looked correct. They approved. The handoff condition they did not write was the condition that would have caught this: *every link on the new page resolves on the actual deployment, not just on local dev.*

This chapter is about writing that condition before the dangerous middle catches you.

Act One closes here. The teacher who finishes Chapter 4 has CLAUDE.md (Chapter 2), specifications (Chapter 3), and handoff conditions. Together, those three artifacts form the conducting discipline at its smallest scale. Gate 1 — the bar that opens Act Two — is met when the teacher has one deployable page built with all three artifacts in place, plus one classroom activity designed for each.

![The handoff condition gate](images/05-handoff-conditions-fig-01.png)
*Figure 5.1 — The handoff condition gate*

---

## What a handoff condition is

A handoff condition is the answer to one question: *what must be true after this step before the next step begins?*

Three properties make a handoff condition load-bearing.

**Specific.** Names the artifact and the property of the artifact. Not *"the page works."* — *"the page renders correctly at 375px mobile width with no horizontal scroll."* Specific to which page, which property, which threshold.

**Testable.** Can be verified by a command, a visual check with explicit criteria, or a comparison with a known-good reference. If the only verification is *"squint and feel okay,"* the condition is a feeling, not a handoff condition.

**Binary.** Pass or fail. The next step depends on the condition; either it is met or it is not. *"Mostly works"* is not a condition.

Exit codes are not handoff conditions. Claude reporting *"done"* is not a handoff condition. A diff that looks reasonable is not a handoff condition. The handoff condition is the specific test that, if it fails, means the build step is not done — and that triggers the revert-and-respecify discipline below.

---

## Strong vs. weak: the worked table

For the class-website build's three main step types:

| Step | Weak condition | Strong condition |
|---|---|---|
| Add a new page | "looks good"; "renders" | Page renders at 375px mobile width with no horizontal scroll; every link on the page resolves to a real file at the expected path (run `npm run lint-html`); the new page is linked from `src/index.html`'s nav; no existing files outside `src/index.html` and the new page were modified |
| Change CSS | "no errors"; "looks better" | All previously-rendered pages still render identically on three representative pages (compare screenshots); the change appears only where intended; `npm run lint-html` exits 0; the change respects the CLAUDE.md no-Tailwind rule |
| Deploy to server | "rsync exits 0" | The live site at the class URL serves the current git SHA (curl + grep); the new page is reachable at its expected URL with 200 status; no 404s in the broken-link sweep; the school server's specific path conventions (trailing `.html`, no trailing slash) are respected |

Read the two columns. The weak column is what most teachers write when they write conditions at all. The strong column is what would have caught the syllabus-link bug from the opening.

Every strong condition has the same shape: a named artifact, a measurable property, a threshold or expected value. The discipline of writing them is the discipline of forcing your intent into testable form.

---

## How to write one: five questions

Five questions, asked in order, in the moment you would otherwise be approving the build step.

**1. What did the previous step claim to do?** Read the specification you wrote in Chapter 3. The handoff condition is anchored to the operation, output format, and negative constraint elements.

**2. What artifact would prove it?** A page exists at a path. A file's contents match expectations. A test runner exits 0. A screenshot at a target resolution looks right. The artifact is the *evidence* the step did what it claimed.

**3. How would you verify the artifact?** Concretely — a command, a visual comparison with explicit criteria, a grep, an HTTP response check. If you cannot name the verification in one or two sentences, the condition is probably still vague.

**4. What is the threshold?** Exact match where the spec was exact. A tolerance where the spec allows variation. *"Mobile renders correctly"* is too vague — *"renders at 375px width with no horizontal scroll on a Chrome devtools iPhone-12 preset"* is checkable.

**5. What does failure look like?** Not "an error." Specifically: the operational signature of the failure mode you are protecting against. *"If the lint reports broken links, the cause is probably a page-relative link path that does not match the school server's URL handling."* You can predict the failure's root cause from the condition's failure signature.

For the syllabus-link bug:

1. *Claim of step?* Add an About page; do not modify other files.
2. *Artifact?* The new `src/about.html` and one nav-link change in `src/index.html`.
3. *Verification?* Run `npm run lint-html` against the new file *and* against the rendered live site after deploy.
4. *Threshold?* Lint exits 0; every link 200s on the deployment.
5. *Failure signature?* If a link 404s, the cause is probably a relative path that resolved on local dev and not on the school server.

The five questions take 60–90 seconds per handoff condition. The bug they catch costs hours.

---

## Revert, do not correct forward: `/rewind`

The hardest discipline when a handoff condition fails: do not write a follow-up prompt to fix the result.

The instinct is to ask Claude to correct the failed step. *"The link is broken; fix it."* Claude composes a correction. Maybe it works. Maybe it produces a second wrong fix on top of the first wrong execution. Either way, the session's context now contains the failed step, the failure observation, the attempted correction, and Claude's reasoning across all of them. The context is *polluted* — the next prompt operates against the failure history as if the failure is part of the desired result.

The discipline is **revert-and-respecify**, made operational in Claude Code by the `/rewind` command (or pressing Esc twice).

1. **`/rewind`.** Select the checkpoint *before* the failed step. Claude restores both the conversation context and the file changes to that point. The failed step is gone — from both Claude's memory and your file system.

2. **Respecify.** Rewrite the original specification with the failure mode as an explicit negative constraint or as a new positive requirement. For the syllabus-link bug: *"All link paths in the new page must use absolute server-relative paths (leading `/`); never use page-relative paths that depend on the page's own location."*

3. **Re-run from clean state.** Submit the revised specification. Claude executes against the corrected spec; the failure mode is now an explicit constraint; the new execution avoids it.

4. **Re-verify.** Run the handoff condition again. If it still fails, the specification is still wrong. `/rewind` again; tighten again.

**After two failed corrections on the same step**, `/clear` the session entirely and rewrite the specification from scratch with the failures named as constraints. Two failures is the signal that the context is polluted beyond what `/rewind` alone can clean. Start fresh.

The cost of `/rewind` feels high in the moment. It is consistently lower than the cost of continuing with forward correction. The compounding from polluted context is what most often turns a 15-minute bug into a 90-minute one.

---

## Some conditions require human review

Not every handoff condition can be a command. Some require human judgment — *"the table is laid out in a visually balanced way,"* *"the tone of the feedback is appropriate for a tenth-grader."* These are real handoff conditions; they cannot be automated.

The discipline for non-automatable conditions: name *which human, at what point*, performs the review. For your own builds, that is you. For classroom work, that may be a peer reviewer. For high-stakes builds (the grading tool in Act Two, the simulation in Act Three), it may be you plus a colleague.

A *STOP block* (foreshadowed for Chapter 7's Hooks) is the operational form. The build pauses; Claude reports the artifact and the condition that requires human review; the human reviews and signals continuation. The pause is not optional — Claude does not proceed until the human signals. For visual judgment, this is the only safe pattern.

For Chapter 4, the introduction is conceptual: some handoff conditions require human review and the conducting discipline is the practice of *naming the review*, not approving without it. Chapter 7 makes the STOP block operational with hooks.

---

## Teacher build: handoff conditions for your class-website pages

Apply the chapter's discipline to your project.

Pick three steps from your class-website build. For each, write a handoff condition in a one-sentence form that names the artifact, the verification method, and the threshold.

Then integrate the conditions into your build workflow:

1. **Before approving any build step**, evaluate the handoff condition. If automated (lint, test, command), run it. If visual, check explicitly against the named criteria.
2. **If the condition fails**, `/rewind` to before the step. Respecify with the failure as a negative constraint. Re-run.
3. **If you fail twice on the same step**, `/clear` and rewrite the specification from scratch with both failures as constraints. The third attempt usually works.

Add one Lessons Learned entry to CLAUDE.md for any handoff condition that fired during this exercise. The lesson goes into the persistent context; the next session is faster.

---

## Classroom activity: students design handoff conditions

For your students' classroom:

Hand students a multi-step build sequence — extracting a CSV, processing the rows, writing a summary. Five steps. Each student writes a handoff condition for each step.

Their constraints:

- Each condition is specific, testable, binary.
- At least one condition uses an automated check (a command, a lint, a test).
- At least one condition includes a human-review pause for a property that cannot be automated.

Pair students. Each peer attempts to *break* the partner's build sequence — corrupted CSV, extra rows, unexpected column — and observes which handoff conditions catch the break and which do not. Rewrite the conditions that did not catch.

The pedagogical move: students learn that handoff conditions are *not exhaustive* — they protect against the failure modes you thought of. The peer attack surfaces the failures they did not think of. Two iterations of the drill usually produce conditions strong enough to catch most reasonable attacks.

This activity maps to the student book's Chapter 9 (Handoff Conditions). Your design here is the worked example.

---

## Gate 1: Act One acceptance

The teacher closes Act One with:

1. **CLAUDE.md exists for the class-website project.** Refined from `/init`; under 200 lines; specific to the project; never-rules included.
2. **One deployable page** built through Explore → Plan → Implement → Commit with a five-element specification (Chapter 3).
3. **Three handoff conditions** written, each specific/testable/binary, integrated into the build workflow.
4. **One classroom activity per Act One concept** designed: terminal session (Chapter 1), CLAUDE.md (Chapter 2), specifications (Chapter 3), handoff conditions (Chapter 4). Four activities. Each maps to its student-book counterpart.

If all four are true: Gate 1 is met. Act Two begins.

If any are not true: the subsequent chapters compound. The grading tool in Act Two assumes the discipline is in place. Going forward without Gate 1 closed produces the same failure mode the chapters warn against.

---

## Common misconceptions

**"Looks good is a condition."** No. It is a feeling. Conditions are specific, testable, binary.

**"Forward correction is fine."** Sometimes. Often it pollutes the context. The two-failure rule: after two failed corrections on the same step, `/clear` and start fresh with the failures as negative constraints.

**"/rewind is for failures."** Also for *I want to try a different approach without polluting the context.* `/rewind` is the cheapest way to explore alternatives.

**"Handoff conditions are for big builds."** For any step whose failure is expensive to discover late. A `<p>` tag addition does not need one. A deploy to the school server does. The criterion is recovery cost.

**"My grading scripts already work; I do not need handoff conditions."** The grading tool in Act Two is where handoff conditions matter most. Your future grading tool fails in ways your current script's exit-0 will not catch. Chapter 8 is the chapter where this becomes vivid.

---

## Exercises

1. **(Apply)** For each of the three steps in your class-website build, write a handoff condition that is specific, testable, and binary. Integrate into the workflow.

2. **(Analyze)** Identify one step in your build where the handoff condition cannot be fully automated. Name what the human review is checking; name which human reviews and when.

3. **(Create)** Design a revert-and-respecify drill for your classroom. The drill must include: an intentionally broken specification, the handoff condition that catches the break, the `/rewind` and respecification step. Sketch in one paragraph.

---

## What would change my mind

The chapter's strong operational claim is that **explicit handoff conditions catch a meaningful share of failures that exit-0 verification misses** — and that **`/rewind` produces more durable fixes than forward correction**. If a controlled comparison found no measurable difference in build-failure rates between handoff-disciplined and ad-hoc verification, the chapter's discipline becomes optional. The chapter would still recommend it for the supervisory-practice benefit; the urgency softens.

I expect the difference to be substantial because the syllabus-link pattern from the opening — exit 0, lint passes locally, fails on deployment — is the modal failure mode in classroom-deployed builds. Handoff conditions are the specific defense.

---

## Still puzzling

- **The right number of handoff conditions per build.** Three for the class-website is the chapter's example; some builds need more, some fewer. Heuristic: one per step whose failure is expensive to discover late.

- **Visual handoff conditions at scale.** Per-page visual checks scale linearly with the site. Some teachers automate via Playwright screenshot diffing; some keep manual. The chapter's working answer: manual until the site is large enough to justify automation.

- **The transfer to other agentic tools.** `/rewind` is Claude Code-specific. Codex and `gh copilot` have analogous mechanisms (Codex `/rewind` or `/clear`; `gh copilot` requires manual git revert). The discipline transfers; the affordance differs.

---

## Links

- [Claude Code /rewind documentation](https://code.claude.com/docs) — the affordance that makes revert-and-respecify operational.
- [GitLab database incident post-mortem (2017)](https://about.gitlab.com/blog/2017/02/01/gitlab-dot-com-database-incident/) — canonical case for why exit 0 is not enough.

---

##  AI Wayback Machine
 **W. Edwards Deming** (1900–1993) — American statistician whose **Plan-Do-Check-Act** cycle, articulated across *Out of the Crisis* (1986) and his decades of work with Japanese manufacturers, made the case that **quality is built into a process through explicit verification at every step, not inspected in at the end**.[^1] Deming's argument, against the dominant practice of his era, was that final inspection was the most expensive place to catch failure. The cheapest place to catch failure was at every handoff in the production sequence — the place where the work crossed from one stage to the next.

![W. Edwards Deming](../images/w-edwards-deming-efb.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

The handoff condition is PDCA at command granularity. Plan: the specification (Chapter 3). Do: the Explore → Plan → Implement → Commit workflow. Check: the handoff condition. Act: `/rewind` and respecify (or proceed) based on what the check revealed. The four phases map directly. Deming wrote about manufacturing lines; the conducting discipline is the same shape at terminal scale.

Deming's most quoted line — *"A bad system will beat a good person every time"* — is the argument for Chapter 7's Hooks foreshadowed here. The system is what enforces the discipline when the practitioner is tired, distracted, or under deadline pressure. The handoff condition is the discipline; the hook is the system that makes the discipline non-optional. Both come from Deming's insistence that quality is *built in*, not heroically enforced by individuals.

Deming's Toyota collaborators extended the principle with the **andon-cord** — the right of any worker on the line to stop production at any abnormality, without permission, without consequence. The teacher who runs `/rewind` on a failed handoff is pulling the andon-cord. The cost of stopping the build at the failed step is one of the cheapest costs in the system. Deming's framework formalizes why.

---

## Bridge

Act One is closed. The teacher has the framework: CLAUDE.md, specifications, handoff conditions, the revert-and-respecify discipline. Chapter 5 opens Act Two by naming what the teacher has been doing — the five supervisory capacities the discipline exercises.

---

[^1]: Deming, W. E. *Out of the Crisis*. MIT Press, 1986; reissued 2018. PDCA is articulated across his work; the 1986 book is the standard reference.
