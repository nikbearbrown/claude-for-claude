# Chapter 5 — The Five Supervisory Capacities


## TL;DR

- This chapter gives a working overview of The Five Supervisory Capacities, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, The five capacities, PA — Plausibility Auditing, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> These are the five things you do that Claude cannot. Name them. Exercise them explicitly. Never delegate them.

---

## Learning outcomes

1. **(Remember)** Name and define the five supervisory capacities — PA, PF, TO, IJ, EI.
2. **(Apply)** Label the supervisory capacity required at each step of a grading-tool build sequence.
3. **(Analyze)** Diagnose a build that went wrong by identifying which capacity was absent.

---

## Opening

Seth was mid-build on the grading tool. He had asked Claude to draft feedback for a stack of ten essay submissions. The CLAUDE.md was in place. The specification was well-formed. Claude returned ten short feedback drafts.

The drafts were fluent. Each one identified the essay's thesis, named one or two specific strengths, noted one or two areas for revision. The tone was even. The grammar was clean. Seth scanned them, ready to ship.

Then he stopped. Something was wrong. He could not name it.

He read the first draft again. *"Your essay demonstrates a strong central argument about the costs of standardized testing. Your evidence is well-chosen. Consider expanding your discussion of the international comparisons in paragraph three."* The feedback was correct. Each claim was accurate. The student had written about standardized testing. The argument was strong. The international comparisons could be expanded.

Then he read the second draft. *"Your essay demonstrates a strong central argument about the costs of standardized testing. Your evidence is well-chosen. Consider expanding your discussion of the international comparisons in paragraph three."*

The second student had not written about standardized testing. She had written about food deserts. The feedback was Seth's first essay's feedback, with the topic substituted invisibly.

Claude had produced ten drafts in the form of the first. The first was accurate; the other nine had been pattern-completed against the first one's structure, with surface words swapped to match each student's topic. The feedback was *fluent and pedagogically empty*. Seth almost shipped.

The capacity that caught the failure was *plausibility auditing* — Seth's training, refined across years of grading, that hears the wrong note in a feedback draft even when the draft is technically correct on the surface. Without PA firing, the ten drafts would have gone to ten students whose essays had been read by no one.

This chapter names the five capacities Seth exercised in that moment, plus four others. Together they constitute the work the teacher does that Claude Code cannot. Once named, they become teachable. Practiced, they become reflex. The chapter is the vocabulary; the chapters that follow exercise the capacities across the grading-tool build.

![Five supervisory capacities ](images/06-five-supervisory-capacities-fig-01.png)
*Figure 6.1 — Five supervisory capacities *

---

## The five capacities

Each capacity is one operation. Each is something Claude Code cannot perform reliably because the operation requires context, judgment, or accountability the model does not have.

### PA — Plausibility Auditing

*Hearing the wrong note in Claude's output even when the output passes every surface check.*

The Seth-and-the-ten-drafts moment. PA fires when something does not match — the output is technically correct and contextually wrong, fluent and pedagogically empty, scoped right and purposed wrong. The capacity is trained on years of grading actual student work; the surface fluency of Claude's output does not produce in you the embodied response that genuine grading does.

Worked moments in the grading context:

- Feedback that describes the essay structure correctly but in language that does not match what you would write to this student.
- A draft that praises a strength the student has been working on for a month — and the praise is generic enough that the student will not feel seen.
- Output that respects the rubric in form (touches each criterion) and not in intent (treats each criterion as equal weight when your rubric has implicit weighting).

PA is engagement, not paranoia. The Anthropic 2026 RCT confirmed: students with high engagement (asking follow-up questions, demanding explanations, exercising critical reading on AI output) outperform students with low engagement by 17 percentage points on coding skills.[^1] PA is the engagement-pattern made operational for teacher work.

### PF — Problem Formulation

*Deciding what the build IS, in operational terms, before any prompt runs.*

PF is upstream. Before Seth wrote the first specification for the grading tool, he had to answer: *am I building a tool that generates feedback for me to send, or a tool that flags patterns across submissions so I can write the feedback myself?* The two are different builds with different scopes, different stakes, and different dangerous middles.

The wrong formulation is invisible from inside the build. Claude optimizes within the frame you give it; if the frame is wrong, the output is wrong, *elegantly*. The chapter-opening failure was partly a PF failure — *generate feedback per submission* was the frame; the right frame for the assignment's particular shape would have been *flag patterns across submissions, write per-student feedback myself.*

PF is irreducibly the teacher's. The CLI cannot know which frame matches your pedagogical intent. The frame is the work you do before any specification is written.

### TO — Tool Orchestration

*Choosing which Claude Code affordance applies to each step, in what order, with what trust level.*

The Claude Code surface is large. CLAUDE.md for always-on context. Skills (Chapter 6) for invokable workflows. Hooks (Chapter 7) for deterministic enforcement. Subagents (Chapter 9) for isolated-context research. Plan mode. `/rewind`. `/clear`. Each affordance has a use case; the wrong choice produces worse outcomes than no tool would.

For the grading tool's "draft feedback" step, the orchestration question is: *do I put the rubric in CLAUDE.md (always present in context) or in a Skill (invoked only when grading)? Do I run the analysis as a single prompt or delegate the rubric-pattern-detection to a Subagent? Do I write a Hook to enforce the no-final-grade rule, or rely on CLAUDE.md alone?*

TO is the discipline of matching the tool to the work. The chapters that follow (6, 7, 9) give you the tools; this chapter names that *choosing* is its own capacity.

### IJ — Interpretive Judgment

*Supplying meaning Claude cannot supply because the meaning requires context the model does not have.*

The clearest case in grading. Claude reads a student's essay and produces feedback that is technically correct: the thesis is present, the evidence is named, the structure is identified. The feedback is *anonymous* — it applies to any student who wrote that essay. The teacher who has known the student for a semester, who watched them struggle with this argument three weeks ago, who knows what feedback this particular student can hear right now and what feedback would shut them down — supplies the interpretive layer that turns the technically-correct feedback into *useful* feedback.

CLAUDE.md captures the technically encodable layer of IJ. *"This class is tenth grade; tone should be supportive."* That helps. It cannot replace the per-student judgment about which encouragement matters now, which critique can be heard now, which silence is the right response now. IJ is irreducibly the teacher's because the teacher is the one in the room.

### EI — Executive Integration

*Holding the whole build's intent across steps; catching the drift when a later step contradicts an earlier constraint.*

The grading tool has multiple steps: read the submission, score against the rubric, draft feedback, format the output, write to the per-student file. EI is the capacity that, at step 4, remembers that step 1 established *the tool never generates a numeric grade* — and notices when step 4's output contains a "Score: 87/100" line.

The agentic loop is structurally tempted to add functionality. *"While I'm here, let me also include the score."* The CLAUDE.md never-rule and the Hook (Chapter 7) will catch some of this. EI is the human capacity that catches the rest — particularly the cases where the contradiction is subtle (the feedback uses grade-implying language without quoting a number; the formatting suggests a final assessment rather than a draft).

EI requires holding the whole build in your head. Most builds are small enough that you can. The first time you cannot, the build is too big for the session; `/clear`, decompose into smaller builds, run them in sequence.

---

## How the capacities fire across one grading-tool build

Trace a grading-tool build with capacities labeled.

1. **Open Claude Code in the grading project.** Run `/init` if you have not already. Read the CLAUDE.md. `[TO]` choice: this is a CLAUDE.md project, not a Skill project; the rubric will live in CLAUDE.md because every grading session uses it.

2. **Compose the specification for "draft feedback for one student."** Single submission. Single rubric. Five elements. `[PF]` for the frame: feedback per submission, not pattern across submissions, on this assignment because the assignment is short and the per-student tone matters more than the pattern.

3. **Run in plan mode, edit via Ctrl+G, approve.** Claude executes. Returns the draft. `[PA]` on the draft: does the feedback match what I would say to this student? `[IJ]` on the tone: does the encouragement land for *this* student who has been struggling?

4. **The draft is close.** Two of three rubric criteria are addressed; the third is dropped. `[PA]` again: the dropped criterion is the one the student needs feedback on most. `[EI]` reminds: the spec said all three criteria must be addressed. The draft fails the handoff condition.

5. **`/rewind`.** Restore to before the failed step. `[TO]`: revert-and-respecify, not forward-correction.

6. **Respecify with the criterion explicitly named.** *"Address all three rubric criteria. If a criterion does not apply to this essay, say so explicitly rather than skipping it."* `[PF]`: the spec is sharper now; the frame protects against the failure mode that fired.

7. **Re-run. Output meets the handoff condition.** `[PA]` on the new draft: does it still match what I would say to this student? Yes. Ship.

8. **Update CLAUDE.md.** *"Grading specs must require addressing every rubric criterion explicitly, including criteria that do not apply."* `[EI]`: the lesson belongs in persistent context.

Five capacities, eight labeled moments, one grading draft shipped. The labels are diagnostic; in practice the capacities blend into a single conducting motion. The chapter's contribution is the vocabulary that makes the motion teachable.

---

## Diagnosing a failed build by absent capacity

A teacher's grading tool sent 23 feedback drafts to students last week. Eight of them contained the wrong essay's analysis (pattern-completion from a prior draft, like the Seth opening). The teacher discovered the error when a student replied confused.

Decompose by capacity. Find the absent one.

- **PF?** The spec was *"generate feedback for each submission."* No mention of avoiding cross-submission pattern leakage. Vague.
- **PA?** Each draft was read once before sending. The pattern leakage was not obvious in a fast scan. PA fired weakly because the surface fluency masked the failure.
- **TO?** The tool was a single Claude prompt that processed all 23 submissions in one session. The context window saturated; pattern-completion intensified. A subagent per submission would have avoided this.
- **IJ?** The teacher knew the students well enough that, on a slower read, the wrong-essay feedback would have been caught. The fast scan disabled IJ.
- **EI?** The spec from three weeks earlier said *each draft is independent*. The current execution violated this. EI did not fire because the violation was implicit, not explicit.

The diagnosis: four of five capacities weakly engaged or absent. The proximate failure was PA on a fast scan. The root failure was TO (wrong tool choice — single prompt for 23 submissions) compounded by PF (specification did not name the cross-submission failure mode).

The fix is at multiple layers: a Subagent-per-submission orchestration (TO); a specification that names *no cross-submission pattern leakage* as a negative constraint (PF); a slower reading discipline (IJ); a Hook that flags suspicious phrase repetition across drafts (EI made operational).

This is the chapter's diagnostic move. When something goes wrong, the absent capacity names the lesson. The lesson goes into CLAUDE.md.

---

## Teacher build: prepare the grading-tool build

You will build a grading tool across Chapters 6–9. This chapter's build is preparation: design the capacity-exercise points before building.

1. **PF prep.** Write the one-sentence problem formulation for your grading tool. *What does it do? Whose work does it touch? What does it never do?* Three sentences. If you cannot answer in three sentences, the formulation is not done.

2. **TO plan.** For each step of the grading tool you envision, pre-decide: CLAUDE.md? Skill (Ch 6)? Hook (Ch 7)? Subagent (Ch 9)? Single prompt? You will revise as Chapters 6–9 introduce each affordance; the initial plan surfaces your assumptions.

3. **PA training.** Identify the two most likely wrong-note moments in your grading tool — the points in the build where Claude's output is most likely to look fluent and be pedagogically wrong. Write the handoff condition that would catch each.

4. **IJ scaffolding.** Identify what you, the teacher, will need to add to every draft that Claude cannot. Per-student knowledge. Tone calibration. The thing only you can do.

5. **EI guardrails.** Write the build's three guiding never-rules. *Never generate a final grade. Never modify the source submission file. Never use generic praise.* These become CLAUDE.md entries and (Chapter 7) Hooks.

The five exercises take 25–35 minutes. They produce a CLAUDE.md update, a TO plan, and a list of PA targets that the Chapter 8 dangerous-middle chapter will tighten.

---

## Classroom activity: capacity-labeling on a transcript

For your students' classroom:

Provide a transcript of a Claude Code session for a small build (a data-processing script, a small simulation, a feature addition). Each prompt and response is unlabeled.

Their task: label every moment where a supervisory capacity fired. Use the abbreviations PA, PF, TO, IJ, EI. Multiple labels per moment are allowed. Some moments may have no labels (mechanical execution).

Pair students. Disagreements are the discussion. A peer who labels `[IJ]` for a moment the partner labeled `[PA]` produces the most useful conversation — was the capacity that fired about *gap between command and intent* (PA) or *supplying intent the model could not* (IJ)?

The activity teaches diagnostic vocabulary. After two transcripts, students label fluently. The skill transfers — they can name what they are doing in their own builds and what they are not.

This activity maps to the student book's Chapter 5 (Supervisory Capacities). Your design here is the worked example.

---

## Common misconceptions

**"Five things on every prompt."** No. Each capacity fires at a different moment. A given prompt typically exercises one or two; the full set is exercised across a build.

**"Conscious performance every time."** With practice the capacities fuse into a single conducting motion. The labels are scaffolding; once reflexive, the labels recede.

**"PA = paranoia."** PA is engagement, not anxiety. The Anthropic 2026 RCT data is clear: engagement-pattern users outperform low-engagement users substantially. The capacity is curiosity directed at the gap between command and intent.

**"Capacities are soft skills."** They are practicable operations. They can be exercised, named, diagnosed when absent, improved through deliberate practice. Calling them "soft" is the language that produces low-stakes treatment; the chapter's framing is supervisory-engineering.

**"The model will improve and replace these."** The Contested Claims sidebar in the students book applies here: *currently requires human judgment*. The architectural reason: the capacities operate on the teacher's classroom-specific context, which is not in the model's training data. Tool improvement changes *where* capacities fire, not *whether* they are needed.

---

## Exercises

1. **(Apply)** Take a transcript of your most recent Claude Code session (5+ prompts). Label every capacity-firing moment using PA, PF, TO, IJ, EI. Identify the capacity you exercised most; the one you exercised least.

2. **(Apply)** For the grading tool you will build in Chapter 6 onward, write the five-step preparation in the Teacher build section. Save as `prep.md` alongside CLAUDE.md.

3. **(Evaluate)** Recall a build that went wrong (yours or a colleague's). Decompose by capacity. Name which was absent. Write the CLAUDE.md entry that would prevent recurrence.

---

## What would change my mind

The chapter's strong claim is that **naming the five capacities improves teacher performance** on subsequent builds compared to unnamed practice. If a controlled comparison found no measurable difference between teachers who learned the capacity vocabulary and teachers who only practiced the discipline, the names become organizational scaffolding rather than load-bearing pedagogical tools. The chapter would still teach them for diagnostic vocabulary; the urgency softens.

I expect the difference to be substantial because the Anthropic 2026 RCT validates the underlying engagement-pattern framework, and the capacities are a finer-grained version of those patterns.

---

## Still puzzling

- **Stability under tool improvement.** Future Claude Code generations may automate parts of PA (anomaly detection in Claude's own output; consistency checks across multi-prompt drafts). The capacities migrate, they do not disappear. PA may move from *the teacher notices* to *the teacher reads the CLI's anomaly flag and decides*. The supervisory work persists.

- **Distinguishability in practice.** PA and IJ overlap. EI overlaps with both. The labels are useful even when fuzzy. The classroom activity's disagreements typically clarify the distinctions better than definitions do.

- **Whether five is the right number.** Operational, not theoretical. Four conflates; six over-specifies. Five is the working choice; revise if the diagnostic value drops.

---

## Links

- [Anthropic 2026 RCT — *How AI Assistance Impacts the Formation of Coding Skills*](https://www.anthropic.com/research/AI-assistance-coding-skills) — the engagement-pattern validation.

---

##  AI Wayback Machine
 **Norbert Wiener** (1894–1964) — American mathematician and founder of cybernetics, whose *The Human Use of Human Beings* (1950, rev. 1954) argued that **the question for any human-machine system is not whether the machine works, but whether the human-machine feedback loop maintains its goal**.[^2] Wiener's framing was the loop, not either party. The system's intelligence lives in the corrections the human makes to the machine and the corrections the machine makes possible for the human. Without the corrections, the loop is open and the system drifts.

The five supervisory capacities are Wiener's feedback loop made specific to Claude Code. PA catches the drift in Claude's output. PF closes the loop upstream by formulating the right problem. TO selects the appropriate feedback channel (CLAUDE.md, Skill, Hook, Subagent) for each step. IJ supplies the context the model cannot infer. EI holds the whole loop toward the original goal across multiple turns. Each capacity is a feedback mechanism. The discipline is the practice of keeping the loop closed when the temptation is to let Claude run open and trust the output.

Wiener's most quoted warning, in the 1954 revision: *"We can be humble and live a good life with the aid of the machines, or we can be arrogant and die."* The chapter's framing is less dramatic. The teacher who exercises the five capacities is humble in Wiener's sense — neither delegating judgment to Claude nor refusing the agentic loop entirely, but *conducting* both. The teacher who delegates the capacities is the open-loop case Wiener warned about. Seventy years later, the warning still holds.

---

## Bridge

The capacities are named. Chapter 6 introduces the Claude Code feature that makes specific workflows reusable across builds — Skills.

---

[^1]: Anthropic. *How AI Assistance Impacts the Formation of Coding Skills*. Anthropic Research, 2026. anthropic.com/research/AI-assistance-coding-skills.
[^2]: Wiener, N. *The Human Use of Human Beings: Cybernetics and Society*. Houghton Mifflin, 1950; rev. 1954. The 1954 revision is the standard reference.
