# Chapter 13 — Verification: How You Know It Works

*The build is done when it passes the handoff conditions — not when Claude says it's done.*

---

It is late Saturday afternoon. Phase 1 of the application tracker is done. Five rows on the score, all green. `npm test` reports nine of nine passing. The page loads. I can add an application, toggle it submitted, delete it, refresh the browser, and the applications come back. The handoff conditions I wrote in Chapter 11 — *three tests pass on an `Application[]` of length 0, 1, 3*; *refresh preserves state across 10 toggles*; *parse failure triggers the confirmation dialog* — all run, all pass. I am about to type the commit message and call Phase 1 finished.

I do one more thing first. I open the SDD I wrote at the beginning of the build and read it out loud. Not the bullet points I extracted into the score — the full prose. Section 2.3, *User Needs*, has a sentence I had stopped looking at by row 3: *The user should be able to see at a glance which applications remain to submit and which are already in, without scrolling.*

I look at the running build. Six applications visible, three submitted with strikethrough, list in insertion order — newest at the bottom. If I use the app across the November–January application window, the schools I still have to submit to will be scattered through the list, mixed with the ones I sent off in October. *At a glance* fails. Not because the code is wrong. Because the spec said *what the user needs* and I built *what the score asked for*, and the score asked for `addApplication`, `toggleSubmitted`, `deleteApplication` — not *display order that surfaces what remains*.

The failure is not a bug in the code. It is a gap between the SDD's prose and the score's rows. The handoff conditions I wrote are all satisfied. The need the SDD named is not.

This is the chapter. The build passes its tests; the build does not pass its needs. Tests are a proxy for needs; sometimes the proxy is wrong. Verification — real verification — is the discipline of checking the build against the needs, not against the proxy.

![Three vertically stacked verification passes — Pass 1 functional and Pass 2 edge case marked with gear icons, Pass 3 SDD needs marked with a human-eye icon and bordered in red. Each pass branches right on failure to one or two resolution boxes (fix code, amend SDD). Amend-SDD boxes are red-bordered with a loop-back arrow returning the sequence to Pass 1.](images/13-verification-fig-01.png)
*Figure 13.1 — The verification sequence: three passes, two resolutions, one loop-back*

---

The most common verification mistake — the one fluent Claude Code users make by reflex, me included — is to treat *the test suite passes* as a synonym for *the build is done*. It is not. Tests verify code against tests. A build is done when it verifies against *the needs the SDD named*, which is a strictly larger claim.

Tony Hoare made the formal version of this argument in 1969.[^hoare] A program is correct *with respect to a specification*, never in the abstract. Correctness is a relation between code and spec, not a property of code alone. Five years later, Barbara Liskov and Stephen Zilles operationalized Hoare: an abstract data type is defined by the operations it admits and the relations those operations preserve, not by its representation.[^liskov74] The post-conditions are the spec. The tests are evidence about the post-conditions. The code is correct only when, for every input the spec defines, the post-conditions hold.

[^hoare]: C. A. R. Hoare, "An Axiomatic Basis for Computer Programming," *Communications of the ACM* 12, no. 10 (October 1969): 576–580. The Hoare triple {P} C {Q} is the formal statement of "code is correct only with respect to a specification."

[^liskov74]: Barbara H. Liskov and Stephen N. Zilles, "Programming with Abstract Data Types," *ACM SIGPLAN Notices* 9, no. 4 (March 1974): 50–59. The argument that the type is defined by its post-conditions, not its representation, is the foundation of behavioral verification.

The 2026 version of the problem is sharper because Claude can write the tests too. If the model that read the spec also writes the tests, both sides may share the same misreading. The tests pass because the code matches the tests — and the tests match the wrong reading of the spec. The loop is self-consistent and wrong. The 2024–2025 LLM-coding literature documents this as the dominant agentic-coding failure mode: code passes its self-generated tests at high rates while failing held-out, human-written tests at much lower rates.[^llmcode] The gap between those two numbers is the gap between *tests pass* and *needs met*.

[^llmcode]: Recent empirical work on agentic code evaluation finds pass@k rates inflate 10–61% when tests are drawn from the same generation event as the code — sometimes called *evaluation contamination*. See SWE-bench-Live and LiveCodeBench (2024–2025), which use post-cutoff problems specifically to break this loop. The pattern is the LLM analogue of the older problem Just et al. (2014) documented for human-written tests: line coverage and assertion count are not adequate proxies for fault detection.

Two real failures make the abstract argument concrete. Both happened because verification ran against tests, not needs.

The **Mars Climate Orbiter** burned up in the Martian atmosphere on September 23, 1999.[^mco] Lockheed Martin's ground-side software produced thruster impulse data in pound-seconds; JPL's trajectory model consumed it as newton-seconds. Every unit test on each side passed. Each module was correct against its local spec. The system was wrong against the mission's actual need — deliver a spacecraft to Mars orbit — because no test in either codebase tested *the units agree across the interface*. The test that would have caught it required reading both specs and noticing the units differed. That is a Pass 3 question. No proxy could have caught it.

[^mco]: Mars Climate Orbiter Mishap Investigation Board, *Phase I Report* (November 10, 1999), NASA. SM_FORCES output thruster impulse in pound-seconds; the trajectory model expected newton-seconds. Each subsystem passed its own tests. No verification step checked the unit contract across the interface.

The **Therac-25** medical linear accelerator delivered massive radiation overdoses to six patients between 1985 and 1987; at least three died.[^therac] The cause, as Nancy Leveson and Clark Turner documented in 1993, was a race condition on shared variables — a fault that survived unit testing because each unit behaved as designed. The catastrophe lived in the integration. Leveson's verdict has been the dominant teaching example in safety-critical CS education for thirty years: the software's unit behavior was correct; the system's behavior in use was lethal. Verification against the SDD's safety needs — not against the unit suite — was the missing pass.

[^therac]: Nancy G. Leveson and Clark S. Turner, "An Investigation of the Therac-25 Accidents," *IEEE Computer* 26, no. 7 (July 1993): 18–41. Six radiation overdoses, at least three deaths, 1985–1987.

You will not build a Mars probe in high school. You will build something where *the units agree across the interface* and *the system behaves in use* are still real questions. Both passes answer them.

---

The diagram above names three passes. You run them in order because each is strictly more expensive than the last, and each catches what the earlier passes are blind to.

**Pass 1 — Functional verification.** Does it run end-to-end on the happy path? You take the canonical input the SDD names, run the build, compare the output to what the SDD says it should produce. Pass means the program executes and the happy path returns the expected output. Fail means the build crashes, throws, or returns the wrong value on the SDD's central input. Resolution: fix code, re-run Pass 1.

Pass 1 is the pass `npm test` or `pytest` is built to run. It is the pass Claude can both write tests for and execute. It gives you the green check. It is also the pass that — by itself — fooled Mars Climate Orbiter, Therac-25, and approximately every LLM-coding benchmark before 2024.

**Pass 2 — Edge-case verification.** Does it behave correctly on inputs the SDD defines as out of bounds — empty, oversized, malformed, adversarial? You run the build against each boundary input the SDD names. Pass means the build handles each boundary as the SDD specifies. Fail has *two* resolutions and you choose between them deliberately. If the SDD specified a handling and the code didn't implement it, fix the code. If the SDD did not anticipate the edge case, amend the SDD — and amending the SDD invalidates Pass 1, so you loop back.

Pass 2 is partially automatable: the boundary inputs can sit in a test file. What is not automatable is *what counts as an edge case*. That comes from the SDD, which comes from you.

**Pass 3 — SDD-needs verification.** Read aloud, does the build satisfy what the user needs, not just what the code claims? This is the pass you cannot automate, the pass Claude cannot run, the pass that takes longest. You open the SDD to the *User Needs* section. You read each sentence aloud. After each sentence, you check the running build against it — not by running a test, by *using the build*. By looking at it. By asking whether a person who had not just built it would experience the sentence as true.

Pass 3 fails when the build is functionally correct and needfully wrong. The opening of this chapter is a Pass 3 failure: every test passed; the *at a glance* need failed. Mars Climate Orbiter was Pass 3 dressed as Pass 1; Therac-25 was Pass 3 dressed as Pass 2. A failed Pass 3 has the same two resolutions as Pass 2 — fix the code or amend the SDD — and either way you loop back to Pass 1.

This pattern shows up at studio scale. In Seth's Zebonastic reconstruction of *Hollow Knight*'s development, the studio's Pass 3 equivalent is the *cut*. The Boneforest — a planned area at the bottom of the map, with art already produced — was cut because it did not satisfy the world's theme as well as the Abyss and Ancient Basin did. The White Palace boss encounters were prototyped and cut in favor of pure platforming. A second playable character, Hornet, was fully designed, found to break Hallownest's existing geometry, and spun out into a separate game (*Silksong*) rather than wedged into the one already shipping. Each of these features was functionally correct: the art existed, the systems worked, the prototypes ran. Each failed Pass 3 against the larger need the studio held — the world's theme, the platforming purity, the geometric coherence of Hallownest — and each got cut on that basis. The cuts are the verification. A studio that ships everything it builds has skipped Pass 3.

The ISO/IEC 25010 software quality standard frames the same distinction in industrial language.[^iso] It names two complementary models: *product quality* (functional suitability, reliability, security, usability) and *quality in use* (effectiveness, efficiency, satisfaction, freedom from risk, context coverage). Passes 1 and 2 target product quality. Pass 3 targets quality in use. A build can score perfectly on one and fail the other, because the two are different things.

[^iso]: ISO/IEC 25010:2011, *Systems and Software Quality Requirements and Evaluation (SQuaRE) — System and Software Quality Models*. The standard distinguishes *product quality* (what the software is) from *quality in use* (what the software does for people in context).

The honest version of "the build is done" is: all three passes returned green, on this date, against this version of the SDD. Anything else is an estimate.

---

A test that passes because it is a bad test is not a passing test.

This sounds like a tautology. It is not. A 100%-line-coverage test suite can have near-zero real-fault detection if the assertions are weak; the build can fail in production and the suite will still show green.

The empirical case is the *mutation testing* literature, most cleanly summarized in the Defects4J benchmark.[^just] The technique: take your build, introduce small targeted defects into the code — flip a `<` to `<=`, change `+` to `-`, replace a return value with a constant — and run the test suite. If the suite catches the mutant, the suite is doing real work. If the suite passes against the mutant, the suite was not testing the thing the mutant changed. Across thousands of programs, line coverage and mutation score do not correlate. A suite at 95% line coverage can have a mutation score below 20%. The lines are touched. Nothing is asserted about them.

[^just]: René Just, Darioush Jalali, and Michael D. Ernst, "Defects4J: A Database of Existing Faults to Enable Controlled Testing Studies for Java Programs," *Proceedings of ISSTA 2014*, 437–440; and the companion FSE 2014 paper, "Are Mutants a Valid Substitute for Real Faults in Software Testing?" Together they establish that mutation analysis correlates with real-fault detection where line coverage does not.

Three patterns of bad tests to spot in your own builds.

The **tautology test**: the expected value is computed by the same function the test is supposed to verify. Often because Claude wrote a test and a helper at the same time and the helper sneaked into the expectation. `expect(addApplication([], "x").length).toBe(addApplication([], "x").length)` passes forever and asserts nothing.

The **over-mocked test**: stubs out everything the unit interacts with, so what's tested is the stubs. Mock the database, network, time, and file system and you are verifying your stub harness composes — not that your code works against the world.

The **handler-only test**: calls the function, asserts *something* came back — non-null, right type — but never asserts the content. A `deleteApplication` test that confirms the result is an array but never that the deleted application is gone passes against any correct implementation. It also passes against *return applications unchanged*. Indistinguishable.

Pass 1 assumes its tests are good tests. If they are not, Pass 1 is theater. The cheapest defense is to ask, for each test, *what minimum change to the code would make this test fail?* If you cannot name one, the test is probably bad. The more rigorous defense is mutation testing — `mutmut` for Python, Stryker for JavaScript, PIT for Java — which automates the question.[^mutationtools]

[^mutationtools]: Mutation-testing tooling is mature enough for student-scale builds in 2026. The command names carry moderate aging-risk; the pattern — introduce defects, run tests, count survivors — is stable.

Donald Knuth's 1984 case for literate programming is the conceptual ancestor of this point.[^knuth] Code is a work of literature whose primary audience is humans. A test that *reads* as if it is asserting something is not the same as a test that *is*. Verification becomes legible only when the test can be read aloud and defended: *this test asserts that, given an array with one application and a call to delete that application by id, the returned array has length zero and the original array still has length one.* If the test cannot be read aloud, it cannot be defended, and a test that cannot be defended is not evidence.

[^knuth]: Donald E. Knuth, "Literate Programming," *The Computer Journal* 27, no. 2 (May 1984): 97–111.

---

Here is the rule the rest of the chapter defends: the post-build learning document is the most important output of the build.

Not the most important *immediately* — immediately, the running build is what you can show. But across the lifetime of you-as-a-builder, the document outlasts the code. The code becomes obsolete, gets refactored, runs in a deprecated framework, solves a problem you no longer have. The account of why this build was structured this way, with which delegations and retained decisions, is the thing that compounds. Each document is a layer of pattern recognition you carry into the next build.

The document has five sections. Each is one paragraph, sometimes two. The whole document is one page. Length is a discipline: anything longer is usually padding; anything shorter is usually evasion.

**What I built.** The build named honestly, with its actual scope. Not what you hoped. Not what the SDD called it. What it actually does, in plain prose, as if telling a friend who has never seen it. If a feature is half-implemented, say so. If it works on Chrome but not Safari, say so. This section is not marketing.

**What I delegated to Claude and why.** The rows where Labor said Claude, and the reason each was a Claude row. You wrote those reasons during planning; here you write whether they held up. If a delegation did not hold, that is the most useful sentence in the document.

**What I kept for myself and why.** The rows where Labor said Human, and what kept it human — the actual supervisory capacity: problem formulation, taste, judgment under uncertainty, integration, accountability. This is also the place to record almost-delegations — the human row you nearly handed to Claude and didn't, and what told you not to.

**What I learned.** Specific, in two registers: something you learned about *the domain* and something you learned about *yourself as a builder*. The two-register format is the rubric. If either is missing, the section is not done.

**What I would do differently.** A single concrete change to your process — not a vague resolution. *Next time I will copy the User Needs section of the SDD into the top of every Claude prompt during Pass 3.* The test of whether this section is done: would the difference be visible in the next score I write? If not, it is not concrete enough yet.

The discipline is in the specificity. A document filled with abstractions — *I learned a lot; I would plan better* — is paperwork, not a document. The rubric: if a stranger read this document and your code together, could they reconstruct what your decisions were and why? If yes, the document is doing work.

There are three reasons this matters more than the code, and each is individually sufficient.

The code is fungible; the account is not. In 2026, the cost of generating a working application tracker has fallen to roughly the cost of typing the prompt. What is rare — what you can produce that Claude cannot — is the account of why this build was structured this way, with which delegations, with which capacities exercised. The code is one of many possible artifacts that could have come from your SDD. The document is the only artifact that records why this one and not the others.

The document is the proof that supervision happened. Each of the five supervisory capacities from Chapter 5 is observable only through accounting. Problem formulation lives in the *what I built* section. Judgment under uncertainty lives in the delegation paragraph. Integration lives in the kept-for-myself paragraph. Without the document, the capacities are claims. With the document, they are evidence.

And reflection is the part of learning that doesn't happen on its own. The CS-education literature converges on a finding that should not surprise you: students who write structured reflections after their builds learn measurably more than students who do not, and the effect size depends on how *structured* the prompt is.[^reflection] Open-ended *what did you learn* yields padding. Specific prompts about specific decisions yield evidence. The five sections above are structured for that reason.

[^reflection]: The CS-education reflection literature finds structured prompts substantially outperform open-ended *what did you learn* questions for measured learning gains, with the largest effect on students with low self-regulated-learning skills (Hutt et al., 2022).

You will be tempted to skip the document. Resist. The cheapest moment to write it is right after Pass 3 turns green, when the decisions are still live. Wait a week and it becomes guesswork. Wait a month and it becomes fiction.

---

What follows is the verification pass I actually ran on Phase 1. Five rows, nine unit tests, one Pass 3 failure the test suite was structurally incapable of catching. Then the post-build document.

**Pass 1.** The canonical input from the SDD: open the app, type *Submit Common App to Mizzou*, click add. Open another, type *Finish supplemental for Drexel game dev program*, click add. Click the first one to mark it submitted. Refresh the browser. Expected: two applications, the first marked submitted, persisting across refresh. Actual: two applications, the first marked submitted, persisting across refresh. `npm test` reports nine of nine passing. The page loads in Chrome, Firefox, and Safari. No console errors. Pass 1: green.

**Pass 2.** The boundary inputs the SDD names, plus one Claude proposed during planning.

| Boundary | SDD expectation | Result |
|---|---|---|
| Add empty string | Reject; do not add | Pass — input field rejects empty submit |
| Add 5,000-character string | Accept and truncate display, full text on hover | Pass |
| Delete the only application | Empty list, no crash | Pass |
| Toggle an application that doesn't exist (manual `localStorage` injection) | No crash; ignore | Pass |
| `localStorage` unavailable (Safari private window) | In-memory fallback with notice in root div | Pass |
| `localStorage["applications-v1"]` set to malformed JSON | Confirmation dialog; on confirm clear and start empty | Pass |
| Two applications with the same `id` (manual injection) | *Not specified in SDD* | **Anomaly** — toggle affects both |

The bottom row is the interesting one. The SDD did not anticipate duplicate IDs because `crypto.randomUUID()` doesn't produce them — but the build toggles both on a hand-injected duplicate. That is a Pass 2 amend-the-SDD case: the spec was silent and needs a sentence. I added one — *`toggleSubmitted` and `deleteApplication` operate on the first matching id; downstream uniqueness is the data layer's responsibility* — and looped back to Pass 1. The tests still pass; the SDD change matches existing behavior. Pass 2: green.

**Pass 3.** I open the SDD to section 2.3, *User Needs*. I read each sentence aloud against the running build.

*The user should be able to add, mark submitted, and delete applications.* Yes. Pass.

*Applications should persist across browser refresh and reasonable browser closures.* Yes. Pass.

*On corrupted storage, the user should be offered a recovery path, not silently lose data.* Yes, tested in Pass 2. Pass.

*The user should be able to see at a glance which applications remain to submit and which are already in, without scrolling.* I look at the running build. Six applications. Three submitted with strikethrough. List order is insertion order; the submitted ones are scattered. With twenty applications across the November–January window, *at a glance* would be impossible. **Pass 3 fails.**

The failure is not in the code. The failure is that the score never had a row for *display order*, because the SDD's prose buried *at a glance* in section 2.3, and by row 5 I had stopped re-reading section 2.3.

Resolution: fix the code, not amend the SDD. The need is clear and good; the code did not satisfy it. I add row 6: *Sort displayed applications: not-yet-submitted first, submitted below, each subgroup in insertion order. Handoff condition: with three unsubmitted and three submitted applications added in arbitrary order, the unsubmitted ones render above the submitted ones on every refresh.* I run row 6 as a Claude row. Prompt: *In `main.ts`, sort displayed applications before rendering: `submitted: false` first in insertion order, then `submitted: true` in insertion order. Sort a copy; don't mutate. Update no other behavior.* Claude produces a six-line change and one new test. `npm test` reports ten of ten passing. I re-run Pass 3 on the *at a glance* sentence. Unsubmitted on top, submitted below. *At a glance* now reads as true.

Pass 3: green. The build is done.

---

**Post-build document: Phase 1 of the application tracker.** *Written immediately after Pass 3 turned green.*

> **What I built.** Phase 1 of the application tracker is a single-page browser app that lets one user add, mark submitted, and delete short college-application entries. Applications persist in `localStorage` under the key `applications-v1`. On parse failure the app shows a recovery dialog. On `localStorage` unavailable the app falls back to in-memory and tells the user. Unsubmitted applications render above submitted ones; within each group, insertion order is preserved. The app does not yet support editing, per-school supplements, recommender tracking, or fee-waiver status; those are Phase 2 and Phase 3.
>
> **What I delegated to Claude and why.** Three rows were Claude rows. Row 2 (HTML scaffold) was delegated because the boilerplate is bounded and not load-bearing; the delegation held. Row 3 (`addApplication`, `toggleSubmitted`, `deleteApplication`) was delegated because the contract was specifiable as a tested interface; it held with one `/rewind` to re-specify immutability after Claude's first pass used `splice`. Row 5 (`localStorage` wiring) was delegated because the platform API is well-documented and Claude was useful for the parse-failure recovery dialog; it held. The pattern: I delegated rows whose handoff condition was unambiguous and whose scope was small enough to read in a single sitting.
>
> **What I kept for myself and why.** Two rows were human rows, plus an emergent row 6 during verification. Row 1 (data model) was Problem Formulation — deciding what an *application* is in this app, in writing, before the UI existed. Row 4 (visual hierarchy) was Aesthetic Judgment — three CSS variables decided before any styles were written, so Claude inherits the palette instead of choosing it. Row 6 (sort order) emerged from Pass 3 and stayed human because the *at a glance* need is a judgment call about what the user sees; the implementation was a Claude row once the order was decided. The supervisory capacity that did the most work across the build was Integration — connecting the SDD's prose to the score's rows. The capacity that almost failed was Accountability — I almost skipped Pass 3.
>
> **What I learned.** I learned, about the domain, that *display order* is part of *what applications do* in an application tracker; insertion order is not a neutral default but a design decision, and a wrong one for the at-a-glance need. I learned, about myself as a builder, that I treat the SDD's prose as homework once the score is written — I substitute the score's rows for the SDD's needs and stop re-reading the section that produced the rows. This is exactly the failure mode the chapter on verification names. I caught it because I had been taught to run Pass 3 by reading aloud; if I had been running tests-as-verification, I would have shipped a build that passes its tests and fails the need that motivated the build.
>
> **What I would do differently.** Next time, I will copy the *User Needs* section of the SDD as a literal block at the top of the score, above the rows, and I will read it aloud at the start of every verification pass, not just Pass 3. The change is concrete: it is a row 0 in the score template. The test of whether it works is whether Pass 3 ever again surfaces a need the score had not already enrolled as a row.

That document is the most important file in the Phase 1 commit. The code is the second.

---

## 🕰️ AI Wayback Machine

**Charles Sanders Peirce** (born 1939) is a computer scientist at MIT whose work answers the question this chapter's verification sequence rests on: *what does it mean for code to be correct?* In 1974, with Stephen Zilles, she published the foundational paper on abstract data types — the argument that a type is defined by the operations it admits and the relations those operations preserve, not by its representation. In 1994, with Jeannette Wing, she formalized the Liskov Substitution Principle: any property proved of a supertype must hold of its subtypes, with a *history constraint* that the property hold across sequences of operations, not just single calls. The principle is one of the four foundational tenets of object-oriented design; she won the Turing Award in 2008.

Liskov's contribution is the formal version of *"correct" must be defined before it can be verified*. Pass 3 is its operational form. You cannot check whether a build does what the user needs until the needs are specified independently of the code. The score is the schedule; the SDD is the Liskov contract.[^liskov-note]

[^liskov-note]: Honest flag. The AI Wayback Machine rule in this book is pre-2000 foundational, with contributors deceased by 2001. Charles Sanders Peirce is alive (born 1939) and her best-known formalization (Liskov & Wing) is 1994 — by strict reading she does not qualify. She is kept here because her contribution is the precise intellectual ancestor of the SDD-needs verification pass, and replacing her with a strictly-qualifying figure would weaken the connection the chapter is making. The alternates considered — Tony Hoare, Margaret Hamilton, Robin Milner — each appear elsewhere in this book. The override is deliberate editorial choice, not oversight.

**

![Charles Sanders Peirce, 1870s. AI-generated portrait based on a public domain photograph.](../images/charles-sanders-peirce.jpg)
*Charles Sanders Peirce, 1870s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:**

> *Treat my SDD's User Needs section as a behavioral contract in Liskov's sense. For each numbered need, write the post-condition an outside observer would have to confirm to count the need as met. Do not look at my code. For each post-condition, propose one observation — not a unit test, an observation — that would confirm or falsify it on the running build. Output a table with columns: Need, Post-condition, Observation.*

Paste your SDD's *User Needs* section underneath the prompt. The output is your Pass 3 checklist. If a need does not yield a post-condition, the need is too vague; rewrite it before you build.

---

**Links:** [boondoggling.ai](https://boondoggling.ai) · [irreducibly.xyz](https://irreducibly.xyz)

**References (in-chapter citations):**

- Hoare, C. A. R. (1969). An axiomatic basis for computer programming. *Communications of the ACM*, 12(10), 576–580.
- ISO/IEC 25010:2011. *Systems and Software Quality Requirements and Evaluation (SQuaRE) — System and Software Quality Models*. ISO/IEC.
- Just, R., Jalali, D., & Ernst, M. D. (2014). Defects4J: A database of existing faults to enable controlled testing studies for Java programs. *Proceedings of ISSTA 2014*, 437–440.
- Knuth, D. E. (1984). Literate programming. *The Computer Journal*, 27(2), 97–111.
- Leveson, N. G., & Turner, C. S. (1993). An investigation of the Therac-25 accidents. *IEEE Computer*, 26(7), 18–41.
- Liskov, B. H., & Wing, J. M. (1994). A behavioral notion of subtyping. *ACM Transactions on Programming Languages and Systems*, 16(6), 1811–1841.
- Liskov, B. H., & Zilles, S. N. (1974). Programming with abstract data types. *ACM SIGPLAN Notices*, 9(4), 50–59.
- Mars Climate Orbiter Mishap Investigation Board (1999). *Phase I Report*. NASA.

---
