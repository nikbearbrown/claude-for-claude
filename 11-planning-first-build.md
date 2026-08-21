# Chapter 11 — Planning Your First Boondoggled Build

*Before Claude sees a single prompt, you know exactly what you are building, why, and which steps belong to you.*

---

The first time I ran a fully Boondoggled build from end to end, I sat at my desk for forty minutes and did not type a single word into Claude.

The editor was open. The project folder existed. The cursor blinked in the input box. I did not type.

What I wrote instead was one sentence in a markdown file. *"A web page that lets me add, mark submitted, and delete college applications for this admissions cycle, and remembers them across browser refreshes."* I read it. I read it again. I deleted *this admissions cycle* and put it back. I deleted *across browser refreshes* and put it back. And then I sat with the sentence for a minute and made sure it was the project — the whole project, no smaller, no larger.

This is the part of the discipline that is hardest to teach. Forty minutes of sitting with one sentence does not feel like work. It is the most important work of the build. While I was sitting with the sentence, something changed in my head. My brain stopped narrating what I would type into Claude and started thinking about *the problem*. Whether *application* meant only the Common App or each supplement separately. Whether *delete* needed an undo, given that I had a record of which schools I was abandoning. What I would feel like, at 11 p.m. on a Sunday in November, opening this page to figure out which fee waivers were still pending. The narrator composing prompts had been replaced, briefly, by a person actually thinking about what they were building.

That replacement is the chapter.

Everything that follows — the SDD, the Boondoggle Score, the Minion Brief, the execution log — is mechanical. The mechanics exist to *cause* the cognitive shift that happened at minute forty, when I realized I had not been thinking about Claude for half an hour. This chapter is where you make that shift happen on purpose, before the build begins, every time.

![Five artifact boxes — /v0, /v1, SDD, Score, Minion Brief — connected by four labeled gates G1 through G4. Each gate names what must be true before it opens. The Minion Brief is highlighted in red as the gate that releases the first prompt.](images/11-planning-first-build-fig-01.png)
*Figure 11.1 — The planning sequence: five artifacts, four gates*

---

## Five Artifacts, Four Gates

The planning sequence has five documents and four gates between them. The documents are evidence the gates were honored. The gates are the actual discipline.

**/v0** is one sentence. One user. One outcome. No conjunctions hiding a second project. *"An application tracker that's also a scholarship database and an essay editor"* is three projects wearing one sentence as a disguise. *"An application tracker that lets me add, mark submitted, and delete college applications and remembers them across refreshes"* is one project — the *and* connects operations on the same data, not separate systems. The /v0 gate catches the difference. If you cannot say what you are building in one sentence, you do not yet know what you are building, and the next four documents will paper over the not-knowing in increasingly elaborate ways.

**/v1** is two short lists: *in scope* and *out of scope*. Both must be non-empty. The out-of-scope list is load-bearing. A /v1 without one is a /v1 that will quietly grow into something else under prompting pressure. The most painful items to write are the features you actually want but are cutting. Write them anyway. They are the ones that will try to slip back in at prompt nine when Claude offers to add them "since we're touching that anyway," and their presence on the list is the only thing that keeps the answer to that offer to *no*.

**The SDD** has five sections: data model, UI states, persistence, failure modes, and out-of-scope restated. Five sections, each with at least one concrete artifact named — a file path, a data shape, a state transition, a named failure trigger. Sections that say *"we will figure out the data model later"* do not pass Gate 3. Sections that say *"one `Application` interface in `types.ts` with `id: string`, `school: string`, `program: string`, `submitted: boolean`"* do. The SDD is the document Claude will work from. It is also the document you will defend against Claude. When Claude proposes a structure that doesn't match the data model section, the data model section wins.

**The Boondoggle Score** is a table with one row per step in Phase 1. Five columns: step number, phase, labor (Claude or human), supervisory capacity if the labor is human, handoff condition. The handoff condition is what must be true to call the step done. Not "Claude completes the function" — that is delegated work, not planned work. The handoff condition for a function is the name of the test file, the number of cases, and the inputs those cases cover. The condition is machine-checkable, not judgment-dependent.

**The Minion Brief** is the per-Claude-step prompt specification — for each row in the Score where labor is Claude, the brief names what Claude needs to know to execute the step. Chapter 8's five-element format, applied row by row. You write the brief from the score. The brief is what you paste into Claude when execution begins.

The four gates between these artifacts:

**Gate 1 (/v0 → /v1).** One sentence, one user, one outcome. Check every *and* — is it connecting operations on the same data, or hiding a second project?

**Gate 2 (/v1 → SDD).** Both lists are non-empty. The out-of-scope list contains things you actually want.

**Gate 3 (SDD → Score).** Every section names at least one concrete artifact. No section defers.

**Gate 4 (Score → Minion Brief).** Every row has a sharp handoff condition. No row says *"Claude figures it out."*

When all four gates are satisfied, the planning gate is open and the first prompt is permitted. This is the rule. The rest of the chapter argues for it and shows it in practice.

---

## Scope

A first Boondoggled build should be smaller than the project you think you want to do. This is the most reliable advice in this book and the advice students most reliably ignore.

The reason is mechanical. Boondoggling has per-step overhead that ungoverned vibe-coding does not have. If you choose a project at the upper limit of what you could build without the discipline, you will hit the overhead and quit. If you choose a project at the lower limit — something you could already build in a weekend — you will see the discipline produce a build that is cleaner than your unaided weekend work, and you will keep going.

Three scoping constraints that hold at student scale. First: one user, one device, one session. Your first Boondoggled project has one user (you), runs on one device, and is used in one session at a time. Multi-user, multi-device, and concurrency are three of the hardest problems in software. Each of them is out of scope. Second: no external dependencies you haven't used before. If you have never touched a Postgres database, the first Boondoggled build is not the one that introduces it. Use the storage primitive you already know. Claude can write Postgres code. The question is not whether Claude can. The question is whether *you* can supervise it. Supervisory capacity is the binding constraint, not technical capacity. Third: one named user need. The /v0 sentence names one need and resists the second. The most painful out-of-scope items to write are the ones you actually want. They are also the reason the project will finish.

---

## What the Score Tells You Before You Write a Line

The Boondoggle Score is not a checklist. It is a diagnostic. A honestly produced score tells you three things by inspection, before you have touched the keyboard in anger.

| # | Phase | Labor | Supervisory capacity | Handoff condition |
|---|---|---|---|---|
| 1 | Phase 1 — Data model | **Human** | Problem Formulation | `Application` interface written: `{ id, school, program, submitted: boolean, addedAt }`. Three sample objects round-trip through `JSON.parse(JSON.stringify(...))` without loss. |
| 2 | Phase 1 — Page scaffold | **Claude** | — | `npm run dev` serves `index.html`. Page renders empty list, an input, and an Add button. No console errors. |
| 3 | Phase 1 — Mutations | **Claude** | — | `addApplication`, `toggleSubmitted`, `deleteApplication` exported; unit tests for each pass on arrays of length 0, 1, and 3. |
| 4 | Phase 1 — Visual hierarchy | **Human** | Interpretive Judgment | Submitted applications render with strikethrough below unsubmitted ones, in insertion order. Verified by eye against the SDD `at-a-glance` need. |
| 5 | Phase 1 — Persistence | **Claude** | — | After ten toggles plus a browser refresh, the rendered list matches the pre-refresh state byte-for-byte. Malformed `localStorage` triggers the confirmation dialog. |

*The critical path* is the sequence of rows where each handoff condition is a precondition for the next row's labor. In the application tracker Score below, rows 1 → 2 → 3 are critical: the data model must exist before the page scaffolds against it, and the page must exist before the mutation functions can be tested in it. Rows 4 and 5 are parallel paths off the spine. A critical-path row that fails its handoff condition stops the build. A parallel row that fails flags a branch.

*The highest-risk handoffs* are the rows where the cost of a missed condition is largest. Row 1 is almost always the highest-risk row in any build, even if it is the smallest in lines of code, because it is the row every other row inherits from. A wrong data model in row 1 produces wrong UI in row 2, wrong mutations in row 3, wrong persistence in row 5, and visual hierarchy in row 4 designed against a wrong model. What looks like one mistake at row 1 is five mistakes by the end.

*The supervisory distribution* is the third thing. Look down the Labor column, then at the Supervisory capacity column for the human rows. A healthy Phase 1 distribution has at least two distinct named capacities across the human rows, and the highest-risk row is human. A score where every human row says "domain knowledge" is making you do the same thinking in five places. A score where every human row says "Claude figures it out" is not a Boondoggled score — it's a delegated one.

---

## When the Score Catches a Thin SDD Section

The most common early diagnostic, and the one that feels like failure the first time: you finish the SDD, start writing the Score, and at some row you cannot write a clean handoff condition. The condition you write keeps being vague. *"The function works."* *"The data is saved."* *"The page looks right."* You can hear, in those words, that you do not know what *works* means for this row.

The Score is telling you a section of the SDD is thin. *"The function works"* is what you write when the failure modes section was thin. *"The data is saved"* is what you write when persistence was thin. *"The page looks right"* is what you write when UI states never named the states.

The response is not to write a better handoff condition. The response is to walk back through Gate 3, find the thin section, and thicken it — which means naming a concrete artifact where there was prose. *"The data is saved"* becomes *"after every mutation, the full `Application[]` is written to `localStorage['applications-v1']` as JSON; on load, malformed JSON triggers a confirmation dialog and a reset."* Now the handoff condition can be sharp: *"refresh preserves state across 10 toggles; corrupt JSON triggers reset-with-confirm dialog."* Now it is testable.

The Score cannot lie. You can write a vague SDD section and convince yourself it is specific. You cannot write a vague handoff condition without hearing the vagueness. The Score is the SDD's lie detector.

---

## The Planning Gate

The planning gate is the moment you decide planning is complete and the first prompt is permitted. It is the most important decision in the build, and almost every student gets it wrong in the same direction: they open it early, because planning is slow and execution is fast, and the asymmetry is uncomfortable.

The gate opens when all four sub-gates are satisfied. The informal fifth test is this: you have not thought about Claude for the last ten minutes. You have been thinking about the problem — the user, the data, the failure modes, the states — and Claude has briefly disappeared from your mental workspace. That disappearance is the shift the planning phase exists to cause. When you notice it, the gate is open.

The economic argument for the gate is the IBM Systems Science Institute defect-cost curve: a defect caught in design costs roughly 1×, in implementation roughly 6×, in production 30–100×.[^ibm] Boondoggling's version of the argument is that AI tools invert the usual assumption behind this curve. Traditionally, the cost of *producing* wrong code is roughly proportional to the cost of the programmer's time. With Claude, producing wrong code is cheap — it takes eleven seconds to generate a 400-line file. The cost of catching wrong code late is therefore no longer bounded by the cost of producing it. The curve steepens. The gate becomes more valuable, not less, as the tool gets faster.

Fred Brooks made the deeper case in 1986: software's essential difficulties — complexity, conformity, changeability, invisibility — are not reduced by faster tools.[^brooks] Faster tools reduce *accidental* difficulty. The planning phase is where you work on the essential difficulties. The execution phase is where you and Claude work on the accidental. A student who skips planning is not working faster. They are working entirely on accident.

The planning gate is also now the dominant architectural pattern in production agentic systems. Yao and colleagues' ReAct framework operationalized interleaved planning and acting at the model level (Yao et al., 2022). The 2025 survey of plan-then-execute implementations across LangGraph, CrewAI, and AutoGen confirms the split is a structural assumption of production agents, not a stylistic preference.[^pte] Claude Code's plan mode is the same distinction surfaced as a UI affordance. The Boondoggle Score is the human-side of this architecture: the reasoning you write down before Claude is permitted to act.

One more note, for the case of an existing codebase. If your first Boondoggled build enters a project that already exists — a club repo, a fork, a previous semester's code — the planning sequence has a prequel. Before writing the /v0 sentence, you spend at least one session asking Claude questions about the codebase. No edits. Questions only: what does this project do, how is it organized, where does state live, what does the user actually do with the running system. Write down the answers in your own words. When you can describe the existing project accurately, you can write a /v0 sentence that is honest about what is being added to what. This codebase Q&A session is the gate before the gate.

---

## The Application Tracker Plan

Here is the complete planning document for the build executed in Chapter 12. It fits on roughly one printed page. Two hours to write, most of them spent on the out-of-scope list and the failure modes — the two sections students most want to skip, and which catch the most bugs.

---

**`v0.md`**

> A web page that lets me add, mark submitted, and delete college applications for this admissions cycle, and remembers them across browser refreshes.

*Gate 1 check:* One sentence, one user (*me*), one outcome (*track this cycle's applications across refreshes*). The *and* connects three operations on the same data — one project. Gate 1 open.

---

**`v1.md`**

*In scope:* add an application (school name + program name), toggle submitted/not submitted, delete an application, persist across refresh on the same device, keyboard operation.

*Out of scope:* per-school supplemental essay drafts; recommender status tracking; fee-waiver request tracking; scholarship deadlines; sync across devices; login, accounts; tags, priorities, sorting; mobile-specific layout; editing school or program text (delete and re-add instead).

*Gate 2 check:* Out-of-scope list has six items I actually want. Gate 2 open.

---

**`sdd.md`**

*Data model.* One `Application` interface in `types.ts`:

```ts
interface Application {
  id: string;        // crypto.randomUUID()
  school: string;    // 1..120 chars, trimmed
  program: string;   // 1..120 chars, trimmed
  submitted: boolean; // default false
}
```

Applications live in memory as `Application[]`, in insertion order, never resorted.

*UI states.* Three: *empty* (no applications; placeholder line), *populated* (one or more applications; list and input visible), *all-submitted* (all applications `submitted: true`; list plus a quiet "all submitted for this cycle"). No loading state — `localStorage` reads are synchronous.

*Persistence.* On every mutation (`addApplication`, `toggleSubmitted`, `deleteApplication`), serialize the full `Application[]` to `localStorage['applications-v1']` as JSON. On page load, read and attempt `JSON.parse`. On parse failure, show a confirmation dialog (*"Saved applications are corrupted. Reset?"*); confirm clears the key and starts empty; cancel leaves the key and shows an empty list for this session.

*Failure modes.* (1) Corrupt JSON — handled above. (2) `localStorage` unavailable (private browsing) — in-memory only; one-line banner: *"Applications won't persist in this window."* (3) School or program text over 120 chars — reject, keep focus in input, no toast.

*Out of scope, restated.* No editing of school or program text, no per-school supplements, no sorting beyond insertion order.

*Gate 3 check:* Five sections, each with at least one named concrete artifact. Gate 3 open.

---

**Boondoggle Score — Phase 1**

| # | Phase | Step | Labor | Supervisory capacity | Handoff condition |
|---|---|---|---|---|---|
| 1 | Phase 1 | Define the `Application` data model | Human | Domain knowledge: what fields this app needs | Written as one TypeScript interface in `types.ts` before any UI code |
| 2 | Phase 1 | Scaffold the HTML page | Claude | — | Page loads in browser with empty root; no console errors |
| 3 | Phase 1 | Implement `addApplication`, `toggleSubmitted`, `deleteApplication` | Claude | — | Three unit tests pass on `Application[]` of length 0, 1, 3 |
| 4 | Phase 1 | Decide visual hierarchy | Human | Aesthetic judgment: what this app should feel like | Three CSS variables (`--font`, `--primary`, `--accent`) named in `style.css` before Claude writes styles |
| 5 | Phase 1 | Wire `localStorage` with corruption guard | Claude | — | Refresh preserves state across 10 toggles; corrupt JSON triggers reset-with-confirm dialog |

*Gate 4 check:* Five rows. Two human, three Claude. Human rows cover two distinct supervisory capacities. The highest-risk row (row 1, data model) is human. Every handoff condition is testable. No row says "Claude figures it out." Gate 4 open.

---

The document is short on purpose. A student SDD is not an industrial SDD. The minimum viable specification is the specification that admits no ambiguous handoff and no thinner. The planning document does not just plan the build. It *defends* the build — against Claude's helpful suggestions and against the part of your own brain that wants to add one more thing at prompt nine. When Claude proposes a per-school essay tracker "since we're touching the data model anyway," the answer is on this page. When Claude proposes a loading spinner because `localStorage` is "async," the answer is on this page.

The research grounding for this form is in the project-based learning literature. Chen and Wang's 2019 meta-analysis finds student-led written planning among the strongest moderators of project quality. Larmer, Mergendoller, and Boss name the Project Planning Form a non-negotiable of gold-standard PBL.[^pbl] Boondoggling adds the labor column and the handoff condition that PBL planning forms do not have, because those forms were written before the labor question had a language model in it.

---

## 🕰️ AI Wayback Machine

**Grace Hopper** (1936–2022). British-Austrian-American architect and design theorist. *A Pattern Language* (Oxford University Press, 1977), co-authored with Sara Ishikawa and Murray Silverstein, presented 253 design patterns, each structured in the same three movements: problem statement, discussion, core solution. The book physically refuses to present a solution until the problem has been named in standalone form. *The Timeless Way of Building* (1979) is the theoretical companion: design begins with what the inhabitant needs, not with what the builder wants to make. Alexander called the goal *the quality without a name* — the quality of fit between problem and form. The SDD is Alexander's structure applied to software: problem first, in writing, before any solution. The /v0 gate is the pattern-language gate. Forty-nine years before this chapter, an architect wrote the rule this chapter enforces.

**

![Grace Hopper, 1980s. AI-generated portrait based on a public domain photograph.](../images/grace-hopper.jpg)
*Grace Hopper, 1980s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:**

> Read *A Pattern Language* (Alexander, Ishikawa, and Silverstein, 1977) — any single pattern, your choice. Quote the pattern's three movements (problem statement, discussion, core solution) in under 200 words. Then write the equivalent three movements for a single feature of your own planned project. Where does Alexander's structure refuse to compress? Where does it map cleanly onto the SDD's sections? Where does it suggest something your SDD is missing?

---

The plan is complete. Chapter 12 executes it.

---

[^ibm]: The widely circulated curve originates with IBM Systems Science Institute (1970s); the canonical academic citation is Capers Jones, *Applied Software Measurement*, 3rd ed. (McGraw-Hill, 2008). Treat the specific multipliers as order-of-magnitude rather than precise. [verify]

[^brooks]: Frederick P. Brooks Jr., "No Silver Bullet — Essence and Accident in Software Engineering," *Proceedings of the IFIP Tenth World Computing Conference* (1986); reprinted in *The Mythical Man-Month*, Anniversary Edition (Addison-Wesley, 1995).

[^pte]: *Architecting Resilient LLM Agents: A Guide to Secure Plan-then-Execute Implementations*, arXiv:2509.08646 (2025).

[^pbl]: Yan Chen and Jane Wang, "Revisiting the effects of project-based learning on students' academic achievement: A meta-analysis investigating moderators," *Educational Research Review* 26 (2019); John Larmer, John Mergendoller, and Suzie Boss, *Setting the Standard for Project Based Learning* (ASCD / Buck Institute, 2015).

---
