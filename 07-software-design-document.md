# Chapter 7 — The Software Design Document: Building the Mission Before the Build

*The SDD is not bureaucracy. It is the decision that prevents you from making the same decision twice under deadline pressure.*

---

Seth opens his laptop on a Saturday morning. He has a one-sentence project description for a small side tool — *a local flicker-cue editor that lets him design and preview the light-flicker timelines for Midnight Fuel before pasting them into the Luau scripts* — and he has, in his head, what he believes is a clear picture of what he is about to build. He opens Claude Code. He starts.

Three hours later, he stops.

Not because anything has crashed. The code compiles. The page loads. He can add a cue. He can mark it ready. There is even a stub for a multi-track view, which Claude proposed in passing and which Seth accepted in passing, and which is now three files deep into the project. The thing on the screen is, by most fair measures, a flicker-cue editor. It is also, Seth realizes, not the one he set out to build.

The user model slipped. Somewhere around the second hour, Claude began treating the user as plural — "users can sign in," "users can share cue libraries" — and Seth, deep in a function and not paying full attention, accepted the change without noticing. The data model slipped with it; what was supposed to be a single JSON blob in `localStorage` is now sketched as a server-backed `users` table with a foreign-key relationship to `cues`, because at some point Claude said "you'll probably want a real backend for this" and Seth, who has built three things this semester, said yes.

The decision Seth needed to make was at the beginning, not the middle. He needed to write down, in advance, what the system was — and more importantly, what it was *not*. He didn't. So Claude made the decisions instead, one quiet drift at a time, each one defensible in isolation, each one wrong against the project Seth was actually trying to build.

This chapter is about the document Seth didn't write.

![Five stacked labeled boxes representing the SDD's five sections in dependency order: Problem Statement (load-bearing, highlighted in red), Architecture Principles, Core User Flows, User Needs, Component List. Black arrows point downward between each section. A red dashed arrow loops back from the Component List to the Problem Statement, labeled CONSISTENCY CHECK.](images/07-software-design-document-fig-01.png)
*Figure 7.1 — The SDD as decision record*

---

A **Software Design Document** is a written record of the design decisions a project depends on. That is the entire definition. It is not a project plan. It is not a feature list. It is not a Gantt chart. It is not a pitch deck. It is a record — durable, version-controlled, re-readable — of the decisions that, if changed, would change the system.

The distinction matters because students arrive at the SDD with one of three wrong mental models, and the wrong model determines whether the document does any work.

The first wrong model is SDD as paperwork. A Word doc no one reads, written to satisfy a rubric. Under this model the SDD is a tax — you pay it, submit it, forget it. The document never opens again. This is the model most students inherit from previous classes, and it is the one the rest of this chapter is built to refute.

The second wrong model is SDD as feature list. A catalog: "users can log in, users can add tasks, users can mark done, users can share, users can export." This is the failure mode that turns SDDs into wish-lists. Every entry is a thing the system *could* do, and the document has no way to refuse anything. The architecture section, if it exists at all, reads like a tagline. Six weeks later the build is twice the size it should be, half of it abandoned, the other half half-finished. The list never said *no* to anything.

The third wrong model is SDD as project plan — a schedule: week one we do X, week two we do Y. Schedules are useful but they are not what the SDD is for. The SDD answers *what we are building and what cannot change*; the schedule answers *when*. Conflating them produces an SDD that ages out of relevance the first time the schedule slips, which it always does.

The right model — the one this book uses — is **SDD as decision record**. The document captures the decisions that, if reversed, would force a redesign. Not every decision. Not the small ones. The ones whose reversal cascades. The font of the page is not in the SDD. The choice of `localStorage` over a backend is. The choice of single-user over multi-user is. The choice of "no login" as a hard constraint is. These three decisions, written down, are what would have prevented Seth's three-hour drift. Two of them were in his head when he started. None of them were in writing where Claude could read them.

The intellectual lineage of SDD-as-decision-record is short and worth naming. The IEEE 1016 standard defines an SDD as a document that "describes the design entities, their relationships, and the design decisions" of a system — format-agnostic, methodology-agnostic, and critically, not prescribing length. A one-page SDD that meets the standard is as conformant as a hundred-page one, provided the design entities, their relationships, and the decisions are all documented. The lightweight tradition the student SDD descends from is the arc42 template (Starke and Hruschka, 2005–present), open-source, designed explicitly for teams who would not write a hundred-page IEEE doc but would write twelve sections of one page each. The recent-five-years descendant of arc42 is the **Architecture Decision Record** — one decision, one page, one filename, stored in `/doc/adr/` alongside the code (Nygard 2011; Fowler 2017). Students who go on to internships will find ADRs everywhere; the SDD is the on-ramp.

One more thing about the SDD's audience, because it matters in 2026: the document has two readers. It is written for you, so you can re-read it on the second Saturday and remember what you decided on the first. It is also written for Claude, because a structured design document pasted as a prompt prefix is the cheapest tool in the toolbox for keeping Claude consistent across sessions. Without the prefix, Claude redesigns your system every time you open a new conversation. With it, Claude is constrained by what's on the page. Whether the format best for humans is also best for a language model is an open question — currently the answer appears to be yes, one document serves both readers — but the point is that the second reader is real and should be in your head when you write.

---

The SDD has five sections. They go in order. Each constrains the next.

The **Problem Statement** is the load-bearing sentence the rest of the document is built on. It says what the system does, for whom, and what makes it count as done. It is one sentence. Every other section is downstream of it; if it changes, the rest of the document changes with it.

A weak Problem Statement is unspecific: "An app for game cues." It names no user, no done-condition, and cannot be refuted by any future feature, because every conceivable feature is "for game cues." A strong Problem Statement is specific enough that you can name features that *violate* it: "A local-only timeline editor for one developer to design today's set of light-flicker cues for Midnight Fuel without a login." The phrase *local-only* refuses a backend. The phrase *one* refuses multi-user. The phrase *without a login* refuses authentication. The phrase *today's set of light-flicker cues* refuses a shared cue library. Each refusal will matter later. None of them are visible in the weak version.

The **Architecture Principles** are the design decisions that constrain everything downstream and that, under pressure, *win*. Three is the right number at student scale; fewer than three usually means you haven't thought it through, more than five usually means you've started writing taglines.

A principle is not a value. "Clean code" is a value; it is not a principle, because nothing in the project ever defends a decision by saying "actually, we want our code to be dirty here." A principle has to be able to *lose* to another principle under pressure. If two principles cannot collide under any realistic decision, at least one of them is a slogan.

This is the **Principle Collision Test**, and it is the most useful diagnostic in this chapter. Take any two of your Architecture Principles. Construct a realistic situation in which they would tell you to do different things. If you can, both are real principles. If you can't, one of them is a tagline, and you should rewrite it until the collision is visible. In Seth's flicker-cue editor, *local-only storage* and *sync across devices* would collide; the SDD makes the decision in advance — local-only wins, sync loses — and that decision is not re-litigated at 11 p.m. three hours into a build. The Principle Collision Test is, in software-engineering vocabulary, an information-hiding discipline in the sense Parnas (1972) intended: the right criterion for decomposing a system is not its process flow but the decisions each component is concealing. Architecture Principles, written down, are the project-level version of that move.

The **Core User Flows** are the verbs the user performs in order. They are not features. They are not screens. They are the path through the system, from arrival to outcome, as the user experiences it.

A weak Core User Flow is exhaustive: "users can add, edit, delete, archive, share, export, search, filter, tag." This is a feature catalog dressed as a flow. A strong Core User Flow names the one path that matters, in order, with as few steps as the work allows: "add task → mark done → see today's list." The strong version is testable; you can sit a real student down and watch whether the three steps produce the outcome. Most projects have exactly one core flow that, if it works, the project works. Find it. Write it. Trim the rest to one line of secondary flows, if any, and stop.

The **User Needs** section is the outcomes the user must get, written as testable conditions. This is where most student SDDs collapse, because the temptation to write feature descriptions is overwhelming and the discipline to write outcomes is unfamiliar.

A weak User Need is a feature: "should have a multi-track view." A weak User Need is also a value: "should be fast and easy." A strong User Need is a testable outcome: "today's cue list renders in under 2 seconds on a laptop browser." It names a condition, an environment, and a threshold. It is decidable — you can sit at a laptop with a stopwatch and the question is answered. It is also falsifiable: if the render takes three seconds, the need has *failed*, and you have the diagnostic on the spot. Three strong User Needs is the right shape for a student SDD. They are the conditions under which the project is *good*, and they are the conditions a project's success score can be measured against. Without them, you are measuring effort against a moving target, which is to say measuring nothing useful.

The **Component List** is the buildable parts of the system, named at the right level of granularity. It is the last section of the SDD, not the first, because every other section constrains what belongs on it.

A weak Component List is generic: "frontend, backend, database." These are not components; they are architectures-in-the-large. A strong Component List names the specific pieces this specific project needs: "input form, localStorage wrapper, list renderer, today-filter. No backend." Note the *no backend* — the Component List can name things the system is *not* going to have, and at student scale this is often where the document earns its keep, because it forecloses temptations that will arise mid-build.

The Component List is also where the SDD's consistency check fires. Walk back up the document: can these components, under these principles, supporting these flows, satisfy these User Needs, deliver this Problem Statement? If you cannot answer yes all the way up, something is wrong, and the answer is to revise upward — change the components, or discover that the upstream sections were over-promising.

| Section | What it does | Weak version | Strong version |
|---|---|---|---|
| Problem Statement | Names the one problem this build solves, for whom. | "Build a study tracker." | "A single high-school senior tracking AP-class study hours by week, on one laptop, with no syncing." |
| Architecture Principles | Names the 2–4 invariants the build will not violate. | "Clean code." | "Local-only. No backend. localStorage as the source of truth. Fail visibly on storage corruption." |
| Core User Flows | Names the 2–3 sequences the user actually performs. | "User uses the app." | "Add a session → see it in today's list → toggle complete → close laptop → next day, today's list resets." |
| User Needs | Names what the user must be able to do, *at a glance*. | "Easy to use." | "See remaining sessions for today without scrolling. Recover from corrupted storage without losing prior days." |
| Component List | Names the specific pieces (and explicit non-pieces). | "Frontend, backend, database." | "Input form, localStorage wrapper, today-filter, list renderer. *No backend. No multi-day history view in v1.*" |

---

The SDD has five sections, but it has one load-bearing claim, and that claim is the Problem Statement. Everything else is downstream. If the Problem Statement is wrong — unspecific, or two-systems-disguised-as-one, or unanchored to a real user — the rest of the document inherits the wrongness, and no amount of careful work on Architecture Principles or User Needs will fix it.

The structural test for whether a Problem Statement can hold the weight has three parts. Does the sentence name a single system? If there is an *and* connecting two things the system does, the sentence is two Problem Statements; pick one. Does the sentence name the user specifically? "One Midnight Fuel developer" passes; "game developers" does not. The named user is what makes the User Needs section testable, because you can ask whether *this* user can do *this* thing under *these* conditions. Does the sentence name the done-condition? "Without a login, in under two seconds, on a laptop browser" is a done-condition; "easy to use" is not.

The three tests are checkable in under a minute. Run them before writing anything else. If your sentence fails any one of them, the SDD cannot begin, because the document is one sentence elaborated five ways — Architecture Principles refine the constraints the problem statement implies; Core User Flows refine the path it promises; User Needs refine the done-condition it names; Component List refines the system it defines. The document is not five independent sections. It is one sentence, five times. If the sentence is wrong, all five elaborations are wrong from the top.

---

How much SDD is enough?

The honest answer is we don't currently know empirically what the floor is at high-school scale. The industrial evidence establishes that some SDD is much better than none — Info-Tech's roughly 50% rework rate attributable to requirements issues, the NASA Mars Climate Orbiter case where unwritten interface assumptions cost $327.6 million — but does not tell us how many sections are enough. This chapter takes a defensible position: at student scale, five sections is the minimum that does the work, and the five are the ones listed above.

Each section does work that cannot be done by another section. Cut the Problem Statement and the document floats, anchored to nothing. Cut the Architecture Principles and the document cannot refuse anything; every feature is in scope. Cut the Core User Flows and the document does not describe a path through the system, only a pile of capabilities. Cut the User Needs and the document cannot be tested; "done" becomes a feeling. Cut the Component List and the document does not connect to the code; nothing on disk has to match anything in the doc.

For a 200-line script, five sections may feel like overkill. It is not; the five sections will fit on one page. The shape that is overkill at this scale is a thirty-page IEEE 1016-conformant document with appendices. Don't write that. Write the five sections, one page total, and *use* the document — paste it into Claude at the top of every session, reread it before each build, revise it deliberately when a decision changes.

---

Here is a complete minimum viable SDD for Seth's project, after the rebuild. The system is a flicker-cue editor — a browser tool Seth uses during Midnight Fuel sound-and-lighting passes to lay out the timing of a scene's flicker cues before pasting them as a Luau table into the game. This is the document that would have prevented the three-hour drift in the chapter's opening. It fits on one page.

---

**SDD: Flicker-Cue Editor**

**Problem Statement.** A local-only timeline editor for one Midnight Fuel developer to lay out today's set of light-flicker cues in order, mark each cue ready, and copy the final timeline as a Luau table, without a login and without leaving the browser tab.

**Architecture Principles.**
1. *Local-only storage.* All data lives in `localStorage`. No server, no backend, no auth. If `localStorage` is unavailable, the app fails visibly with one sentence of explanation.
2. *Single user.* The app assumes one person is using one browser. No accounts, no multi-device sync, no shared cue library.
3. *Fail visibly.* All errors render to the page as readable text. Nothing is logged silently. If the app cannot save, the user sees that on the screen within one second.

**Core User Flows.** Open the page → add today's flicker cues as a list with timestamps → mark each cue ready as the timing settles → optionally tag a cue as "needs-retiming" with one line of text → close the tab. The next morning, open the page; yesterday's cues are gone (intentionally — the system shows *today's* working set, not a history); add today's.

**User Needs.**
1. The timeline renders in under 2 seconds on a laptop browser with 4 GB of RAM, measured from page load to first interactive input.
2. No data is lost during a single day's session; closing and reopening the tab within the same day restores the list to the state it was in when the tab was closed.
3. The page is usable with one hand on a phone-sized screen (375px wide); all buttons are at least 44 pixels tall and reachable by thumb.

**Component List.** Input form (one text field, one timestamp field, one add button); `localStorage` wrapper (read, write, clear-on-new-day); list renderer (renders cues with ready/needs-retiming states); today-filter (clears yesterday's data on first open after midnight); error banner (renders to the top of the page on any caught exception); Luau export (serializes today's ready cues as a Luau table string). No backend. No router. No build step beyond a single HTML file with inline CSS and JavaScript.

---

Read it as a whole. Notice what's there and what's not.

What's there: every decision in the document is decidable. Each Architecture Principle could collide with a tempting future feature — sync across devices, user accounts, silent retry on save failure — and the document tells you which one wins. Each User Need is a stopwatch-and-laptop test; no ambiguity. The Component List names six components, each at the right scale for a student build, and explicitly refuses three things — no backend, no router, no build step — that Claude would otherwise quietly propose.

What's not there: a schedule, a font choice, a list of stretch features, an explanation of why this is useful, a glossary, an appendix. The document is one page, dense, decisional, re-readable in ninety seconds.

Now run the Principle Collision Test. *Local-only storage* vs. *single user* — do these collide? Not really; they reinforce each other. *Local-only* vs. *fail visibly* — yes: if `localStorage` is full, *fail visibly* says show the user, but *local-only* says don't fall back to a server. The collision produces: tell the user we can't save, ask them to clear some space. *Single user* vs. *fail visibly* — yes: if the same tab opens twice with overlapping writes, the collision produces: tell the user another tab is using this data. Each pair yields a defensible decision the document has already made. The principles are real, not slogans.

Now run the back-arrow consistency check. Can these six components, under these three principles, supporting this Core User Flow, deliver this Problem Statement? Open the page (input form + list renderer) → add today's cues (input form + `localStorage` wrapper) → mark each ready (list renderer + `localStorage` wrapper) → tag needs-retiming (input form + list renderer) → export to Luau (Luau export) → close the tab (browser does this for free) → next morning today-filter clears yesterday's data on open. The Problem Statement delivers. The User Needs are testable against this build. The document is consistent.

This is what Seth pastes into Claude at the top of his next session. Claude will not propose `users.email` as a column. Claude will not propose a shared cue library. Claude will not propose authentication. The SDD has refused them in writing, and the model is constrained by what's on the page. The eleven minutes Seth spent writing the SDD are the most expensive eleven minutes of the build and the most cost-saving.

---

The SDD unlocks two things the rest of the book depends on.

The first is a trustworthy project score. Any score measuring the ratio of effort on the right work to effort on misformulated work requires a fixed point — a *what we are building* — to measure against. Without the SDD, the fixed point is in your head, and your head changes between Saturdays. Every Claude session can quietly redefine the problem, and the score ends up measuring effort against a moving target, which is measuring nothing useful. With the SDD, the fixed point is on disk. Drift becomes visible — this commit moved away from the SDD's Architecture Principles, here is the diff — and correctable.

The second is prompt prefix discipline, the topic of Chapter 8. A Claude prompt is more trustworthy when it begins with the SDD, because the SDD constrains the model's solution space. Without it, Claude is free to redesign your system in every prompt; with it, Claude has to argue against the document, which it will rarely do unprompted. The SDD is currently the cheapest way to keep Claude consistent across sessions. It is also the document you will revise *when* you decide to redesign — which is the point. Redesign is fine; redesign you didn't notice is not.

There is a third thing the SDD unlocks, and it is the one that matters in the long run. The act of writing the SDD is the act of deciding in advance — of carrying the system in your head clearly enough to put it on paper. This is the cognitive work the book's thesis is built on. The work that, if delegated to Claude, leaves you with output but no capability. The student who writes the SDD builds the capability. The student who skips it polishes output. By the end of the semester, the difference is not visible in any single commit. It is visible in the *next* project, when the first student writes the next SDD in twenty minutes and the second student stares at a blank page.

---

## 🕰️ AI Wayback Machine

**Ada Lovelace** (1930–2002), Dutch computer scientist, made one claim that the SDD is the descendant of: *program correctness is a property of design, not of testing.* In "Notes on Structured Programming" (EWD249, 1970/1972) and more fully in *A Discipline of Programming* (1976), Dijkstra argued that programs should be *derived* from specifications — that the act of programming is the construction of a proof of correctness, and code is the residue, the last step. His most-quoted line — "Program testing can be used to show the presence of bugs, but never to show their absence" — is a structural argument: if correctness is built in at design time, testing confirms; if it is not, testing cannot fix what was decided wrongly upstream. The SDD is the student-scale form of this stance. The document is where the project's correctness as a design property is recorded — before any code, before any test, before Claude is asked to produce anything. The five-section minimum is not Dijkstra's proof calculus; it is the student version of his discipline: decide what the system is, in writing, before the system exists in code.

**

![Ada Lovelace, 1843. AI-generated portrait based on a public domain engraving.](../images/ada-lovelace.jpg)
*Ada Lovelace, 1843. AI-generated portrait based on a public domain engraving (Wikimedia Commons).*

Run this:** Paste this prompt into Claude. *"Read EWD249, section 1, from the E.W. Dijkstra Archive at UT Austin (cs.utexas.edu/~EWD/). In one sentence, restate Dijkstra's claim about why testing cannot establish correctness. Then apply that claim to the SDD in Chapter 7 of Claude Code for Students: which section of the SDD does the work that testing cannot do, and why?"*

---

**Links:** [boondoggling.ai](https://boondoggling.ai) · [irreducibly.xyz](https://irreducibly.xyz)

**References (in-chapter citations):**

- Dijkstra, E. W. (1970/1972). Notes on structured programming. EWD249. E.W. Dijkstra Archive, University of Texas at Austin.
- Dijkstra, E. W. (1976). *A Discipline of Programming*. Prentice-Hall.
- Fowler, M. (2017). Patterns for managing source code branches. *martinfowler.com* (Architecture Decision Records entry).
- IEEE Std 1016-2009. *IEEE Standard for Information Technology — Systems Design — Software Design Descriptions*. IEEE.
- Nygard, M. T. (2011). Documenting architecture decisions. *thinkrelevance.com* (blog post, November 15, 2011).
- Parnas, D. L. (1972). On the criteria to be used in decomposing systems into modules. *Communications of the ACM*, 15(12), 1053–1058.
- Starke, G., & Hruschka, P. (2005–present). arc42: Template for architecture communication and documentation. *arc42.org*. Creative Commons license.

---
