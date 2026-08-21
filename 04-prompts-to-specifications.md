# Chapter 3 — From Prompts to Specifications: The First Build


## TL;DR

- This chapter gives a working overview of From Prompts to Specifications: The First Build, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, The five elements, Prompt vs. specification: the worked table, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> "Add a page to my class website" is a request. A specification names the file, the structure, what it must not touch, and what done looks like. The class website is the first build.

---

## Learning outcomes

1. **(Apply)** Write a five-element specification prompt for a Claude Code task.
2. **(Apply)** Execute a build step using Explore → Plan → Implement → Commit with Ctrl+G plan editing.
3. **(Analyze)** Identify the difference between a prompt that delegates scope decisions and a specification that conducts.

---

## Opening

The pebble.

Not the whole class website. One page. The course's syllabus page — units, dates, topics, links to lesson materials. Static HTML. Linked from the home page's navigation. Nothing fancy.

The teacher had two ways to ask.

The first way: *"Add a syllabus page to my class website."*

The second way: a five-element specification that took 90 seconds to write and that specified exactly which file would be created, which existing files must not change, which CLAUDE.md rules govern the page, what the finished page looks like, and what Claude must not do along the way.

Both prompts got Claude to produce a syllabus page. The first produced a generic syllabus page with assumptions Claude defaulted to — a JavaScript-rendered table, a CDN-loaded font, a section structure that did not match the teacher's actual course outline. The second produced the page the teacher had in mind, sitting in the right file, linked from the right nav, with the head template the rest of the site uses.

The first prompt took 8 seconds to write. The build that followed took 45 minutes of correction.

The second prompt took 90 seconds to write. The build that followed took 12 minutes and produced the page that was wanted.

This is the chapter. The 90 seconds is the conducting work. The 45 minutes is what happens when you skip it.

![The Explore → Plan → Implement → Commit](images/04-prompts-to-specifications-fig-01.png)
*Figure 4.1 — The Explore → Plan → Implement → Commit*

---

## The five elements

The format is not a template; it is a set of categories that reliably matter. A specification is a prompt that contains decisions across all five.

**1. Operation.** The specific verb and object. *Create the file `src/syllabus.html`.* Not *"add a syllabus page"* — that is a goal, not an operation. The operation names what file, what command, what change. If the operation cannot fit in one short clause, decompose into multiple specifications.

**2. Invariants.** What must not change. *No existing files modified. The existing CSS in `src/styles.css` is not touched. The home page's nav structure is preserved.* Invariants are the protection against scope creep — the implicit "while I'm here, let me also fix this" that the agentic loop is structurally tempted to do.

**3. Context.** Which CLAUDE.md sections govern this step. *Stack: vanilla HTML/CSS, no build step. Code style: semantic HTML5, two-space indent. Verification: `npm run lint-html`.* Even though CLAUDE.md is auto-loaded, restating the relevant sections in the prompt strengthens the constraint — Claude is more reliable when the constraint is in the immediate prompt than when it is in the always-loaded context. The restate is short (one or two lines) and it matters.

**4. Output format.** What done looks like. Specifics. *A complete `src/syllabus.html` with the course's units and dates as a semantic table. Linked from `src/index.html`'s nav. Renders correctly at 375px mobile width.* The output format gives Claude the success criterion and gives you the verification target.

**5. Negative constraint.** What Claude must not do. *No JavaScript. No new dependencies. No CSS changes. No new template files; use the existing head/nav inline.* The single most under-used element. The negative constraint catches the dangerous middle most often because it names exactly the failure mode you are protecting against.

The five elements are 100–200 words for a typical class-website task. The first time you write one, it takes 3–5 minutes. By the tenth, it takes 60–90 seconds.

---

## Prompt vs. specification: the worked table

For the syllabus page:

| Element | Request prompt | Specification |
|---|---|---|
| Operation | "Add a syllabus page" | "Create the file `src/syllabus.html`" |
| Invariants | (implicit) | "No existing files modified; `styles.css` not changed; `index.html` only modified to add a nav link" |
| Context | (none) | "Per CLAUDE.md: vanilla HTML5, semantic markup, no build step; verification via `npm run lint-html`" |
| Output format | (implicit: "a page") | "Complete syllabus with unit names, dates, topics as a `<table>` with appropriate headers; nav link added between Home and Resources; renders at 375px mobile width" |
| Negative constraint | (none) | "No JavaScript. No new dependencies. No CSS rule additions. No CDN-loaded fonts." |

Read the two columns. The request prompt lets Claude make four decisions for you: file path (default invented), CSS approach (default Tailwind probably), JavaScript (default included), styling (default inline). Each default is wrong for the class-website project. The specification removes all four decisions from Claude's scope; Claude generates against the constraints; the output fits the project on the first attempt.

The 90 seconds of specification writing is the four decisions made by you instead of Claude. The decisions need to be made in any case; the question is *who makes them and when*.

---

## Explore → Plan → Implement → Commit

The workflow that operationalizes the specification. Four phases. Each maps to a Claude Code affordance.

**Phase 1 — Explore.** In plan mode (Shift+Tab twice). Claude reads files, identifies the relevant context, proposes the plan. You read the proposal. No file changes happen.

**Phase 2 — Plan.** Ctrl+G opens the plan in your text editor. You edit it directly. Remove a step Claude proposed that you do not want. Add a step Claude missed. Change a file path. Tighten a constraint. Save and return to Claude Code. The plan is now what *you* approved.

**Phase 3 — Implement.** Switch out of plan mode. Claude executes the edited plan. The agentic loop runs — gather, act, verify — until the plan is complete or until a step fails its handoff condition (Chapter 4).

**Phase 4 — Commit.** Git commit with a message that captures the specification's intent. Not "added syllabus page" — *"Add `src/syllabus.html` (static HTML, no JS, links from index nav); per spec 2026-02-12."* The commit message becomes a searchable artifact when you return to the project months later.

The four-phase workflow is the small-scale instance of the conducting discipline. Every build step you do through Claude Code goes through these four phases. The discipline is the practice of not skipping a phase when the build looks routine.

---

## Why plan mode matters

The cheapest defense against the dangerous middle is plan mode.

Plan mode shows you what Claude would do *before* it does it. If Claude's plan reflects a wrong assumption — wrong file path, wrong CSS approach, wrong scope — the plan reveals it. You see the wrong assumption in 30 seconds of reading. You correct it (Ctrl+G; edit; save). The corrected plan executes. The wrong assumption never reaches the file system.

Without plan mode, the wrong assumption executes. Files are edited; dependencies are installed; the syllabus page sits in `pages/` instead of `src/`. You catch the wrong assumption when you read the diff or run the test — 5–15 minutes later, with more work to undo. The undo includes a `/rewind` (Chapter 4), a respecification, and a re-run. Cost: 20–40 minutes.

The 30 seconds in plan mode is the protection. Use plan mode for any operation whose failure would cost more than 30 seconds to recover from. For the class-website builds, this is most operations.

There is one exception: routine operations where you already know what Claude will do and the cost of error is small. Adding a `<p>` tag to an existing paragraph. Renaming a single variable. For these, plan mode is overhead. The heuristic is recovery cost, not operation size.

---

## Give Claude a way to verify its own work

A pattern from Anthropic's own engineering guidance: when your specification includes a way for Claude to check its work, Claude iterates against the check and produces dramatically better results.

For the syllabus page, the checks:

- *"After implementation, run `npm run lint-html` against `src/syllabus.html` and fix any reported errors before reporting done."*
- *"Take a screenshot of the page at 375px width and verify the table is not horizontally scrolling; if it is, fix the CSS."*

Claude runs the lint. Fixes the errors. Runs the lint again. Reports done. The in-prompt verification is the chapter's bridge to Chapter 4's handoff condition discipline — the handoff condition is what *you* verify; the in-prompt verification is what *Claude* verifies before declaring done.

The pattern is not always available — some checks require human judgment that no command can produce. When the check is available, include it. Claude's iterative behavior against an automated check is one of the clearest cases where the agentic loop produces meaningfully better output than single-pass generation.

---

## Teacher build: the first deployable page

Apply the chapter's discipline to your class-website project.

1. **Compose the specification.** Five elements. 100–200 words. For the first page of your site that does not yet exist.

2. **Run in plan mode.** Shift+Tab twice. Submit the specification as your prompt. Read the plan.

3. **Edit the plan.** Ctrl+G. Make at least one change — a path, a constraint, a step. Save. Return.

4. **Switch out of plan mode and execute.** Watch the agentic loop run.

5. **Verify against the specification.** Did Claude produce what the output-format element described? Did it respect the negative constraints?

6. **Commit.** Descriptive commit message that captures the specification's intent.

7. **Note in CLAUDE.md.** If anything surprised you (Claude violated a CLAUDE.md rule despite the file being loaded; Claude defaulted to something you had to correct), add a Lessons Learned entry.

Total time for the first deployable page: 30–45 minutes. The page exists. The discipline is exercised. The CLAUDE.md is one entry richer.

---

## Classroom activity: students build their first deployable artifact

For your students' classroom:

Provide a starter project — a small website skeleton or a small data-processing script. Each student composes a five-element specification for one task: adding a page, adding a function, adding a feature. Their constraints:

- All five elements present and specific.
- The task is executed through Explore → Plan → Implement → Commit.
- The plan is edited via Ctrl+G before approval (mandatory; the activity teaches the editing move).
- Commit message captures the specification's intent.

Peer review the specifications, not the code. Each student reviews two peers' specs. The reviewer flags: any element that is vague, any negative constraint that is missing, any place where the operation is actually two operations that should be decomposed. Discussion.

The pedagogical move: students experience that the conducting discipline is the *specification work*, not the typing of the prompt. After two rounds of peer review, students write specifications that produce builds that work on the first attempt. The activity teaches by producing the first measurable improvement in their own builds.

This activity maps to the student book's Chapter 8 (Specifications). Your design here is the worked example.

---

## Common misconceptions

**"Specifications are for big builds."** Any operation that can fail silently deserves the format. The first page of the class website is small. The five-element specification still saves time on the first attempt.

**"More words = better specifications."** No. Each element specific is what matters, not total length. A 30-word specification with each element specific beats a 200-word one full of generalities.

**"I can just edit the generated code."** Sometimes. When the code is visibly wrong, yes. The dangerous middle is when the generated code is *not* visibly wrong but does not match what you meant. The specification prevents the failure mode at the source.

**"Plan mode slows me down."** It saves the 30 minutes of post-execution debugging more often than it costs the 30 seconds of plan review. The economics favor plan mode for any operation with non-trivial recovery cost.

**"Negative constraints feel paranoid."** They are not paranoia. They are the operational form of *"I have thought about how this could go wrong and named the specific failure I am protecting against."* The catch rate is high.

---

## Exercises

1. **(Apply)** Write a five-element specification for the next page of your class website. Run it through Explore → Plan → Implement → Commit. Edit the plan via Ctrl+G before approving.

2. **(Analyze)** Compare your specification to a one-line request prompt for the same task. Run the one-line prompt in a fresh session (after `/clear`). Document three behavioral differences in what Claude produced.

3. **(Create)** Design a classroom activity that teaches the five-element format *without using the word "specification"* (students learn the format from doing, not from being told). Sketch the activity in one paragraph.

---

## What would change my mind

The chapter's strong operational claim is that **the five-element specification format produces materially better Claude Code output** than free-form prompts. If a controlled comparison found no measurable difference between specification-driven and request-driven sessions, the format becomes a checklist rather than a load-bearing discipline. The chapter would still teach it for the supervisory-practice benefit; the urgency softens.

I expect the difference to be substantial because the five elements name the four decisions (file path, framework, framework opt-outs, scope) that Claude must otherwise make on default, and the defaults are reliably wrong for any project with environment quirks.

---

## Still puzzling

- **The right threshold below which specifications are overhead.** A one-character edit. A whitespace fix. Specifications are heavy for these. The heuristic: skip the format for operations whose entire description fits in one verifiable sentence and whose failure costs less than 30 seconds.

- **The transfer to other agentic-tool surfaces.** The five elements are framed for Claude Code. The same five (operation, invariants, context, output format, negative constraint) work for Codex and `gh copilot suggest`. Tool-agnostic at the conceptual level.

- **Whether plan mode should be the default.** Some teachers turn plan mode on permanently. Some never use it. The chapter's working answer: plan mode for any operation with non-trivial recovery cost, off otherwise. The line drifts with project familiarity.

---

## Links

- [Anthropic Claude Code best practices](https://code.claude.com/docs) — plan mode, prompt structure, the four-phase workflow.

---

##  AI Wayback Machine
 **George Pólya** (1887–1985) — Hungarian-American mathematician whose *How to Solve It* (1945) formalized problem-solving as a four-stage sequence: **understand the problem, devise a plan, carry out the plan, look back**.[^1] Pólya's argument was that mathematical (and by extension, all technical) problem-solving was not the spontaneous insight popular culture imagined it to be — it was a *teachable discipline* with predictable stages. The teacher's job, Pólya argued, was to *make the stages explicit* so that the student could exercise each one deliberately rather than skip to the answer.

The Explore → Plan → Implement → Commit workflow is Pólya's sequence applied to Claude Code. *Explore* is Pólya's *understand*: the teacher and Claude together read the project, identify the constraint, surface the assumption. *Plan* is Pólya's *devise*: the specification is composed, plan mode renders it, the teacher edits via Ctrl+G. *Implement* is Pólya's *carry out*: Claude executes; the agentic loop runs. *Commit* is Pólya's *look back*: the descriptive commit message captures what was decided and why, available to future-you when the project is revisited.

The five-element specification is the artifact form of Pólya's *plan* stage. The element-by-element discipline forces explicit decision at exactly the points where the request-form prompt allows the decision to default. Pólya's deeper claim — that the discipline of explicit-stages produces durable problem-solving capacity, not just one solved problem — is the chapter's claim about specifications: the practice of writing them makes the teacher better at *thinking* about builds, not just at producing them.

Pólya wrote eighty years before Claude Code. The framework holds without modification. The agentic loop is faster than any other mechanism for executing the carry-out stage; the slowness, when it appears, is always in the stages Pólya warned were the most important — understand and devise.

---

![George Pólya](../images/george-plya-977.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

## Bridge

The page is built. Chapter 4 names the gate between build steps — the handoff condition — and closes Act One.

---

[^1]: Pólya, G. *How to Solve It*. Princeton University Press, 1945. The Princeton Science Library 2014 reprint is the standard recent edition.
