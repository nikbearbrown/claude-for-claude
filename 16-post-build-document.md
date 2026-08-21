# Chapter 14 — Your Terminal Deliverable: The Post-Build Document


## TL;DR

- This chapter gives a working overview of Your Terminal Deliverable: The Post-Build Document, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What the post-build document is, The five sections, walked through, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> Three builds. A three-tier CLAUDE.md. The simulation running in your classroom. This chapter is the record of what you built, what you learned, and what you would do differently. Write it tonight.

---

## Learning outcomes

1. **(Create)** Produce a five-section post-build document for the interactive simulation you built across Act Three.
2. **(Evaluate)** Identify a specific decision in your build you would reverse with the benefit of hindsight.
3. **(Apply)** Use your CLAUDE.md and post-build document to plan your next conducted build.

---

## Opening

The simulation had run in front of three classes by the end of the week. The first class found two failures — the missing first-interaction affordance and the touch-event handling on Chromebooks. The teacher caught both with the revision-build cycle from Chapter 12; the Generation Log in PROJECT.md Layer 2 had three new entries by Friday.

By Friday afternoon, the simulation was reliable. The CLAUDE.md across all three builds had grown to 220 lines (over the 200-line guideline; the teacher made a note to prune in summer). The PROJECT.md for the simulation was complete — Layer 1 Intent unchanged since the night of writing; Layer 2 Technical State fully populated with the build's history. DESIGN.md was at its second revision (the screenshot-iterate sequence in Chapter 11 had added one constraint about minimum bar widths at mobile breakpoint).

The framework had produced what it was designed to produce: three working tools, an artifact of the discipline, a teacher who could trace every decision from the architecture to the classroom.

The teacher had one piece of work left.

The post-build document. Five sections. One page. Thirty minutes.

This chapter is short because the work is short. Most of the chapter is the template; the rest is the framing that makes the template worth filling out honestly. The book ends after this chapter. The next build starts there.

---

## What the post-build document is

The post-build document is the artifact that converts the *experience* of the build into the *capacity* for the next build.

The build produced a working simulation. The PROJECT.md Generation Log captured the operational lessons. CLAUDE.md absorbed the entries that should persist. The post-build document captures something different: the *meta-lessons* — what the build taught you about *how you build*. The five sections are designed to surface five kinds of knowing that emerged from the build:

1. What I built (the record).
2. What I delegated to Claude and why (the labor split).
3. What I kept for myself and why (the irreducibly-mine work).
4. What I learned that I did not know before the build (the discovery).
5. What I would do differently (the meta-revision).

Each section is one paragraph. Five paragraphs total. The document fits on one page. The discipline is honesty over polish.

---

## The five sections, walked through

### Section 1: What I built

One paragraph. Plain language. The kind of description you would give to a colleague who asked what you spent the last three months on.

Example:

> I built an interactive visualization of bubble sort for my ninth-grade CS class. Students step through the sort one comparison at a time; the simulation highlights what is being compared, what is being moved, and the growing sorted region. The point of the simulation is not to teach bubble sort as a tool — it is to make visible *why bubble sort scales as O(n²)* and to give students an object to think with as they reason about comparison-based sorts. The simulation deploys via GitHub Pages, runs in any browser, works on Chromebooks. I used it across three class periods this week.

Plain. Specific. Names the artifact, the pedagogical purpose, the deployment. No marketing.

### Section 2: What I delegated to Claude and why

The labor split, made explicit.

Example:

> I delegated to Claude: the HTML scaffold (pattern work; Claude does this faster than I do); the CSS layout once the DESIGN.md variables were defined (Claude applies a defined design system reliably); the bubble-sort algorithm logic itself (well-specified algorithm with clear test cases); the click and keyboard handlers (interaction wiring is pattern work); the mobile-breakpoint CSS after the screenshot-iterate sequence. Each was pattern work the CLI does faster than I can, with my verification — read, plan-mode edit, handoff condition check, plausibility audit — bounded and fast.

The pattern: the CLI handled the operations where *what to type* was the work. The teacher verified. Verification was bounded because the operations were standard.

### Section 3: What I kept for myself and why

The mirror. The work that was irreducibly mine and what made it so.

Example:

> I kept for myself: the Intent Layer in PROJECT.md — what the simulation is for, what students should understand after using it, what questions it refuses to answer. The pedagogical framing (bubble sort *as a counterexample* to efficiency, not as a tool to memorize) is a stance only I have because I know how my students will encounter sorting algorithms in the rest of the curriculum. I kept the DESIGN.md decisions — the six colors and what each one signifies, the interaction vocabulary (single-click, long-click, keyboard arrows, no drag-and-drop), the accessibility constraints. I kept the decision to deny the reset-button suggestion from Step 4 — Claude's idea was good; it was not the current build's scope; logging it in PROJECT.md Layer 2 preserved the build's integrity. Each required knowing my classroom, my prior CLAUDE.md rules, or my own judgment about what reliability means in a 50-minute class period.

The pattern: the teacher handled the decisions where *what to decide* was the work. The decisions required context the model did not have.

### Section 4: What I learned that I did not know before

The discoveries. The features of the tooling, the model's behaviors, the patterns of the build that you understand better now than three months ago.

Example:

> Three things I did not know in February that I know now. First, the screenshot-iterate pattern is dramatically more effective than verbal description for visual targets — three rounds of attached screenshots produced the right mobile layout that twenty rounds of verbal description had failed to produce. Second, Claude's compliance with negative constraints is at the *letter level* unless the constraint is restated at the *architectural level*; *no setTimeout* allowed Claude to produce an architecturally-async callback structure; *no setTimeout, no async, no callbacks; synchronous and pure* worked. Third, the DESIGN.md six-color constraint is the most useful single constraint I have written across any of the three builds — the boundary forced decisions I would otherwise have left to defaults that would have been wrong.

Three specific things. Each is operational. Each will inform the next build.

### Section 5: What I would do differently

The honest section. A specific decision you would reverse, not a general gesture toward improvement.

Example:

> I would write the Intent Layer of PROJECT.md *before* CLAUDE.md and DESIGN.md, not after. I wrote them in the order CLAUDE.md → DESIGN.md → PROJECT.md, on the assumption that the technical and visual constitutions would constrain the intent. The reverse turned out to be true — the Intent Layer (what students should understand, what tone matters, what questions the simulation refuses) changed what I would have put in DESIGN.md (color decisions are pedagogical, not decorative) and what I would have put in CLAUDE.md (some technical decisions follow from intent). The next simulation will start with PROJECT.md Layer 1, then DESIGN.md informed by Intent, then CLAUDE.md informed by both.
>
> I would also specify the step-controls in Chapter 11's Step 4 *before* the simulation logic in Step 3. I built Step 3's algorithm assuming asynchronous animation; Step 4's controls revealed the assumption was wrong; Step 3 was rebuilt. The dependency runs Step 4 → Step 3, not the order I executed. The Generation Log captured the rebuild but the original specification order created the rebuild. Next time: derive the algorithm from the control interface, not the other way around.

Two specific reversals. Each names what would change and why. No performative humility. No generic *I would improve discipline*. The specific is the point.

---

## The template

For your own simulation:

```markdown
# Post-Build Document — [Simulation name] — [Date]

## What I built
[One paragraph. Plain language. The artifact, the pedagogical purpose, the deployment, the classroom use so far.]

## What I delegated to Claude and why
[One paragraph. The specific work the CLI did, and what made it pattern work.]

## What I kept for myself and why
[One paragraph. The specific work I kept, and what context made it irreducibly mine.]

## What I learned that I did not know before
[One paragraph. Three concrete things. Operational, not abstract.]

## What I would do differently
[One paragraph (or two). A specific decision to reverse. What changed and why.]
```

Use it. Thirty minutes. One page. Save next to your CLAUDE.md and your three-file system.

---

## What the document is for

Two readers.

**Future you, at the next build.** You will start the next simulation — for a different concept, a different class, a different semester. You will open the post-build document before you open the new build's blank PROJECT.md. The *what I would do differently* section will become the first three lines of the new build's architecture. The *what I learned* section will inform the new build's specifications. The document compounds.

**Yourself, today, as honest assessment.** The act of writing the document forces you to name the labor split, the discoveries, the reversals. Most of what you learned in the build is implicit; the document makes it explicit. The explicit knowledge is the knowledge that transfers.

The document is *not* for performance or for credentialing. It is for the practitioner you will be in three months. Honest is the only useful register.

---

## Teacher build: write the document tonight

This is the chapter's last apply-level exercise. The book's last exercise.

Tonight. Thirty minutes. Five paragraphs. Save next to your CLAUDE.md.

Do not wait until you have time. You will not. Write it while the build is fresh — while the dangerous middles are still vivid, while the architectural decisions are still re-decidable, while the deployment failures are still recoverable lessons. Two weeks from now, you will be revising it for accuracy; two months from now, you will be reconstructing it from memory; six months from now, you will have lost it.

Write tonight.

---

## What you now have

By the end of this chapter, you have:

- **A working class website** built in Act One; deployed weekly.
- **A working grading tool** built in Act Two with Skills, Hooks, Subagents, the narrowing principle.
- **A working interactive simulation** built in Act Three with the three-file system; deployed in your classroom.
- **A three-tier CLAUDE.md.** Spanning all three builds. Lessons Learned section with the year's most valuable operational knowledge.
- **A DESIGN.md and a PROJECT.md** for the simulation (the second and third files of the Brutalist three-file system).
- **A post-build document.** Five sections. One page. Honest.
- **A classroom that has seen the framework in action.** Students who have watched you conduct, not just code; who have audited a build log; who can name PA, PF, TO, IJ, EI and label them in their own work.
- **The practice.** You are no longer following a framework written in a book. You are practicing a discipline you have lived through three builds and one school term.

That is what the book was for.

---

## The next build

The book ends here. The practice continues.

A few possibilities for what comes next.

**The next simulation.** A different concept, a different unit, a different class. The framework will be faster the second time — most of CLAUDE.md is reusable; DESIGN.md is reusable or one-edit-away; the three-file process is reflex. The second simulation will take 30–45% of the time the first one took.

**A different build domain.** The framework is tool-agnostic at the conceptual level. The same discipline (persistent context, the gate, specifications, handoff conditions, supervisory capacities, the dangerous middle, session management) transfers to `gh copilot` (with CLI.md as the manually pasted context) and to Codex (with AGENTS.md as the auto-loaded context). The companion books — *GitHub Copilot CLI for Teachers* and *Codex for Teachers* — teach the variant disciplines. The framework is the same; the affordances differ.

**Sharing the artifacts.** Your redacted CLAUDE.md, your three-file system for the simulation, and your post-build document are now teaching material. Other teachers at your school could use them. Other teachers in your district could use them. The constructionist tradition Papert named is the teacher who builds artifacts that have meaning beyond their construction; your artifacts qualify.

**Just teaching the unit.** The framework is the unit. The students are now doing what you did. Your role is the question-asker, the artifact-bringer, the framework-conductor of conductors. That is enough.

Whatever you do next, you have the practice.

---

## Common misconceptions

**"Post-build documents are bureaucratic."** Five paragraphs. One page. Thirty minutes. The discipline is honest, not exhaustive.

**"I will write it when I have time."** Tonight. Memory degrades fast. The document written now is the document that captures the lessons; the document written in two weeks is the document that captures your reconstructed best-guess at the lessons.

**"My document should be modest."** Performative humility is the failure mode. The discipline is specific self-criticism — *I would reverse this exact decision* — not general modesty.

**"This is just a journal entry."** A journal entry serves the present writer. The post-build document serves the future builder. The structure (five named sections; specific reversals) is what makes it portable.

**"I am done after I write this."** You are done with the book. The practice has just become yours. The next build is where the framework demonstrates its compounding value.

---

## Exercises

1. **(Create)** Write your post-build document for the simulation you built across Chapters 10–12. Five sections. One page. Save as `post-build-simulation-1.md` next to your CLAUDE.md.

2. **(Evaluate)** Read your *What I would do differently* section aloud. Is the reversal specific (you can name the decision and the alternative), or general (you would "improve" something)? If general, rewrite for specificity.

3. **(Apply)** Open your CLAUDE.md and your post-build document side by side. Sketch the first three lines of the architecture section for your *next* simulation (whatever it is). What changes carry over directly from the post-build document?

---

## What would change my mind

The chapter's strong claim is that **completing the post-build document materially improves subsequent build performance** compared to ending the build at the working artifact. If a controlled comparison found that teachers who skipped the post-build document built their next simulation with comparable speed and quality to teachers who wrote one, the document becomes optional. The chapter would still recommend the artifact for portability and PCK development; the urgency softens.

I expect the difference to be substantial because the post-build document is the only place where the meta-lessons of the build (the labor split, the reversal candidates, the technical pattern discoveries) become explicit. Without explicit articulation, the meta-lessons stay implicit and do not transfer cleanly to the next build.

---

## Still puzzling

- **Whether the post-build document should be shared.** The CLAUDE.md becomes classroom material (Chapter 13). The post-build document is more personal — it can contain reflections the teacher does not want public. The working answer: private by default; portions extractable for sharing with colleagues or students when the lessons are particularly transferable.

- **The right cadence for post-build documents.** One per significant build (one that took more than 2–3 hours). The book recommends one for the simulation; teachers who build multiple simulations in a semester will accumulate multiple documents. The working answer: write one per significant build; skip for one-off scripts.

- **What comes after the post-build document.** The book ends; the practice continues. The next build will produce the next post-build document. After three or four builds, the documents themselves become an artifact worth re-reading periodically to see the patterns across builds.

---

## Links

- [*Claude Code for Students*](https://anthropic.com) — the companion your students are reading.
- [*GitHub Copilot CLI for Teachers*](https://anthropic.com) — the variant discipline for `gh copilot`.
- [*Codex for Teachers*](https://anthropic.com) — the variant discipline for Codex with AGENTS.md.
- [brutalist.art](https://brutalist.art) — the three-file system source.

---

##  AI Wayback Machine
 **bell hooks** (1952–2021) — American scholar and educator whose *Teaching to Transgress: Education as the Practice of Freedom* (1994) argued that **the teacher is a practitioner who learns alongside the student**, and that the most important pedagogical artifact a teacher produces is the *record of their own continuous learning*, made visible to the students they teach.[^1] hooks's argument was against the teacher-as-finished-authority model — the teacher who has mastered the subject and now delivers it. She argued for the teacher-as-fellow-practitioner — the teacher who is still learning the subject, who shows the learning openly, who treats the classroom as a site where the teacher's growth and the students' growth are bound together.

The post-build document is hooks applied to AI-assisted teaching. The document is not a record of mastery; it is a record of continued learning. The *what I would do differently* section names what the teacher does *not* yet have mastery of — the decisions they would reverse, the architectures they would rewrite, the orderings they would invert. The honesty is the pedagogical content. A teacher who can show students *what I am still learning, three months into this practice* models the discipline the framework exists to produce — not the appearance of certainty, but the practice of conducting under uncertainty.

hooks wrote that *"as a classroom community, our capacity to generate excitement is deeply affected by our interest in one another, in hearing one another's voices, in recognizing one another's presence."* The chapter's recommendation — share your post-build document's lessons with your colleagues, with your students, with the next teacher to read this book — is hooks's vision applied to the artifact. The document is one voice in a conversation across practitioners; the conversation is the field of practice. Without the documents, each practitioner reinvents the framework alone. With them, the practice compounds across teachers and across years.

The book ends here.

You are now the practitioner.

The next build is yours.

---

[^1]: hooks, b. *Teaching to Transgress: Education as the Practice of Freedom*. Routledge, 1994. The 2017 paperback edition is the standard recent reference. (hooks chose to write her name in lowercase as a deliberate orthographic choice; this convention is preserved here.)
![bell hooks](../images/bell-hooks-y18.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

