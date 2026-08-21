# Chapter 13 — Teaching the Discipline: What Your Students Are Reading


## TL;DR

- This chapter gives a working overview of Teaching the Discipline: What Your Students Are Reading, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, The chapter map, Sharing your CLAUDE.md as a classroom artifact, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> Your students have *Claude Code for Students*. This chapter maps what they are reading to what you now know how to teach — and how your three-tier CLAUDE.md, your build transcripts, and your lived dangerous middles become the most valuable teaching artifacts in your classroom.

---

## Learning outcomes

1. **(Apply)** Map each chapter of the students book to a corresponding teacher build experience and a suggested classroom activity.
2. **(Create)** Prepare a redacted version of your CLAUDE.md for classroom sharing; identify what to redact and what to highlight.
3. **(Evaluate)** Design a build-log assessment with a rubric on the five supervisory capacities.

---

## Opening

The teacher was reading Chapter 5 of *Claude Code for Students* — the five supervisory capacities. Seth was describing how PA had fired when his grading-tool output passed every check and was still pedagogically wrong. The chapter described the moment.

The teacher knew this moment. The teacher's own CLAUDE.md, by this point in the school year, spanned three build tiers — class website, grading tool, simulation. The Lessons Learned section had an entry from the night the grading-workflow Skill almost shipped a draft to an ESL student that pattern-completed from another submission. The teacher had caught it. The catch was PA. The lesson had gone into CLAUDE.md.

The teacher closed the students book and opened CLAUDE.md.

The file was the textbook the chapter described. Not in the abstract — the actual file. Three months of grown discipline, with the dangerous middles and the architectural decisions visible in the Lessons Learned section. The students book described the artifact. The teacher *had* the artifact.

This is the chapter. The students book teaches the framework; the teacher's lived practice with the framework produces the teaching assets the students book cannot produce. The teacher's CLAUDE.md, the teacher's build transcripts, the teacher's stories of the dangerous middles — these are the irreplaceable pedagogical content the students book points at but cannot provide. The chapter maps the students book chapter by chapter to the teacher's accumulated assets, and offers guidance on how to bring those assets into the classroom.

---

## The chapter map

For each chapter of *Claude Code for Students*, the corresponding teacher build experience and a suggested classroom activity. Fourteen rows. Refer to the table during planning; bring the relevant artifact when you teach the matching student-book chapter.

| Student Ch | What students learn | Teacher build experience | Suggested classroom activity |
|---|---|---|---|
| 0 | The cautious-builder observation | The class-website first session — your first encounter with the agentic loop | Discuss: when have you seen a friend approve AI output they didn't fully understand? |
| 1 | Claude Code is not ChatGPT | Your Chapter 1 first session, the agentic loop, the questions-before-code rule | Run a first session on a starter project; ask three questions before any change |
| 2 | The /v0 gate and explore-first | Your Chapter 3 specification work; the Explore→Plan→Implement→Commit workflow | Run the workflow on a peer-supplied task |
| 3 | The dangerous middle (intro) | Your Chapter 8 grading-tool dangerous-middle story (the bias case, the descriptive feedback case) | Audit AI-generated feedback for the three failure modes |
| 4 | Conducting (suggest → explain → verify analogue) | Your gate transcripts across all three builds | Apply the gate to a peer-supplied prompt; document each step |
| 5 | Five supervisory capacities | Your Chapter 5 capacity application; the grading-tool's PA moment | Label a build transcript by capacity (PA, PF, TO, IJ, EI) |
| 6 | CLAUDE.md | Your three-tier CLAUDE.md — show the artifact and the growth | Write a CLAUDE.md for a student project; peer-review one section |
| 7 | Problem formulation | Your Chapter 6 grading-tool prep (one-sentence formulation) | One-sentence test on a peer's project description |
| 8 | Specifications | Your specifications across builds — show three good and one weak | Rewrite a vague prompt as a five-element specification |
| 9 | Handoff conditions and the technical dangerous middle | Your Act One handoff conditions; your /rewind moments | Write handoff conditions for a 5-step terminal task |
| 10 | Creative builds (Brutalist) | Your Chapter 10 three-file system for the simulation | Write a PROJECT.md Intent Layer for a student project |
| 11 | Planning the first conducted build | Your Chapter 10 three-file process | Plan a student-scale build; produce the three files |
| 12 | Running the build | Your Chapter 11 simulation build with the three pivotal moments | Execute a Phase-1 build with a Generation Log |
| 13 | Verification | Your Chapter 12 three-check deployment-verification protocol | Run the three-check pass on a peer's build |
| 14 | The first full build | Your Chapter 14 post-build document | Complete a student build + post-build document |

The table is a planning aid. Bring it out when you plan the unit. Pull the relevant CLAUDE.md section, build transcript, or story when you teach the matching chapter. The students book provides the framework; your assets provide the texture.

---

## Sharing your CLAUDE.md as a classroom artifact

Your CLAUDE.md is one of the most pedagogically valuable artifacts in your possession. With careful redaction, it can be shared with students as a model.

**What to redact.**

- The `[private]` section (already separate from the main file, per Chapter 2's structure).
- Specific student names anywhere in Lessons Learned (replace with role: *"the ESL student who…"* → *"a student whose grading draft pattern-completed from another submission"*).
- Server credentials, internal URLs, district-IT-sensitive details.
- The specific assignment file paths from this semester (replace with `<current-assignment>/`).
- Disciplinary references.

**What to highlight.**

- The Lessons Learned section — particularly entries from the dangerous middles. These are evidence the framework works under pressure.
- The Never Rules — these show students what discipline looks like as written-down constraint.
- The growth across versions if you have git history. *"Here is CLAUDE.md after the class website. Here is CLAUDE.md after the grading tool. Here is CLAUDE.md after the simulation. Notice what was added when."*
- The three-file system from Chapter 10 — DESIGN.md and PROJECT.md alongside CLAUDE.md for the simulation. Students rarely see the three-file system in practice; your example is the demonstration.

**How to present.**

Open the redacted CLAUDE.md (and DESIGN.md, and PROJECT.md if showing the simulation's files) on the projector. Scroll slowly through each section. Pause at one or two Lessons Learned entries and tell the story behind them — *"this entry came from the night I almost shipped a feedback draft that pattern-completed across submissions. I will tell you what happened and what I changed."* Students remember the story. They infer the discipline from the artifact.

The walkthrough takes 20–30 minutes. It is one of the most engaging classes of the unit. Students see the framework not as a recipe in a book but as a *practice* their teacher has lived through.

---

## Build logs and how to assess them

The students book's chapters on running the build and verification introduce the build log — a record of the build as it unfolded, with capacity labels at each decision point. The teacher's chapter map sends students to write build logs alongside their code.

The pedagogical move: assess the build log, not (only) the code.

**Why.**

Code can be generated by AI in one prompt with no conducting. A build log that captures specification, plan-mode review, handoff condition, capacity label, /rewind, revision — this *cannot* be generated cleanly by AI from the code alone. The build log is the artifact of conducting. Grading the build log measures the discipline; grading the code measures only the result.

**The build-log rubric.**

Five dimensions. Each scored 0–4. Total possible: 20.

1. **Specification quality.** Are the five elements (operation, invariants, context, output format, negative constraint) present and specific? (0 = absent or vague; 4 = each element specific and load-bearing.)

2. **Gate execution.** Is each step documented through Explore → Plan → Implement → Commit? Are the plan-mode edits captured (what changed and why)? (0 = gate skipped; 4 = every step gated, with plan edits noted.)

3. **Handoff conditions.** Are handoff conditions written, testable, and binary? Are failures documented and resolved through /rewind and respecify? (0 = no conditions; 4 = strong conditions with at least one /rewind documented.)

4. **Capacity labeling.** Are PA, PF, TO, IJ, EI labeled at the decision points where they fired? Are at least three capacities exercised across the build? (0 = no labels; 4 = at least three capacities labeled with reasoning.)

5. **Lessons learned.** Does the build log produce at least one Lessons Learned entry suitable for CLAUDE.md? Does the entry name the issue, the cause, and the future protection? (0 = no entry; 4 = one or more well-formed entries.)

Sample score interpretation: 16–20 = conducting evident; 11–15 = conducting partial; 6–10 = framework present but uneven; 0–5 = framework absent.

Peer review can do most of the assessment. Each student reviews two peers' logs against the rubric. The teacher reviews the rubric agreements and disagreements (not every log individually). The teacher's time goes to high-impact follow-up.

**Whether to grade.**

Two options. Either (a) build logs are formative — they shape feedback and conversation but are not scored, or (b) build logs are summative — they replace or supplement the code grade. The chapter's working recommendation: formative for the first two builds, summative for the third (the capstone simulation). Students experience the framework as conversation first, as assessment second.

---

## The teacher's role during student builds

When students are working through their builds in class, the teacher's role shifts. Not the tutor who tells them the right command. Not the silent observer. The conductor's question-asker.

**Questions to ask.**

- *"What did Claude's plan-mode output tell you would happen?"*
- *"What is your handoff condition for this step?"*
- *"Which supervisory capacity is firing right now?"*
- *"Is the output the output your specification asked for?"*
- *"What would the failure mode have looked like if you had not noticed?"*
- *"What goes into CLAUDE.md from this?"*
- *"Should this be a Skill, a Hook, or a Subagent — and why?"*

Each question is the framework spoken aloud. None tells the student the answer. Each prompts the student to exercise the capacity rather than delegate it to the teacher.

**Questions to avoid.**

- *"Here is the right specification."* (Removes PF; the framework's central act.)
- *"That output looks fine; move on."* (Removes PA; the framework's most important capacity.)
- *"Let me show you how I would build it."* (Replaces the student's conducting with the teacher's.)

The teacher's restraint is the discipline. It is harder than it sounds. When a student is stuck and you can see the answer, the urge to give the answer is strong. The framework requires letting the stuck moment last long enough that the student exercises the capacity to get unstuck — with a question, not a solution, from the teacher.

---

## Specification writing IS CS education

Geoffrey Challen, in his January 2026 Illinois CS faculty retreat talk, said it directly:

> *"The challenge in CS teaching is no longer turning specifications into code. AI is good at that. The challenge is getting your idea out of students' brains and into specifications that coding agents can work with. Teaching specification writing IS CS education."*

The chapter's recommendation: take Challen seriously. Restructure your unit so that *specification writing* is the assessed skill, not (only) the code that results. The five-element specification format from Chapter 3 is the working framework. The exercises that ask students to convert vague prompts to specifications, peer-review specifications before any code is generated, debug specifications when the resulting code is wrong — these are the new CS exercises.

This is a curriculum shift, not just an assessment shift. The chapters of the students book that students will engage with most deeply are the ones that build specification-writing capacity: Chapter 2 (the /v0 gate), Chapter 7 (problem formulation), Chapter 8 (specifications), Chapter 9 (handoff conditions). Bias your classroom time toward these. The other chapters reinforce; these establish.

---

## Teacher build: prepare your teaching artifacts

Apply the chapter's discipline to your own teaching prep.

1. **Redact your CLAUDE.md.** Produce a `CLAUDE.md.classroom` version with the redactions named above. Verify nothing FERPA-sensitive remains. Keep the Lessons Learned entries (with names removed); the lessons are the pedagogical content.

2. **Select three build transcripts.** One from the class website, one from the grading tool, one from the simulation. Annotate each with the capacities that fired and the handoff conditions that gated. Save as artifacts to bring out when teaching the corresponding student-book chapters.

3. **Compose one dangerous-middle story.** The Chapter 8 story (the ESL bias case, or your own) is the canonical one for the grading-tool chapter. Write it as a 2-minute classroom story — beginning (what you were building), middle (what failed), end (what you learned and added to CLAUDE.md or as a Hook).

4. **Design one build-log assessment for your unit.** Use the five-dimension rubric above; adapt the scoring to your context. Decide whether the first deployment is formative or summative.

5. **Draft your role guide.** A one-page document with the questions-to-ask and questions-to-avoid lists; bring to class as a reference until the questions become reflex.

The five artifacts take 4–5 hours to prepare. They serve the entire semester of teaching the unit. The economics favor the prep.

---

## Classroom activity: students audit a public build log

A summative version of build-log assessment as a class activity.

You provide a build log (your own, redacted) or a curated public one. Students apply the five-dimension rubric to score it. They submit their rubric scores plus a one-paragraph justification for the lowest score they gave.

Discussion: where did students agree? Where did they disagree? What is the difference between scoring a 2 and a 3 on capacity labeling? The rubric's calibration is the discussion's outcome.

The activity teaches assessment design as well as conducting practice. Students who score build logs learn to write build logs that score well. The metacognitive transfer is the activity's pedagogical contribution.

This activity is teacher-specific; the students book does not contain a build-log scoring exercise. Your design is the worked example for the future updated edition.

---

## Common misconceptions

**"Teaching the discipline is just assigning the students book."** No. The students book provides the framework. The teacher's PCK — built through their own builds, captured in CLAUDE.md and transcripts — provides the texture that makes the framework teachable.

**"My CLAUDE.md is too personal to share."** Most of it is not. With the `[private]` section separated and the FERPA-relevant entries redacted, the rest is some of the best teaching material in your possession.

**"Build logs are too much work to grade."** Peer review reduces teacher load. The five-dimension rubric makes scoring fast (10–15 minutes per log; less with practice). The first time is the longest; the rubric calibration happens in the first round.

**"Students will not see my framework as authority."** They will if you have lived it. The three-tier CLAUDE.md is evidence the framework works. The dangerous-middle stories are evidence of where it caught real failures. Authority follows from artifact, not from claim.

**"Asking questions instead of giving answers slows the class."** Per moment: yes, by about 90 seconds. Per semester: it speeds the class by producing students who can work independently sooner. The investment is upfront; the payoff is across the unit.

---

## Exercises

1. **(Apply)** Produce your redacted `CLAUDE.md.classroom`. Verify no FERPA-sensitive content remains. Compare your file to the students book's CLAUDE.md example; note three places where your artifact is richer (or thinner) than the example.

2. **(Create)** Design a build-log assessment for the first conducted build your students will do. Specify: the prompt (what they will build), the rubric (the five dimensions), the assessment cycle (formative or summative), the peer-review structure.

3. **(Evaluate)** Observe yourself in two consecutive classes. Count the times you gave an answer when you could have asked a question. Set a target reduction for the next class.

---

## What would change my mind

The chapter's strong claim is that **the teacher's CLAUDE.md and build transcripts are pedagogically more valuable than any textbook example** for teaching the conducting framework. If a controlled comparison found that students taught with textbook examples only performed comparably to students taught with their teacher's lived artifacts, the chapter's central pedagogical move (bring your own artifacts into the classroom) becomes optional. The chapter would still recommend it for engagement; the case for irreplaceability weakens.

I expect the difference to be substantial because PCK research consistently shows that subject knowledge grounded in personal practice is more teachable than subject knowledge held only at the textbook level. The CLAUDE.md and build transcripts are the textbook examples in their applied form — the form students will recognize in their own builds.

---

## Still puzzling

- **The right balance of formative vs. summative build logs.** The chapter recommends two formative, one summative. Some practitioners prefer all-formative; others all-summative. Local context (district grading requirements, student maturity) decides.

- **Whether the teacher's CLAUDE.md should be public.** Some teachers share theirs as open educational resources; others keep the artifact private. The chapter's working answer: redact and share for your students; consider open-sourcing the redacted version for the broader teaching community.

- **The teacher's-role guidance under load.** With thirty students all building simultaneously, the questions-to-ask discipline is harder to maintain. The chapter's working answer: pair students into reviewing-each-other groups; the teacher rotates and asks questions only at high-impact moments.

---

## Links

- [Lee Shulman, "Those Who Understand"](https://www.jstor.org/stable/1175860) — the founding PCK paper.
- [Mishra & Koehler, "Technological Pedagogical Content Knowledge"](https://www.tcrecord.org/Content.asp?ContentId=12516) — TPACK foundational.
- [Geoffrey Challen, "A Day With Claude"](https://geoffreychallen.com/talks/2026-01-15-a-day-with-claude-using-and-teaching-coding-agents) — the specification-writing-IS-CS-education claim.

---

##  AI Wayback Machine
 **Lee Shulman** (born 1938) — American educational psychologist whose 1986 *Educational Researcher* article *"Those Who Understand: Knowledge Growth in Teaching"* introduced the concept of **pedagogical content knowledge (PCK)** — the distinct knowledge a teacher needs that is *neither* pure subject expertise *nor* general pedagogy, but the specific synthesis of *how to teach this particular subject to these particular students*.[^1] Shulman's argument was that good teachers know things that good practitioners of the subject do not necessarily know, and that good general educators also do not know — the subject-specific transformations and representations that make the subject teachable.

The conducting framework is PCK applied to AI-assisted classroom teaching. The teacher who has built a class website, a grading tool, and a simulation with the framework has PCK that neither a CS professor without classroom experience nor a generalist education professor without Claude Code experience can replicate. The CLAUDE.md is the artifact of that PCK. The build transcripts are the artifact of that PCK. The dangerous-middle stories are the artifact of that PCK. None can be downloaded from a textbook because PCK is built only through doing.

Shulman extended PCK in his 1987 *Harvard Educational Review* paper to argue that teaching requires the practitioner to *reconstruct the subject in pedagogically appropriate forms* — analogies, demonstrations, examples, narrative — for the particular students in the particular classroom. The teacher's CLAUDE.md walkthrough, the dangerous-middle story, the build-log rubric are exactly this reconstruction. The conducting framework provides the subject; the teacher's lived practice provides the pedagogically-appropriate forms.

Mishra and Koehler extended Shulman's PCK to *Technological Pedagogical Content Knowledge (TPACK)* in their 2006 *Teachers College Record* paper — adding technology as a third dimension that interacts with subject and pedagogy. The conducting framework is TPACK applied to AI tools specifically. The technology (Claude Code) interacts with the subject (CS, or whatever the simulation teaches) and the pedagogy (how the teacher structures the classroom around the tool). Mishra and Koehler's argument was that teachers who develop TPACK across all three dimensions teach more effectively than teachers who develop one or two. The chapter's recommendation — bring your CLAUDE.md, your transcripts, your dangerous-middle stories into the classroom — is the operational form of TPACK development for the agentic-AI classroom of 2026.

---

## Bridge

The discipline is taught. Chapter 14 closes the book with the practitioner's most important closing artifact — the post-build document. Not for the simulation. For the framework as a whole.

---

[^1]: Shulman, L. S. "Those Who Understand: Knowledge Growth in Teaching." *Educational Researcher* 15, no. 2 (1986): 4–14. See also Shulman, L. S. "Knowledge and Teaching: Foundations of the New Reform." *Harvard Educational Review* 57, no. 1 (1987): 1–22. The TPACK extension is in Mishra, P. and Koehler, M. J. "Technological Pedagogical Content Knowledge: A Framework for Teacher Knowledge." *Teachers College Record* 108, no. 6 (2006): 1017–1054.
