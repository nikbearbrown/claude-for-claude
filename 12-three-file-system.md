# Chapter 10 — The Three-File System: Intent, Constitution, State


## TL;DR

- This chapter gives a working overview of The Three-File System: Intent, Constitution, State, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, The three files, CLAUDE.md — Technical Constitution, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> Before Claude writes a single line of the simulation, three files define everything. The Intent Layer is human, always. The Technical Constitution governs what Claude never improvises. The Project State tracks what is built and what is pending.

---

## Learning outcomes

1. **(Understand)** Explain what each of the three files protects and why all three must be populated before generation begins.
2. **(Apply)** Write the Intent Layer of `PROJECT.md` for an interactive classroom simulation.
3. **(Apply)** Write a `DESIGN.md` specifying the simulation's visual and interaction vocabulary completely enough that Claude cannot improvise outside it.

---

## Opening

The teacher opened Claude Code in a fresh project directory and typed:

```
build me an interactive sorting algorithm simulator
```

Claude built something. The agentic loop ran for about eight minutes. At the end there was an `index.html` with a sorting visualization, a `style.css` with a color palette, a `simulation.js` with the algorithm logic. The teacher opened it in the browser.

It worked.

It also was not theirs.

The color scheme was Material Design's default palette — bright blue, white, gray. The teacher's class website used four earth tones; the simulation looked like it had been built by a different person for a different course. The interaction model was drag-and-drop — the teacher had wanted single-click stepping. The pedagogical scaffolding (what each color meant, why the algorithm slowed down on certain inputs, what the comparison count was telling the student) was missing. The simulation worked as code. It did not work as *the lesson*.

The teacher had not drawn the line before Claude started. They had given Claude one line of intent (*"build a sorting simulator"*); Claude had filled in everything else with defaults. The defaults were reasonable for some generic sorting simulator. They were wrong for *this* simulator, in *this* classroom, for *these* students.

This chapter is the alternative. Before Claude writes a line of code for the simulation, three files define everything. The files are written by you, in 45–60 minutes total. The files are then in Claude's context for every subsequent build prompt. The simulation that emerges is *yours* — the colors are yours, the interaction model is yours, the pedagogical scaffolding is yours, because you decided each of those before Claude saw the project.

The framework is from the Brutalist Design System at [brutalist.art](https://brutalist.art) — the author of this book's own design framework. The chapter is transparent about this. Brutalist is one entrant in a broader 2025–2026 ecosystem of AI-readable design files (designmd.app catalogs 454 design systems; getdesign.md and others have similar three-file or two-file structures). The principles transfer; the specific Brutalist conventions are the ones the chapter teaches because they are the ones the author maintains and uses.

![The three-file system as nested boxes](images/12-three-file-system-fig-01.png)
*Figure 12.1 — The three-file system as nested boxes*

---

## The three files

Each file protects a different concern.

### CLAUDE.md — Technical Constitution

What you have already been building since Chapter 2. For the simulation project, CLAUDE.md is the technical constitution:

- The stack (vanilla HTML/CSS/JS, no framework, single-file deployable).
- File structure (`index.html`, `simulation.css`, `simulation.js`; no other top-level files).
- Naming conventions (kebab-case for HTML IDs; camelCase for JS functions).
- What Claude never touches without instruction (the class website's shared CSS; any file outside the simulation directory).
- Verification commands (`open index.html` and verify no console errors; test on mobile breakpoint).

CLAUDE.md changes when the *tech stack* changes — adopting a different framework, switching to a build tool, adding TypeScript. Most projects, most of the time, CLAUDE.md is stable across the project's life.

### DESIGN.md — Visual Constitution

The file that is new for Act Three. CLAUDE.md governs what Claude does *technically*; DESIGN.md governs what Claude does *visually and interactively*.

The minimal DESIGN.md for the sorting simulator:

```markdown
# DESIGN.md — Sorting Algorithm Simulator

## Color system (six variables — no others)

- `--background: #f5f5f0` (warm white)
- `--ink: #1a1a1a` (near-black for text and lines)
- `--accent: #c97a3a` (terracotta — for the element being compared)
- `--target: #3a7ac9` (blue — for the element being placed)
- `--rule: #999999` (medium gray — for axis lines)
- `--highlight: #f0d843` (amber — for the sorted region of the array)

If a sixth color seems necessary, escalate to the human; do not invent.

## Typography

- Monospace for all numeric values (font: ui-monospace).
- Sans-serif for labels and instructions (font: system-ui).
- No serif. No script. No additional font weights.

## Interaction vocabulary

- Single click on a bar: step forward one comparison.
- Long click on a bar: step backward one comparison.
- Keyboard arrows: fine control (left = backward, right = forward).
- Spacebar: play/pause auto-stepping.

Never:
- Drag-and-drop.
- Hover-only states (touch devices have no hover).
- Hidden controls that require a menu.
- Animation faster than 60ms per step (cognitive limit).

## Accessibility

- All interactive elements keyboard-navigable.
- All visual state has a text equivalent (screen-reader-readable).
- Minimum font size: 16px.
- Color is never the only carrier of state — always paired with shape or label.
```

The six-color rule is the chapter's most-quoted constraint. The point is not the specific number — it is the *boundary*. A simulation with twenty colors is one where the design was made by Claude's defaults. A simulation with six colors, each named, each with a defined role, is one where the design was made by you.

The *never* rules are the most important section. They name the failure modes you are protecting against: drag-and-drop fails on touch devices and on keyboard-only users; hover-only states are invisible to anyone who cannot hover; faster animation than 60ms breaks the cognitive parse. Each *never* is a decision you make once, written down, enforced through the constitution.

DESIGN.md changes when the *design system* changes — adopting a new color palette, switching the interaction vocabulary, adding accessibility requirements. Like CLAUDE.md, DESIGN.md is stable across most of the project's life.

### PROJECT.md — Project State (two layers)

The file unique to Act Three. PROJECT.md has two layers, with different ownership.

**Layer 1 — Intent (human, never overwritten by Claude).** What this simulation is, what students should understand after using it, what questions it answers, what questions it refuses to answer, the tone.

```markdown
## Layer 1: Intent (Human Layer — never overwritten by Claude)

What this simulation is:
An interactive visualization of bubble sort showing why it scales as O(n²) — why
the runtime grows steeply as the array gets larger.

What the student should understand after using it:
- That comparison-based sorts have a tradeoff between simplicity and efficiency.
- That bubble sort is simple to explain (compare adjacent, swap if needed) and
  slow to run because it does work proportional to n².
- That small changes (one early swap) can have cascading effects on subsequent steps.
- That "already sorted" is the best case (one pass) and "reverse sorted" is the
  worst case (n² swaps).

What questions it answers:
- "How does bubble sort work mechanically?"
- "Why is it slow on large inputs?"
- "What happens when the input is already sorted?"
- "How does the comparison count grow with input size?"

What questions it refuses to answer:
- "Which sorting algorithm is best?" (too context-dependent; not this simulation's job)
- "Why is quicksort faster?" (a different simulation's job)
- "How do you implement bubble sort in [language]?" (not a simulation question)

Tone:
- Matter-of-fact; slightly playful.
- No jargon (terms like "asymptotic complexity" are introduced gently or avoided).
- No condescension — the student is the protagonist.
```

The Intent Layer is the pedagogical content of the simulation. Claude cannot write it because Claude does not know what your students need to understand, what tone matches your classroom, or which questions matter for your unit's sequence. You write it. Once written, it is *protected* — Claude is instructed not to overwrite or revise it without your explicit request.

**Layer 2 — Technical State (the AI layer).** What is built, what is pending, the generation log, open questions.

```markdown
## Layer 2: Technical State (AI Layer)

What is built:
- HTML scaffold with the array container and controls.
- CSS with the six variables defined and applied to the scaffold.

What is pending:
- JS simulation logic (the bubble-sort step function).
- Step controls (click handlers, keyboard handlers).
- Sorted-region highlighting.
- Mobile-breakpoint adjustments.

Generation log:
- 2026-03-12: HTML scaffold generated (per spec; pass).
- 2026-03-12: CSS variables defined (per spec; pass).
- 2026-03-12: First attempt at simulation.js included a global mutable
  state; rejected; respecified with explicit pure-function step; pass.

Open technical questions:
- Performance on inputs > 100 elements: investigate before student deployment.
- Keyboard handling for screen-readers: needs WAI-ARIA review.
```

The Technical State is updated by Claude (or by you, manually) as the build proceeds. The Generation Log is the project's record of what was built and what was rejected — useful for the post-build document in Chapter 14 and for the next teacher who picks up the project.

The two layers' ownership matters. Layer 1 stays the same across many sessions; Claude treats it as a constitutional ground. Layer 2 evolves continuously; Claude updates it as part of the build workflow.

---

## The 45-minute scope test

A forcing function from the Brutalist framework.

If you cannot write all three files for your simulation in 45 minutes, the simulation's scope is too large for a first build.

This is not a deadline; it is a diagnostic. A simulation that requires three hours of file-writing is a simulation with too many decisions for one build. Decompose. Build the smaller piece first. The framework rewards scoped simulations and punishes monolithic ones.

For most teacher-scale simulations — a sorting visualization, a plate tectonics simulator, a probability tree explorer, a sentence-tree parser — the 45-minute test passes. The teacher writes CLAUDE.md (15 minutes; mostly refined from `/init`), DESIGN.md (15 minutes; the six-color rule forces decision), PROJECT.md (15 minutes; Intent Layer first, Technical State stubbed). The simulation that follows is the size the framework can support.

For more complex tools — a full interactive textbook chapter, a multi-simulation suite, a classroom-management system — the 45-minute test fails. The right response is to *scope down*. Build the smaller piece this week. The full vision is a series of builds, not a single one.

---

## The Brutalist governing principle

The Brutalist framework's central commitment: **maximally informed, minimally autonomous, by design.**

*Maximally informed* — Claude has every file it needs. CLAUDE.md is complete. DESIGN.md is complete. PROJECT.md is populated. Claude operates with full project context.

*Minimally autonomous* — Claude does not improvise outside the boundary the three files define. If the six-color rule says no additional colors, Claude does not invent a seventh. If the interaction vocabulary says no drag-and-drop, Claude does not add drag-and-drop "for convenience." The three files are not suggestions; they are the contract.

*By design* — the discipline is the architecture, not a hope. The Brutalist Refusal Behavior is the operational form: when you ask Claude to make a decision that belongs in the human layer (a creative judgment, an aesthetic choice that exceeds the six-color rule), Claude declines and asks you to make the decision. The system is built to refuse, not just to advise.

The Brutalist principle is the answer to the chapter-opening failure. Claude built a generic sorting simulator because Claude was *minimally informed* (one line of intent) and *maximally autonomous* (every other decision was Claude's). The Brutalist inversion — fully informed, minimally autonomous — produces the simulation that is yours.

---

## A worked example: the sorting simulator's three files

Composed and stored at `~/teaching/sorting-sim/`. The files take 40 minutes to write.

**CLAUDE.md** (under 100 lines):

```markdown
# Sorting Simulator — Technical Constitution

## Stack
Vanilla HTML/CSS/JS. No framework. No build step. Single-file deployable
or three-file (index.html, simulation.css, simulation.js).

## File structure
- `index.html` — the simulation page
- `simulation.css` — visual styling (loads variables from DESIGN.md)
- `simulation.js` — algorithm logic + interaction handlers

No other top-level files. Tests in `tests/` if any.

## Code conventions
- Kebab-case for HTML IDs and CSS classes.
- camelCase for JavaScript functions and variables.
- ES modules; no bundler.
- All functions pure where possible; state explicit and named.

## Never touch
- Files outside `~/teaching/sorting-sim/`.
- The class website's shared CSS at `~/teaching/class-website/src/styles.css`.

## Verification
- Open `index.html` in Chromium; no console errors.
- Test on Chromium devtools mobile preset (iPhone 12); no horizontal scroll;
  controls remain accessible.
- Keyboard nav from page top to all controls without mouse.
```

**DESIGN.md** (the one shown earlier in the chapter).

**PROJECT.md** (the one shown earlier in the chapter).

All three are committed to git. The next session in this project loads all three automatically (CLAUDE.md by Claude Code's hierarchical discovery; DESIGN.md and PROJECT.md by explicit reference in the first prompt or via a Skill that pastes them).

---

## Teacher build: write the three files for your simulation

Apply the chapter's discipline to your own simulation (from the Bridge's question — *what concept in your curriculum has no good interactive analog yet?*).

1. **Open a new project directory** for the simulation. `mkdir ~/teaching/<simulation-name>/ && cd ~/teaching/<simulation-name>/`.

2. **Run `/init`.** Read the generated CLAUDE.md. Refine for this project.

3. **Write DESIGN.md.** Six colors max (and yes, that constraint will force you to decide what each color means). Typography. Interaction vocabulary. Accessibility requirements. Explicit *never* list.

4. **Write PROJECT.md.** Layer 1 first — what the simulation is, what students understand after, what questions it answers and refuses. Layer 2 second — what is built, what is pending, an empty generation log.

5. **Time yourself.** Total under 45 minutes? Scope is right; proceed to Chapter 11. Over 45 minutes? Decompose; build a smaller piece first.

6. **Commit all three to git.** `git add CLAUDE.md DESIGN.md PROJECT.md && git commit -m "Three-file system: initial population"`.

7. **Sleep on it.** The decisions you made under 45 minutes of pressure benefit from one overnight pass. Tomorrow morning, re-read each file. Edit one or two things you will see differently. The 5-minute pass is the upgrade from competent files to good files.

---

## Classroom activity: students write a PROJECT.md Intent Layer

For your students' classroom:

Each student selects a simulation they would build for a class they are in (math, science, language, history). They write the Layer 1 Intent Layer of `PROJECT.md`:

- What this simulation is (one sentence).
- What the student-user should understand after using it (3–5 specific claims).
- What questions it answers (3–5).
- What questions it refuses to answer (2–3).
- Tone (3–5 words).

Pair students. Each peer reads the partner's Intent Layer *without seeing the simulation concept*. Can the peer guess what the simulation is about? If yes, the Intent Layer is doing its job. If no, the Intent Layer is too vague; rewrite.

The pedagogical move: students learn that the Intent Layer is *the simulation's identity* — the thing that, once specified, every subsequent build decision must serve. After two rounds, students write Intent Layers specific enough to guide their own builds. The peer-test is the diagnostic for whether the writing was specific enough.

This activity maps to the student book's chapter on the three-file system. Your design here is the worked example.

---

## Common misconceptions

**"Three files is too much for a small simulation."** No. The 45-minute test forces appropriate scope. A simulation that does not fit in 45 minutes of file-writing is too large for a first build.

**"DESIGN.md is for designers."** No. DESIGN.md is for anyone building anything with visual or interactive output. The teacher writing a simulation has design decisions whether they call them design or not; DESIGN.md is the file where those decisions live.

**"Claude can figure out the Intent Layer."** No. Intent is irreducibly human. Claude can write something that *sounds like* an Intent Layer for a generic version of your simulation; what it cannot write is the Intent Layer for *your* classroom's specific need. The Intent Layer is the work you do not delegate.

**"I'll iterate on the files as I build."** This is the forward-correction failure mode from Chapter 4 applied to file writing. The cost of writing the files first is 45 minutes. The cost of iterating during the build is hours, and the iteration tends to drift toward whatever the immediate-prompt pressure suggests rather than what the constitution required.

**"The six-color rule is arbitrary."** The number is. The boundary is not. The point is that there is a *limit*, named, with explicit escalation when the limit is reached. Six is the Brutalist default; you can use eight or four if your design system has a different limit. The structural point is that some limit exists.

---

## Exercises

1. **(Apply)** Write all three files for your simulation project before running Claude. Time yourself. If it takes more than 45 minutes, scope down.

2. **(Analyze)** Review a peer's PROJECT.md Layer 1 (or your own from a week ago). Identify what the Intent Layer is *missing* that would have prevented a likely default-behavior failure during the build.

3. **(Evaluate)** What decisions in your simulation are pedagogical (Intent Layer)? What are technical (CLAUDE.md)? What are visual (DESIGN.md)? List three of each. Some decisions will live in multiple files; name which is primary.

---

## What would change my mind

The chapter's strong claim is that **the three-file system, completed before generation begins, materially improves the simulation's fit to the classroom context** compared to ad-hoc per-prompt specification. If a controlled comparison found no measurable difference in simulation quality or revision rate between three-file-discipline and per-prompt practice, the chapter's discipline becomes optional. The chapter would still recommend the Intent Layer for pedagogical protection; the visual-constitution case softens.

I expect the difference to be substantial because the chapter-opening failure (Claude builds a generic simulator because the boundary was not drawn) is exactly the failure mode the three files prevent, and it is the modal failure mode of Act Three work for teachers without a design background.

---

## Still puzzling

- **Two-file vs. three-file vs. four-file.** Some practitioners drop PROJECT.md and inline the Intent into CLAUDE.md. Some add SKILL.md as a fourth. The Brutalist three-file commitment is operational, not theoretical. The chapter teaches three; the principles transfer to other counts.

- **The Intent Layer's protection.** Claude is *instructed* not to overwrite Layer 1. Compliance is probabilistic (per Chapter 7's CLAUDE.md vs. Hook distinction). For high-stakes intent layers, the protection should be a Hook (PreToolUse on Edit to `PROJECT.md` blocking any change that touches Layer 1). The chapter's working answer: the instruction is enough for most teacher work; the Hook is available when the stakes warrant.

- **brutalist.art and the broader ecosystem.** The Brutalist framework is one entrant in a growing 2025–2026 DESIGN.md ecosystem (designmd.app, getdesign.md, awesome-design-md). The principles transfer; the specific Brutalist conventions are the ones this chapter teaches because they are the author's. Teachers may adopt other systems; the framework holds.

---

## Links

- [brutalist.art](https://brutalist.art) — the author's design framework, the source for the three-file system.
- [designmd.app](https://designmd.app) — the broader DESIGN.md ecosystem.
- [getdesign.md](https://getdesign.md) and [awesome-design-md](https://github.com/VoltAgent/awesome-design-md) — alternative implementations of the same idea.

---

##  AI Wayback Machine
 **Eileen Gray** (1878–1976) — Irish-French architect and designer whose villa **E-1027** (built 1926–1929 on the French Riviera) is one of the canonical examples of *total design* — a building in which every element from the structural frame to the door handles to the built-in furniture to the wall-mounted maps was specified by the designer as a coherent whole.[^1] Gray's argument, articulated in her writings and embodied in her work, was that **the parts of a designed environment cannot be separated from the whole** — that a house designed by one person and furnished by another is two designs in conflict, regardless of either party's skill.

The three-file system is Gray's total design applied to AI-assisted simulation building. The Technical Constitution is the structural frame: what the simulation is made of. The Visual Constitution is the interior surfaces: what it looks like and how the user interacts. The Intent Layer is the program: what the building is *for*, what experience it is designed to produce. A simulation built with the three files complete is designed as a coherent whole; a simulation built from a one-line prompt is two designs in conflict — yours (implicit) and Claude's (explicit and inconsistent with yours).

Gray's E-1027 was for many decades attributed to her partner Le Corbusier, who lived in the villa, painted murals on its walls without her permission, and whose name eclipsed hers in the historical record. The reattribution to Gray is a story of the same year — 2026 — that this chapter is published in: the slow but increasing recovery of women's authorship in modernist design. The chapter cites Gray here partly because her total-design framework is the precise architectural ancestor of the three-file system, and partly because the cumulative effect of every chapter's Wayback figure matters. Gray belongs in the lineage.

The Brutalist framework that the chapter operationalizes is itself in this lineage — a 2025–2026 design system that asks the practitioner to specify the *whole* before generating any of the *parts*. The discipline is total design. The artifact is the simulation. The author is you.

---

![Eileen Gray](../images/eileen-gray-3pu.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

## Bridge

The three files are written. Chapter 11 executes the build — the full simulation with every Act One and Act Two discipline applied to a third tool that serves the student rather than the teacher.

---

[^1]: Adam, P. *Eileen Gray: Her Life and Her Work*. Thames & Hudson, 1987 (updated 2009). The 2013 Pompidou *Eileen Gray* exhibition catalog is the standard recent reference for her design philosophy and the E-1027 villa.
