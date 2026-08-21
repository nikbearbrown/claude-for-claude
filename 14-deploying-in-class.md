# Chapter 12 — Deploying in Class: From Build to Lesson


## TL;DR

- This chapter gives a working overview of Deploying in Class: From Build to Lesson, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, Deployment options, The three-check verification protocol, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> The simulation works in your terminal. Now it has to work with thirty students who have never seen it. This chapter is about the gap between *it works* and *it teaches*.

---

## Learning outcomes

1. **(Apply)** Deploy a classroom simulation as a shareable web artifact students can access without setup.
2. **(Analyze)** Identify the gap between *works in terminal* and *works in classroom* using the three-check verification protocol.
3. **(Apply)** Conduct a revision build based on observed classroom failures, using the same conducting framework that produced the original build.

---

## Opening

The teacher projected the sorting simulator on the classroom screen for the first time.

Twenty-eight ninth-graders. First period, Monday morning. The simulation loaded. The colors looked right on the projector. The teacher clicked through one bubble-sort pass to demonstrate. The students watched.

Then the teacher said: *"Open the URL on your Chromebook. Try to predict what happens when the array is reverse-sorted."*

The room got busy. URLs typed. Pages loading. Then questions.

*"How do I make it go?"* — three students at once. The simulation had loaded but the array was at its starting state with no obvious affordance. The teacher had built the simulation knowing single-click stepped forward; the students did not know this.

*"It's not doing anything."* — a student in the back. The simulation had registered the click on her Chromebook, but the touch handler had not fired because the JavaScript only listened for `click`, not for `touchstart`. The Chromebook's touch screen produced a tap-then-click sequence that the simulation handled badly.

*"Mine looks weird."* — a student in the front. His Chromebook was an older model with a smaller screen. The mobile breakpoint that the teacher had tested at 375px width was firing at his screen's 800px width because of a difference in device-pixel-ratio reporting.

Five minutes in, three failures. The teacher had a choice. Hide the failures. Or use them.

This chapter is about both — preventing the failures that can be prevented (the three-check verification protocol the day before class), and conducting the revision when a failure surfaces in front of students (the revision-build cycle that produces a better simulation by next class). Both halves are the framework applied to deployment. The discipline is the same; the surface is different.

![The student-facing verification pass](images/14-deploying-in-class-fig-01.png)
*Figure 14.1 — The student-facing verification pass*

---

## Deployment options

Before the verification protocol: how the simulation reaches the students' devices.

**Claude Artifacts.** Fastest path to a shareable URL. From your Claude.ai workspace (or from Claude Code via the Artifacts MCP tool), generate the simulation as an Artifact. Claude produces a URL that opens the simulation in the student's browser without any installation. Best for one-off lessons or for prototypes you will revise after observation. Limitation: the URL is tied to your Claude session; for stability across the semester, prefer GitHub Pages.

**GitHub Pages.** Free hosting for static HTML/CSS/JS. Push the simulation to a public repo; enable GitHub Pages on the main branch; the simulation is live at `https://<your-username>.github.io/<repo>/`. Customizable URL via custom domain. Persistent across semesters. Best for the simulation you will return to.

**Class website integration.** From Chapter 3 onward you have a class website. The simulation can live as a page within it: `https://classes.example.edu/cs-101/sims/sorting/`. The deployment is your existing rsync (Chapter 3); the simulation's static files go into `src/sims/sorting/`. The advantage is single-domain coherence with the rest of your class materials.

For the chapter's examples, GitHub Pages is the default. It is free, public, persistent, and the URL stays stable across the school year. Claude Artifacts is the right tool when you need a same-day prototype; the class website integration is the right tool when the simulation becomes part of the course's stable infrastructure.

---

## The three-check verification protocol

Total time: 20–30 minutes the day before class. Done once per simulation; re-done after any significant revision.

### Check 1: the first-30-seconds test

A student who has never seen this simulation, and who knows nothing about how it was built, opens the URL. Can they figure out what to do in 30 seconds *without any instructions*?

The chapter-opening failure (*"How do I make it go?"*) is exactly this failure. The teacher knew single-click stepped forward; the students did not. The simulation needed an affordance — a visible hint, a brief intro overlay, a labeled button — that communicated the starting interaction.

**Protocol.**

1. Find a non-student who knows nothing about your simulation — a colleague who teaches a different subject, a family member, a friend who is not a teacher.
2. Sit them in front of the URL. Say nothing about how the simulation works.
3. Start a 30-second timer.
4. Observe. Do not help.
5. After 30 seconds, ask: *"What did you think you were supposed to do?"*

If they figured it out, the affordance is sufficient. If they did not, the simulation needs explicit on-screen guidance. The fix is usually small: a `<p>` with *"Click the array to step forward, long-click to step back"*, a flashing arrow on first load, a brief overlay that dismisses on first interaction. The 30-second test catches it before the classroom does.

### Check 2: failure states — informative or broken?

When something goes wrong in the simulation, does the student get useful information, or does the simulation just stop?

Examples of *broken* failure states:

- The simulation freezes; no error message; no recovery path.
- A button click does nothing; no visual feedback; no console message.
- The page renders empty; no indication of what failed or why.

Examples of *informative* failure states:

- *"This array is already sorted; nothing to step through. Reload to try a different input."*
- *"Long-click not yet implemented for this control; use the keyboard arrow keys."*
- A loading spinner that times out into a clear message: *"Couldn't load the simulation. Try refreshing."*

**Protocol.**

1. Identify the failure modes your simulation has — what could go wrong? (Wrong input, missing data, browser quirk, user interaction the simulation does not handle.)
2. For each, deliberately trigger the failure (or simulate it by editing the code temporarily).
3. Observe what the user sees. Does it tell them what happened and what to do?
4. Where it does not, add the informative state. A short message; a visible affordance for recovery.

The discipline: every failure mode you can think of should produce informative output. The dangerous middle of deployment is the failure mode you did not think of — caught by Check 1 (a student does something you did not anticipate) or by classroom observation.

### Check 3: easy-to-hard progression

Can a student start with the simplest possible use and gradually engage with more complex aspects? Or does the simulation force the student into the deep end immediately?

For the sorting simulator: the easiest use is *click through the sort on a small array, watch the bars move*. The harder uses are *change the input to reverse-sorted and predict the comparison count*, *change to already-sorted and observe the best case*, *predict where the algorithm will spend most of its time*.

If the simulation supports only the hardest use (it requires the student to enter a specific input format, then specify parameters, then interpret a complex display), the simulation has no easy entry. Students who would have engaged on click-through-and-watch never get to the harder uses because the entry costs too much.

**Protocol.**

1. Identify the three uses of your simulation, from easiest to hardest.
2. Verify the easiest use works without setup. The student opens the URL; they can do the easiest thing in one click.
3. Verify the path from easy to hard. The student who succeeds at the easiest use can discover the next use within the simulation, without external instructions.

This is Resnick's *low floor, wide walls, high ceiling* principle applied to deployment. The floor is the easiest entry. The walls are the variety of paths the student can take from there. The ceiling is the most complex use the simulation supports. All three need to be reachable from the URL without setup.

---

## Graceful failure design

The most important deployment property of a classroom-facing tool. The pattern:

1. **Detect the failure mode before it happens** — check the inputs, the browser capability, the resource availability before invoking the operation that depends on them.
2. **Produce a user-readable message** — plain language, specific, names the issue and the next action.
3. **Provide a path forward** — a button, a reload, a fallback mode. The student should never be stuck without an option.
4. **Fail loudly to you, quietly to the student** — log the technical detail (for your revision) to a console or analytics endpoint; show the student only what they need.

For the sorting simulator's three opening failures:

- *Touch-handler missing on Chromebook.* Detection: feature-detect touch events on load; warn if both are needed but the simulation only listens for click. User message: *"This simulation works best with a mouse or trackpad. Touch screens are partially supported."* Path forward: the click events do work; the message tells students to try clicking with their finger sensitively. Revision build: add `touchstart` listeners alongside `click`.

- *Mobile breakpoint firing at wrong screen size.* Detection: log the actual screen dimensions on load. User message: *"This simulation reflows for small screens; resize your browser if the layout looks wrong."* Path forward: resize. Revision build: tighten the media-query threshold or use container queries instead of viewport queries.

- *No affordance for first interaction.* Detection: timer that fires if no interaction occurs in 5 seconds. User message: *"Click any bar to step the algorithm forward."* Path forward: the message *is* the affordance. Revision build: add a permanent on-screen hint.

The discipline: every failure mode produces a teacher-readable log (for your revision) and a student-readable message (for their recovery). The simulation never silently degrades.

---

## The revision-build cycle

The framework applied to classroom-observed failures.

**Step 1: observe.** During or immediately after the class, note what failed. Specifics matter. *"Three students could not figure out the first interaction."* — useful. *"Some students had trouble."* — not useful for revision.

**Step 2: interrogate.** Open Claude Code in plan mode. *"I deployed the sorting simulator in class. Three students could not figure out the first interaction. The simulation has the controls visible but no on-screen explanation of how to use them. What are options for making the first interaction discoverable without adding clutter?"* Claude returns options.

**Step 3: decide.** Read the options. Pick one. Add the decision to PROJECT.md Layer 1 (Intent Layer) — *"The simulation must have on-screen guidance for the first interaction; the guidance can be dismissed after first successful interaction."* The Intent Layer just grew because the classroom revealed a missing intent.

**Step 4: respecify.** Update the relevant Step's specification from Chapter 11 with the new constraint. *"Add an unobtrusive overlay on first load that explains: single-click to step forward, long-click to step back. The overlay dismisses on first successful interaction."*

**Step 5: re-suggest.** Run the build through Explore → Plan → Implement → Commit. The revision is a small change; the gate is the same gate.

**Step 6: re-verify.** Run the three-check protocol again on the revised version. Deploy. Use in class the next day.

The cycle takes 30–60 minutes per observed failure. Each iteration leaves the simulation more reliable for the next class. By the third deployment, most simulations have stabilized; the revision rate drops sharply.

---

## Showing students the fix

A pedagogical move worth naming.

When the simulation fails in front of your students — and at some point it will — you have a choice. Hide the failure. Or use it.

The chapter's recommendation: use it. Open the terminal in front of the class. Open Claude Code. Walk through the interrogation aloud. Walk through the plan-mode review. Switch out of plan mode. Watch the agentic loop run. Reload the simulation. Verify the fix.

The lesson is meta. The students see what conducting looks like in real time. The same discipline the simulation teaches them about sorting algorithms is the discipline they watch you use to build the simulation. The visibility of the framework — demonstrated under the pressure of a failed live deployment — is one of the most pedagogically valuable things you can do with the simulation across the unit.

The teacher who hides the failure teaches that failure is shameful. The teacher who narrates the fix teaches that failure is information. The latter is the lesson the framework exists to model.

---

## Teacher build: deploy your simulation

Apply the chapter's discipline to your simulation from Chapter 11.

1. **Choose a deployment.** Claude Artifacts for fastest; GitHub Pages for stable; class website for integration. The chapter recommends GitHub Pages as the default.

2. **Run the three checks.** Recruit a non-student for the first-30-seconds test. Trigger your simulation's known failure modes and verify the failure states are informative. Walk through your easy-to-hard progression.

3. **Add graceful failure messages** wherever Check 2 surfaced a broken failure state. Each one is a few minutes of work.

4. **Compose Launcher Deployment Notes.** One page. Required dependencies (browser version, any installed plugins). The URL. The known failure modes and their fixes. A note for substitute teachers about how to start the simulation.

5. **Deploy in class.** Observe. Document failures during the class (a notes app open on your phone).

6. **Conduct the revision build.** Same day if possible; before the next class certainly. Update PROJECT.md Layer 1 if the classroom revealed a missing intent.

The deployment-and-revision cycle takes 90–180 minutes for the first deployment. Subsequent deployments of the same simulation (after a revision build) are 30–45 minutes. The third deployment usually stabilizes.

---

## Classroom activity: students help debug a deployment failure

A meta-lesson worth offering once.

When your simulation fails in front of students (and it will, at least once), tell the students: *"the simulation had a problem just now. I want to show you how I'd fix it. We'll work through it together."*

Open Claude Code on the projector. Walk through the revision-build cycle aloud — observe, interrogate, decide, respecify, re-suggest, re-verify. Re-deploy. Use the revised simulation immediately to continue the lesson.

The students who watch this once have seen the framework as a *practice*, not a recipe. The transfer to their own use of AI tools is direct. They are more likely to ask follow-up questions of AI, more likely to demand explanations, more likely to revise their prompts when the first output is not right — because they have seen their teacher do exactly that, in real time, under classroom pressure.

This activity does not map directly to the students book; it is a teacher-specific demonstration. The students book's chapters on supervisory capacities and on the dangerous middle benefit from this demonstration having happened — students read those chapters with embodied understanding because they have seen the framework live.

---

## Common misconceptions

**"If it works on my machine, it'll work in class."** No. The chapter-opening failures are exactly this misconception in action. Three checks; 20 minutes; the alternative is the failed lesson and the parent emails.

**"Pre-deploy testing is overkill."** Three checks. Twenty minutes total. One failed class is fifty minutes of student time plus the recovery cycle plus the trust cost. The math favors the checks.

**"Failures in class are embarrassing."** They are the most valuable pedagogical moments the simulation produces. Showing students the fix teaches the framework better than any other lesson in the unit.

**"I'll fix it during class if something breaks."** Don't. Fix in advance with the three checks; observe in class for revisions; conduct the revision build between classes. The mid-class panic-fix produces worse outcomes than the next-day revision-build.

**"The simulation should be perfect before deployment."** No simulation is perfect. The first deployment teaches what the three files missed. The revision build is the framework's response. Embrace the cycle.

---

## Exercises

1. **(Apply)** Run the three-check verification on your simulation. Document each failure and fix. Update PROJECT.md Layer 2's Generation Log.

2. **(Analyze)** Examine your simulation's failure outputs. For each, classify: graceful (clear message, path forward) or broken (silent, no recovery). For broken cases, add graceful messages.

3. **(Apply)** Deploy your simulation in one class period. Document one observed failure. Conduct the revision build the same day. Re-deploy in the next class period.

---

## What would change my mind

The chapter's strong claim is that **the three-check verification protocol materially reduces classroom-deployment failures** compared to ad-hoc pre-class testing. If a controlled comparison found no measurable difference in first-class failure rates between teachers using the protocol and teachers testing informally, the protocol becomes optional. The chapter would still recommend graceful-failure instrumentation; the protocol's urgency softens.

I expect the difference to be substantial because the three checks address the three modal classroom-deployment failures (first-interaction confusion, broken failure states, missing easy entry), and each is hard to catch by ad-hoc testing on the teacher's own machine.

---

## Still puzzling

- **Chromebook-specific deployment.** Many K-12 classrooms are Chromebook-heavy. Touch-event handling, restricted browser features, low device-pixel-ratio reporting are common quirks. The framework transfers; the specifics differ. A district-IT-specific deployment appendix would be useful future-edition content.

- **The "demonstrate the fix" lesson's frequency.** Once per simulation per deployment cycle. Over-using removes the freshness; under-using misses the meta-lesson. The chapter's working answer: once per significant revision, narrated to the class.

- **Cross-school sharing.** A simulation built for your school can be useful in another school's CS curriculum. The deployment URL is shareable; the PROJECT.md Layer 1 explains what the simulation is for. The governance model (attribution, revision, forking) is open. The chapter's working answer: share when the simulation is stable; expect adoption to surface revision opportunities you would not have noticed alone.

---

## Links

- [Claude Artifacts documentation](https://claude.com) — fastest path to shareable URL.
- [GitHub Pages documentation](https://pages.github.com) — free hosting for static simulations.

---

##  AI Wayback Machine
 **Maria Montessori** (1870–1952) — Italian physician and educator whose method, articulated in *The Montessori Method* (1912) and elaborated through forty years of practice, was built on a principle she called **control of error** — that educational materials should be *designed so the student can identify and correct their own mistakes without teacher intervention*.[^1] A Montessori knobbed cylinder set has cylinders of graded sizes that fit into matching holes; if the child puts a cylinder in the wrong hole, the last cylinder will not fit. The material announces the error. The child re-does the work. No teacher correction; no shame; the error becomes information.

![Maria Montessori](../images/maria-montessori-b57.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

The classroom-deployment work is Montessori applied to teacher craft. The simulation's graceful-failure messages are the cylinder set's *last-cylinder-doesn't-fit* moment — the simulation announces what is wrong and what to do about it, without the teacher having to mediate. The three-check protocol is the prepared environment from *The Absorbent Mind* — the work the teacher does *before* the students arrive, so that the environment supports the learning rather than obstructs it. Montessori's framing was for the child's environment; the chapter's framing is for the teacher's classroom tool. The principle is the same: design the artifact so the failure mode is informational and recoverable, not catastrophic.

Montessori's most enduring contribution was the recognition that *learning happens in a prepared environment* — and that the preparation is the teacher's most important work. The chapter's three-check protocol is exactly this preparation. The simulation is the prepared environment; the deployment verification is the preparation; the graceful-failure messages are the control of error that lets the simulation serve the lesson rather than disrupt it. A century after *The Montessori Method*, the principle scales to AI-built classroom tools without losing any of its force.

---

## Bridge

The simulation is deployed. Chapter 13 maps what your students are reading in *Claude Code for Students* to what you now know how to teach.

---

[^1]: Montessori, M. *The Montessori Method*. Frederick A. Stokes, 1912. See also Montessori, M. *The Absorbent Mind*. Henry Holt, 1949. The control-of-error principle is articulated across her work; the 1912 *Method* is the standard early reference.
