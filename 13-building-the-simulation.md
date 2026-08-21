# Chapter 11 — Building the Simulation: Conducting at Full Complexity


## TL;DR

- This chapter gives a working overview of Building the Simulation: Conducting at Full Complexity, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, Step 1: HTML scaffold, Step 2: CSS with the six DESIGN.md variables, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> The three files are in place. The build begins. Every step has a specification, a handoff condition, and a labeled supervisory capacity. This is what conducting looks like at full complexity.

---

## Learning outcomes

1. **(Apply)** Execute a five-step simulation build using the full framework: three-file system, specifications, handoff conditions, capacity labels, CLAUDE.md updates.
2. **(Apply)** Apply each supervisory capacity at the steps requiring it, labeled explicitly.
3. **(Analyze)** Recognize when the simulation build is drifting from the three-file constitution and stop before the drift compounds.

---

## Opening

CLAUDE.md was written. DESIGN.md had its six colors and its interaction vocabulary. PROJECT.md's Intent Layer named exactly what the sorting simulator was for and what it refused to do. All three were committed to git. The teacher had slept on the files and made two small edits in the morning. The simulation was about to be built.

The instinct, after three months of conducting practice and 45 minutes of file-writing, was to compose all five steps as one specification and let Claude build the whole simulation at once. The instinct was wrong. The three files protect against the wrong outputs; the per-step gate (Chapters 3–4) protects against the *right* outputs being assembled in the *wrong* sequence. Both protections are needed.

This chapter walks through the build step by step. Five steps. Each gets a specification, a gate (Explore → Plan → Implement → Commit), a handoff condition, a capacity label, a Generation Log entry in PROJECT.md Layer 2. Along the way three pivotal moments arrive: a handoff failure that requires `/rewind`, a scope-creep moment that requires log-and-decline, and a screenshot-iterate pattern that produces dramatically better visual output than single-pass generation. The chapter is long because the build is the chapter; treat it as a walkthrough to follow alongside your own.

![The simulation build loop](images/13-building-the-simulation-fig-01.png)
*Figure 13.1 — The simulation build loop*

---

## Step 1: HTML scaffold

**Specification.**

- *Operation:* Generate `index.html` with the page scaffold for the sorting simulator.
- *Invariants:* Use only the six DESIGN.md color variables (defined as CSS custom properties in `:root`). No styling beyond what is needed for the scaffold to render.
- *Context:* DESIGN.md interaction vocabulary (single-click, long-click, keyboard arrows, spacebar); accessibility requirements (keyboard-navigable; ARIA labels on all interactive elements; minimum 16px font).
- *Output format:* Single `index.html` file with semantic HTML5 — `<main>`, `<section>` for the array container, `<section>` for the controls, `<aside>` for the comparison counter and step explanation. All interactive elements have `id` (kebab-case) and `aria-label`.
- *Negative constraint:* No inline styles. No `<script>` tag yet (Step 3 owns the JS). No external resources (no CDN, no font imports — all assets self-hosted later).

**Run the gate.**

Shift+Tab twice to enter plan mode. Submit the specification. Claude returns a plan: file structure, semantic-element choices, ID naming. Ctrl+G to edit the plan in the text editor. Notice that Claude proposed a `<header>` with the simulation's title — that was not in the specification. Decide: keep or remove. Keep, because a labeled simulation aids comprehension. Add to PROJECT.md Layer 2 generation log as a note: "Header element added during planning; not in spec; approved."

Switch out of plan mode. Claude executes. `index.html` exists.

**Handoff condition.**

Run the page in a browser. The structure is visible (containers for array, controls, side panel, header). No console errors. All interactive elements (will-be-buttons; currently `<button>` shells with no behavior) are tab-reachable from the page top with no mouse. The HTML validates (`npx htmlvalidate index.html` exits 0).

**Capacity label.**

`[PA]` on the plan's added `<header>` element. `[IJ]` on the keep-or-remove decision. `[EI]` on the no-inline-styles rule (the CLAUDE.md says vanilla CSS in `simulation.css`; the spec restated; the plan was checked against the rule).

**Generation Log entry (PROJECT.md Layer 2):**

```
2026-03-13: Step 1 — HTML scaffold. PASS.
  Plan added <header> not in spec; approved (labels simulation).
  No inline styles; semantic elements throughout; all interactive
  elements ARIA-labeled.
```

---

## Step 2: CSS with the six DESIGN.md variables

**Specification.**

- *Operation:* Generate `simulation.css` containing only the DESIGN.md six variables defined in `:root`, plus the minimal rules required to render the Step 1 scaffold with the design system's typography and spacing.
- *Invariants:* Exactly six color variables; no additional colors used directly anywhere (every color reference goes through a variable). Typography per DESIGN.md (monospace for numerics, system-ui for labels).
- *Context:* DESIGN.md is the source of truth; if the spec contradicts DESIGN.md, DESIGN.md wins; flag the contradiction.
- *Output format:* `simulation.css` linked from `index.html`; visual matches DESIGN.md's described palette and typography.
- *Negative constraint:* No additional colors. No hardcoded font names outside the typography section. No `!important`. No external font imports.

**Run the gate.**

Plan mode. Claude proposes the CSS. The proposal includes a *seventh* color — `--shadow: rgba(0,0,0,0.1)` — for subtle drop shadows on the controls. PA fires: the DESIGN.md says six colors max. The shadow is real-shadow language but it is still a color. Ctrl+G; remove the shadow variable; document in PROJECT.md that shadows are off the design language for this simulation.

Switch out of plan mode. Claude executes.

**Handoff condition.**

Open the page; visual comparison against DESIGN.md's color palette (the warm white background, the near-black text, the terracotta accent are all visible). Run `grep -c 'rgba\|rgb(' simulation.css` — count of direct color uses outside the six variables. Should be 0. Run `grep -c '!important' simulation.css` — should be 0.

**Capacity label.**

`[PA]` on the proposed seventh color. `[EI]` on the DESIGN.md six-color rule.

**Generation Log entry:**

```
2026-03-13: Step 2 — CSS with six DESIGN.md variables. PASS.
  Claude proposed --shadow as a 7th color; rejected per DESIGN.md
  6-color rule; shadows off the language for this simulation.
  All color references go through the six variables (verified).
```

---

## Step 3: JS simulation logic (first attempt and pivotal moment 1)

**Specification — first attempt.**

- *Operation:* Generate `simulation.js` with the bubble-sort algorithm as a function that takes an array and returns the sequence of states (each state is `{array, comparing: [i,j], swapped: boolean}`).
- *Invariants:* Pure function (no global mutable state). No DOM manipulation in this file (DOM updates are Step 4's concern).
- *Context:* PROJECT.md Layer 1 — the simulation visualizes bubble sort *step by step*; the student controls advancement; the simulation must support stepping forward and backward (which requires the full state sequence).
- *Output format:* `function bubbleSortStates(array): State[]` exported as an ES module.
- *Negative constraint:* No `setTimeout`. No automatic animation. The function returns data; UI consumption (Step 4) drives the visualization.

**Run the gate. First execution.**

Claude generates the function. The function uses `setTimeout` internally to "animate" the steps as it generates them, returning states asynchronously through a callback.

The handoff condition catches it. The negative constraint said no `setTimeout`. The function fails the spec.

**Pivotal moment 1: handoff failure → `/rewind`.**

Two options. Forward correction: ask Claude to remove the `setTimeout` and refactor. The risk: Claude restructures around the existing flawed approach; the result is "no `setTimeout`" but the architecture is still timeout-shaped (callbacks, async, hard to step backward through).

Better: `/rewind` to the checkpoint before Step 3. The conversation history of the failed attempt disappears; the file is removed. The teacher rewrites the specification with the failure mode as an explicit constraint:

```
*Negative constraint:* No setTimeout. No async. No callbacks.
The function must be SYNCHRONOUS and PURE — given the same input,
returns the same output array of states, deterministically and
immediately. The UI (Step 4) calls the function once at the start
and consumes the states one at a time as the user controls
stepping.
```

The restated constraint is more specific than the original. The original said "no setTimeout"; Claude technically obeyed the letter while violating the intent. The restated version closes the loophole.

**Run the gate. Second execution.**

Plan mode; Ctrl+G review; switch out; Claude executes. The new function is synchronous, pure, returns a flat array of states. Test with three inputs (an already-sorted array, a reverse-sorted array, a mixed array). The state sequences are correct.

**Handoff condition.**

`bubbleSortStates([3,1,2])` returns the expected state sequence (manually verified). Already-sorted input produces N-1 states (one pass, no swaps). Reverse-sorted input of length N produces N(N-1)/2 swap states.

**Capacity label.**

`[PA]` on the `setTimeout` violation. `[EI]` on the no-async constraint (restated more sharply in the revision). `[TO]` on the `/rewind` choice (revert-and-respecify, not forward-correction).

**Generation Log entry:**

```
2026-03-13: Step 3 — Simulation logic. PASS after one /rewind.
  First attempt used setTimeout internally despite spec saying no setTimeout.
  /rewind to checkpoint; respecified with stronger constraints (no async,
  no callbacks, synchronous and pure).
  Pattern note: when CLI generates the negation of a constraint at the
  letter level while violating it at the intent level, restate the constraint
  at the intent level explicitly.
```

CLAUDE.md gets an entry too: *"For pure-function specifications, prohibit both the immediate failure mode (setTimeout) and the architectural shape (async, callbacks) explicitly. Claude can technically obey the letter while violating the intent."*

---

## Step 4: step controls and keyboard handling (pivotal moment 2)

**Specification.**

- *Operation:* Generate the click and keyboard handlers in `simulation.js` (separate section from the algorithm; clearly bounded). Wire the controls to the state-sequence consumer.
- *Invariants:* Single-click steps forward; long-click steps backward; keyboard arrows fine-control; spacebar play/pause. Per DESIGN.md, no drag-and-drop, no hover-only states.
- *Context:* The state sequence comes from `bubbleSortStates`; the consumer maintains a `currentStateIndex` and updates the DOM when it advances.
- *Output format:* Handlers attached on DOMContentLoaded; the simulation is interactive on page load.
- *Negative constraint:* No animation faster than 60ms per step. No hidden controls (per DESIGN.md). Accessibility: keyboard-only navigation must reach every control.

**Run the gate.**

Claude generates the handlers. Submission. Plan looks reasonable. Switch out; execute.

The simulation works. Click steps forward; long-click steps backward; keyboard arrows work; spacebar play/pause works. The teacher uses the simulation for two minutes.

Claude then says, in the same response: *"While I'm here, I noticed that the simulation would benefit from a 'reset' button that randomizes the array. I can add that too — should I?"*

**Pivotal moment 2: scope creep → log and decline.**

The reset-with-random-input is a real improvement. It is not in the spec. It is not in DESIGN.md's interaction vocabulary. Adding it now would require a UI decision (where the button lives, what it looks like, whether it has a confirmation step) that DESIGN.md does not address.

The discipline: log to PROJECT.md Layer 2 as a future enhancement. Decline the current addition.

```
Layer 2 — Open enhancements (logged, not built):
- 2026-03-13: Reset button with array randomization (suggested by
  Claude during Step 4). Useful for repeated demonstrations.
  Requires DESIGN.md update for button placement and interaction.
  Revisit after first classroom deployment to confirm need.
```

Telling Claude *"do not add the reset; log it as a future enhancement; the current build's scope is the original five steps"* preserves the build's integrity. Adding it now would have extended Step 4 by 20–30 minutes and would have set the precedent for in-flight DESIGN.md expansion — exactly the failure mode the three-file system was set up to prevent.

**Handoff condition.**

All keyboard nav works. All click/long-click works. Animation respects the 60ms minimum. The teacher uses the simulation through a full bubble-sort run with an 8-element array; the experience is correct.

**Capacity label.**

`[EI]` on the scope-creep refusal (the original five-step plan is the scope; the reset is out). `[PF]` on the future-feature framing (the reset is not bad; it is *not now*).

**Generation Log entry:**

```
2026-03-13: Step 4 — Controls and handlers. PASS.
  Claude suggested reset-with-random-input enhancement; declined and
  logged for future build. Original scope preserved.
  All keyboard nav, click, and long-click work.
```

---

## Step 5: mobile responsiveness and screenshot iteration (pivotal moment 3)

**Specification.**

- *Operation:* Add the CSS media query for mobile breakpoint (max-width: 768px). The array container reflows to vertical orientation; controls stack below; comparison counter moves to a fixed footer.
- *Invariants:* DESIGN.md's typography and color rules unchanged at mobile breakpoint. Accessibility unchanged.
- *Context:* DESIGN.md says minimum 16px font; mobile should not violate this.
- *Output format:* `simulation.css` extended with the media query; mobile rendering verified on Chrome devtools (iPhone 12 preset).
- *Negative constraint:* No horizontal scroll at any breakpoint. No controls below the visible area at the breakpoint.

**Run the gate. Pivotal moment 3: screenshot iteration.**

This is the chapter's most useful 2026 pattern.

The teacher generates the CSS. Opens Chrome devtools, switches to iPhone 12 preset, takes a screenshot. The simulation renders — but the array bars are too narrow to read the numbers and the controls overflow horizontally.

Single-pass generation gets close; the visual fit takes iteration. The teacher includes the screenshot in the next prompt to Claude:

*"Here's the current mobile rendering [attached screenshot]. The array bars are too narrow and the controls overflow horizontally. Adjust the CSS to: (1) increase bar widths by stacking the array vertically below 768px; (2) reflow the controls to a 2x2 grid; (3) maintain the 16px font minimum."*

Claude regenerates the CSS. Take another screenshot. The bars are now correctly stacked but the comparison counter overlaps with the controls. Iterate again: *"Counter overlaps controls; move counter to a fixed footer with the page's --background color."*

Three iterations; the mobile rendering matches the desired target. The screenshot-iterate pattern is dramatically more reliable than describing the visual target in words.

**Handoff condition.**

Chrome devtools iPhone 12 preset: simulation renders, array vertical, controls in 2x2 grid, counter in fixed footer, no horizontal scroll, font ≥ 16px throughout. Run keyboard nav at the mobile breakpoint; all controls reachable.

**Capacity label.**

`[PA]` on each screenshot (visual judgment of the rendering against the DESIGN.md goals). `[IJ]` on the layout choices (which arrangement serves the student best at small screen). `[TO]` on the screenshot-iterate pattern (the right tool for visual targets).

**Generation Log entry:**

```
2026-03-13: Step 5 — Mobile responsiveness. PASS after 3 screenshot iterations.
  Screenshot pattern: take screenshot, attach to prompt, describe gap,
  Claude regenerates, repeat. Significantly faster than text-only description.
  Pattern note: for visual targets, screenshots are the highest-bandwidth
  spec format.
```

CLAUDE.md gets the pattern note: *"For visual builds, use the screenshot-iterate pattern: take screenshot, attach, describe gap, regenerate. Faster and more accurate than verbal description."*

---

## What you have at the end

A working interactive sorting simulator. Three files (CLAUDE.md, DESIGN.md, PROJECT.md) maintained across the build. Five Generation Log entries in PROJECT.md Layer 2. One `/rewind` documented in the log. One scope-creep deferral. One screenshot-iterate sequence. The simulation runs in the browser, handles all the documented interactions, respects DESIGN.md's constraints, and is ready for the classroom deployment in Chapter 12.

Total build time: 2–3 hours for the first simulation. The next simulation (different concept, same framework) will be 45–60 minutes because most of the CLAUDE.md is reusable, the DESIGN.md is reusable (or one-edit-away), and the discipline is now reflex.

---

## Teacher build: execute Phase 1 of your simulation

Apply the chapter's discipline to your own simulation (from Chapter 10's three files).

Walk through five steps appropriate to your simulation's domain. Adapt the chapter's specifics. Expect at least one pivotal moment per step (handoff failure, scope creep, or screenshot iteration) on the first build.

Document each pivotal moment in the Generation Log in PROJECT.md Layer 2. The pivotal moments are the most valuable artifacts of the build — they are the lessons that survive into your next simulation and into the post-build document in Chapter 14.

For visual targets, use the screenshot-iterate pattern from Step 5. It is the chapter's single most useful 2026 Claude Code workflow for simulation builds.

---

## Classroom activity: students build a small simulation

For your students' classroom:

Provide a small simulation target — a visualization of a single data structure operation (stack push/pop, queue enqueue/dequeue), a probability tree for a single coin-flip sequence, a sentence-tree for one short sentence. Small enough to fit in a 50-minute class.

Each student writes the three files for their target (PROJECT.md Layer 1, DESIGN.md with six colors and an interaction vocabulary, a minimal CLAUDE.md). They execute the build using the per-step gate. They document every pivotal moment in the Generation Log.

Pair students. Each peer reviews the partner's Generation Log: identify one pivotal moment that was well-handled; identify one moment that the discipline could have been tighter.

The pedagogical move: students live through the framework on a small build. The pivotal-moment review is where the diagnostic vocabulary from Chapter 5 (PA, PF, TO, IJ, EI) becomes muscle memory. Students who have written and reviewed Generation Logs can read transcripts of their own work with the same diagnostic precision.

This activity maps to the student book's chapter on multi-step builds. Your design here is the worked example.

---

## Common misconceptions

**"With three files, the build should run itself."** No. The three files constrain; the per-step gate remains. Architecture is upstream; the gate is the runtime.

**"I'll skip explain on familiar commands."** Step 3's `setTimeout` violation is a real example of the failure mode. Even familiar commands have surprises in the simulation domain.

**"Scope creep is fine for a single simulation."** Step 4's reset suggestion is exactly when scope creep is most seductive — the build is going well, the addition is small, the cost feels low. The cost compounds; the precedent corrodes the three-file system; the simulation stops being scoped. Log-and-decline preserves integrity.

**"Screenshot iteration is for visual designers."** It is for anyone building anything with visual output. Three rounds of attached-screenshot iteration produce dramatically better mobile layouts than 20 prompts of verbal description. The pattern is the chapter's most useful 2026 affordance for teacher work.

**"The Generation Log is bureaucracy."** It is the artifact the post-build document (Chapter 14) is written from. Without the log, the lessons of the build evaporate by Friday. With the log, they survive into the next build.

---

## Exercises

1. **(Apply)** Execute Phase 1 of your simulation build. Submit: the three files, the working simulation, the Generation Log in PROJECT.md Layer 2.

2. **(Apply)** During your build, document each pivotal moment (handoff failure, scope creep, screenshot iteration). For each, name the supervisory capacity that fired and the decision you made.

3. **(Evaluate)** At the end of Phase 1: what would you change in your three files? Edit one of them and document the change in the Generation Log.

---

## What would change my mind

The chapter's strong claim is that **the per-step gate, applied across the simulation's five steps with the three files in place, catches more dangerous-middle failures than ad-hoc per-simulation checking**. If a controlled comparison found no measurable difference in simulation quality between gate-disciplined and ad-hoc builds, the gate becomes optional. The chapter would still teach it for the supervisory-practice benefit; the urgency softens.

I expect the difference to be substantial because each of the chapter's three pivotal moments (handoff failure, scope creep, screenshot iteration) is exactly the kind of failure the discipline is designed to catch — and each would have cost an order of magnitude more to debug post-classroom than to catch during the build.

---

## Still puzzling

- **The right amount of three-file revision during the build.** Chapter 10's three files were the upfront work; this chapter's build surfaced two file additions (CLAUDE.md pattern note about Claude obeying constraints at the letter while violating them at the intent; CLAUDE.md note about screenshot-iterate pattern). The files are therefore *not frozen* — they evolve under build pressure. The chapter's working answer: revise as you go; keep the revisions in the Generation Log; sleep on big revisions.

- **The screenshot-iterate pattern's scope.** Useful for visual targets. Not useful for non-visual work. The chapter's working answer: screenshot-iterate when the output is visual; verbal-iterate (Chapter 4 handoff conditions) when the output is structural.

- **Cross-platform simulation builds.** The chapter's worked examples are macOS / Chromium. Linux is identical; Windows requires path adjustments. The framework holds; the implementations diverge slightly.

---

## Links

- [Anthropic Claude Code documentation](https://code.claude.com/docs) — the canonical reference for plan mode, `/rewind`, screenshot pasting.
- [The screenshot-iterate pattern](https://code.claude.com/docs) — 2026 documentation of attached-image specification.

---

##  AI Wayback Machine
 **Donald Schön** (1930–1997) — American philosopher and urban planner whose *The Reflective Practitioner* (1983) introduced the concept of **reflection-in-action**: the practitioner's capacity to think about what they are doing *while* they are doing it, adjusting in real time as the work surfaces conditions the upfront plan did not anticipate.[^1] Schön's argument was against the *technical rationality* model — the view that professional work is the mechanical application of pre-formed expertise to standard situations. Real professional work, Schön observed, is continuous interpretation of unique situations: the lawyer with the unusual case, the doctor with the atypical presentation, the architect with the awkward site.

The Generation Log in PROJECT.md Layer 2 is Schön's reflection-in-action made into an artifact. The Generation Log captures the reflection happening in real time during the build — which capacity fired at which moment, what the pivotal moment looked like, how the three-file constitution was updated under build pressure. The build proceeds while the reflection is recorded; the reflection feeds the next decision; the decision generates the next entry. Schön would recognize the structure: the practitioner is not applying rules to a standard simulation; they are interpreting a unique build at each step, with the Generation Log as the accumulated record of those interpretations.

Schön's most-quoted line — *"The practitioner allows himself to experience surprise, puzzlement, or confusion in a situation which he finds uncertain or unique"* — describes the Step 3 moment when `setTimeout` appeared in violation of the spec, or the Step 4 moment when Claude's scope-creep suggestion required a *not now*. The surprise is the data. The reflection is the discipline. The Generation Log is the artifact that converts the moment into capacity for the next build.

Schön wrote about teachers, doctors, planners. The framework holds for the teacher conducting Claude through a classroom simulation. The agentic loop is faster than the human's capacity to design every move in advance; reflection-in-action is the bridge between the loop's autonomy and the build's pedagogical integrity. Forty years after *The Reflective Practitioner*, the framework scales without modification.

---

## Bridge

The simulation is built. Chapter 12 takes it from *works on my machine* to *works in front of thirty students before the bell rings*.

---

[^1]: Schön, D. A. *The Reflective Practitioner: How Professionals Think in Action*. Basic Books, 1983. The 1995 paperback edition is the standard reference.
