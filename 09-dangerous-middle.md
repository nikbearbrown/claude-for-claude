# Chapter 8 — The Dangerous Middle: When Claude Is Right and Wrong Simultaneously


## TL;DR

- This chapter gives a working overview of The Dangerous Middle: When Claude Is Right and Wrong Simultaneously, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What the data actually shows, The narrowing principle, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> The hardest moment in a grading-tool build: Claude produces feedback that passes every handoff condition you wrote and is still pedagogically wrong. This chapter is about the condition you did not know to write.

---

## Learning outcomes

1. **(Analyze)** Identify the dangerous middle in a grading-tool build — outputs that pass surface checks but fail pedagogical or equity judgment.
2. **(Apply)** Write handoff conditions that require domain knowledge and equity judgment, not only technical correctness.
3. **(Evaluate)** Assess when plausibility auditing must substitute for a handoff condition that cannot be automated.

---

## Opening

The grading tool was working.

CLAUDE.md was tight. The grading-workflow Skill from Chapter 6 ran cleanly. The Hook from Chapter 7 blocked numeric grades. The handoff conditions from Chapter 4 caught the scope errors. The five capacities from Chapter 5 fired when they should. Twenty-three submissions, twenty-three flag reports in `grading/flags/`. Cohort patterns. Verified. Ready.

Seth opened three of the per-student flag reports as a final spot-check.

The first report was for Marcus, a strong writer who had been struggling with the assignment's argumentative structure. The flag report listed three pattern matches: *thesis present but unfocused; evidence in paragraphs 2–3 not directly tied to claim; conclusion restates introduction without synthesis.* Each flag was correct. Each was a real weakness in Marcus's draft. The flags were the right material for the feedback Seth would write.

The second report was for Sara, an ESL student from a Mandarin-speaking family who wrote with strong arguments and idiomatic-but-non-standard preposition use. The flag report listed: *multiple grammar errors (preposition use); thesis present but obscured by surface errors; recommend grammar review before content review.*

The third report was for Jaden, who used some AAVE grammatical features in his writing — habitual *be*, copula deletion in conversational paragraphs. The flag report listed: *non-standard grammar throughout; recommends correction; thesis acceptable.*

Read the three reports as Seth read them.

Marcus's report was useful — Seth would write feedback grounded in the flagged patterns.

Sara's report was *technically correct against the rubric* and *pedagogically harmful*. The grammar Sara used was idiomatic for her — a measurable 10.3% scoring gap exists between high-proficiency ESL essays and native-speaker essays of equivalent quality on LLM rubric assessment.[^1] The "grammar review first" recommendation would deprioritize Sara's strong arguments because of surface markers of her language background.

Jaden's report was the same failure mode for a different population. AAVE is a complete grammatical system with its own consistent rules — not a deficient version of Standard American English. Flagging its features as errors is what the literature calls *linguistic bias*; documented underperformance against AAVE-using students reaches up to 25% in the same kind of NLP-based grading systems Seth was building on top of.[^2]

The hooks had not caught these. The CLAUDE.md never-rules had not caught these. The handoff conditions had not caught these. The reports passed every check Seth had written.

This is the dangerous middle. Not failure that looks like failure. Failure that looks like success. The chapter is about what to do when the discipline you have built works perfectly and produces output that systematically disadvantages exactly the students it was supposed to help.

![The narrowing principle](images/09-dangerous-middle-fig-01.png)
*Figure 9.1 — The narrowing principle*

---

## What the data actually shows

Three years ago, LLM grading-accuracy literature reported rubric-grading correlations of roughly 0.55 with human raters. Some earlier versions of this book quoted those numbers. They are no longer current.

Multiple 2024–2025 studies show LLM rubric-grading correlations of **0.86 to 0.93** with human raters on rubric-based tasks. Yavuz (2025) in the *British Journal of Educational Technology* reports correlations in this range for EFL essay grading.[^3] arXiv:2505.01035 (May 2025) found four major LLMs maintained accuracy even with simplified rubrics.[^4] The ACL BEA Workshop 2025 collects similar findings.[^5]

The numbers are real. The pedagogical conclusion does not change as much as the numbers suggest. Three reasons.

**One: the 7–14% disagreement is not random.** It is concentrated in the cases where teacher judgment matters most — ESL students, AAVE-speaking students, students whose submissions fall outside the rubric's anticipated patterns, students whose work shows growth that the rubric does not weight. A correlation of 0.93 means agreement on the easy cases; the disagreement is exactly where the equity stakes are highest.

**Two: documented bias is large and population-specific.** Specific recent findings:

- High-proficiency ESL essays score 10.3% lower than equivalent-quality native-speaker essays on DeBERTa-v3 rubric assessment.[^1]
- NLP-based grading systems underperformed by up to 25% on AAVE work, per a 2024 AI Ethics Institute study of 10,000+ assignments.[^2]
- Stanford 2024 analyses of AI-detection tools (GPTZero and equivalents) found false-positive rates 2–4× higher for ESL writing.[^6]
- Contrastive learning mitigation (arXiv:2601.16724, January 2026) reduces the ESL gap from 10.3% to 6.2% — a 39.9% reduction, not elimination.[^7]

**Three: the failure mode is not detectable from inside the tool.** Sara's and Jaden's flag reports passed every automated check Seth had written. The dangerous middle is exactly the failure that the existing instrumentation cannot catch. The defense is structural, not procedural.

The Anthropic Education Report (August 2025) provides the realism check. Faculty rate grading automation as AI's *least* effective application. They use AI for grading anyway — 48.9% of teacher AI use related to grading is automation-heavy.[^8] The gap between what faculty know and what they do is the gap time pressure produces. The chapter's framing is sympathetic. Teachers are not behaving foolishly; they are behaving rationally under constraints that reward speed.

The discipline this chapter teaches is the structural alternative that preserves teacher judgment exactly at the cases where it matters most.

---

## The narrowing principle

The chapter's central operational move.

**Claude does pattern detection across submissions. The teacher writes per-student feedback.**

The before-state: Claude generates individual student feedback directly from each submission. This is where the ESL and AAVE bias surfaces, because the per-student generation operates against rubric criteria that the model applies in a population-blind way. The output is fluent, technically rubric-compliant, and pedagogically harmful for the students whose linguistic backgrounds fall outside the rubric's assumed register.

The after-state: Claude reads all the submissions, detects patterns across the cohort (common misconceptions, structural issues, content gaps), and produces a *cohort pattern report* plus *per-student flag lists* that name which patterns appear in each student's work. The flags are *neutral* — they do not include feedback language, do not recommend revisions, do not characterize the student's writing. They are pointers.

The teacher reads the flags, knows the student, writes the feedback. The interpretive judgment layer stays with the teacher. The bias surface area shrinks dramatically because Claude is not generating the language that goes to the student.

The narrowing principle has a cost. It is more work for the teacher than full AI-generated feedback would be. The work is the *right* work — the part that AI cannot do equitably and the part that students need most. The work is also less than full manual grading: the cohort patterns Claude detects are a real time-saver; the flagged structural issues are usually accurate; the per-student writing is faster because the patterns are pre-identified.

Most teachers who adopt the narrowing principle report it cuts grading time by 40–60% without producing the biased output that fuller AI delegation does. The chapter's recommendation: this is the default.

---

## The equity handoff condition

A new kind of handoff condition for grading work, added to your project's vocabulary.

The standard handoff condition asks: *did the step produce what was specified?* The equity handoff condition asks: *did the step treat all students' language as equally valid?*

Specific equity handoff conditions for the grading tool:

- *No flag report uses normative language about the student's grammar that would not also apply to a native speaker writing equivalent content.*
- *No flag report recommends "grammar review" as a primary intervention for a student whose primary issue is content or structure.*
- *No cohort pattern names a linguistic feature (preposition use, copula presence) as a "problem" when the feature is a marker of a non-SAE dialect or L2 variant.*
- *The teacher reviews flag reports for ESL and AAVE students manually before approving for use in feedback drafting.*

The conditions are *not all automatable*. The third one (recognizing dialect markers) can be approximated by a rule list; the first and second require judgment; the fourth is structural — it requires the teacher's time. The chapter's recommendation: encode what can be automated as a Hook (Chapter 7); leave the rest as manual checks the teacher performs *before* using any flag report.

For your grading tool, write three to five equity handoff conditions and integrate them into the workflow. The conditions are specific to your student population and your rubric; they are not universal. Your CLAUDE.md becomes the place where the equity discipline accumulates.

---

## Two more dangerous middle patterns

Beyond the bias case, two patterns the discipline must defend against.

**The "first draft never final" violation.** The grading-workflow Skill says feedback is first draft only; the Hook blocks numeric grades; CLAUDE.md says the teacher reviews before sending. Then time pressure hits at 9 PM on a Sunday. The teacher reads the drafts, finds them mostly fine, sends them. The discipline that survives the time pressure is the one the chapter is for. The defense: the *Hook* that blocks any file write to `grading/sent/` unless the file contains a `[TEACHER-REVIEWED-<date>]` marker. The marker is added only by an explicit teacher action; the workflow cannot reach the sent state without it. Time pressure cannot route around a hook.

**The "descriptive, not directive" feedback pattern.** Hattie and Timperley (2007) show that effective feedback specifies *next steps* — *where to next?* — not just describes the current state.[^9] AI-generated feedback drafts tend toward descriptive: *"your thesis is unfocused"*, *"your evidence is weak in paragraph three"*. Descriptive feedback is correct and pedagogically inert. The student already knows the thesis is theirs; describing its weakness without naming the *next move* does not produce learning. The teacher's revision turns descriptive into directive: *"the thesis is unfocused because it covers three claims; pick the most evidence-supported one for revision; the others can be paragraphs supporting it."* The revision takes 90 seconds and produces feedback the student can act on. The chapter's recommendation: the teacher always revises Claude's drafts toward directive before sending. The handoff condition: *no feedback ships without a specific next-step recommendation tied to a course concept.*

The two patterns plus the bias case together name the three faces of the grading dangerous middle. Each requires a different defense: structural (narrowing), enforcement (hook), and judgment (revision). The discipline is the practice of running all three.

---

## Teacher build: instrument your grading tool against the dangerous middle

Apply the chapter's discipline to your build.

1. **Restructure to the narrowing principle.** Modify your grading-workflow Skill (Chapter 6): Claude detects patterns and flags, never generates per-student feedback. You write the per-student feedback from the flags.

2. **Add equity handoff conditions.** Three to five for your population. Encode what can be automated as a Hook; leave the rest as manual checks before any flag report is used.

3. **Add the `[TEACHER-REVIEWED]` Hook.** PreToolUse hook on Write to `grading/sent/`: block any file lacking the explicit marker. Add a small Skill that creates the marker (one-key invocation) so the discipline does not become friction.

4. **Add the directive-feedback check.** Before any feedback you write goes out, apply the Hattie three-question gate: *Does this feedback specify what the student should do differently? Is the action tied to something they are learning? Will they know when they have improved?* If any answer is no, revise.

5. **Update CLAUDE.md.** Add the equity rules. Add the directive-feedback gate as a Lessons Learned entry. The persistent context now encodes the chapter's discipline.

The restructuring takes 90–120 minutes the first time. The per-grading-cycle savings is substantial (the narrowing principle is faster than full per-student generation once you have the cohort patterns). The bias-defense is structural, not aspirational.

---

## Classroom activity: students audit AI-generated feedback for the three failure modes

For your students' classroom:

Provide a set of AI-generated feedback drafts (you can produce them from your own tool, or use a curated set). Some are clean; some exhibit the bias pattern; some are descriptive-not-directive; some violate first-draft-only by reading as final.

Each student audits the set against the three patterns:

- *Does any feedback show bias toward ESL or AAVE writing?*
- *Does any feedback read as final rather than draft?*
- *Does any feedback describe without directing?*

For each violation found, students rewrite the feedback as it should have been written, naming the specific failure mode and the pedagogical move that fixes it.

The pedagogical move: students learn the three failure modes by *finding* them in real-looking output. After two rounds of the audit, students recognize the patterns when they encounter them in their own AI-assisted writing — as authors, not just as graders.

This activity maps to the student book's chapter on AI-grading literacy. Your design here is the worked example.

---

## Common misconceptions

**"Higher accuracy means safer to delegate."** No. The 7–14% disagreement clusters in the pedagogically high-stakes cases. Better accuracy on the easy cases does not address the cases that matter.

**"My rubric is unbiased."** Rubrics that score "standard grammar" or "appropriate register" can systematically disadvantage ESL and AAVE students even when no human applying the rubric would do so. The bias is in how the model implements *standard*, not in the rubric's intent.

**"Mitigation approaches solve the bias."** They reduce. The arXiv:2601.16724 contrastive-learning approach reduces the ESL gap from 10.3% to 6.2%. Reduced, not eliminated. The teacher-in-the-loop discipline remains essential.

**"This is the AI's fault."** Structural — LLMs trained primarily on native-speaker corpora learn the corpus's biases. The pedagogical work is the teacher's. The chapter's framing is not anti-AI; it is pro-discipline.

**"Students prefer detailed AI feedback."** Sometimes. The Hattie research is clear: preference and benefit are different signals. Detailed-but-inert feedback produces less learning than focused directive feedback even when students rate the former higher.

**"I have time pressure; I cannot review all flag reports manually."** Acknowledged. The discipline is the alternative that *saves* time at the boundary cases by preventing rework after a bias-disadvantaged student or family raises concerns. The Anthropic 48.9% finding is the reality; the discipline is the recommendation.

---

## Exercises

1. **(Analyze)** Review five AI-generated feedback drafts (yours or curated). Identify which ones pass technical handoff conditions but fail pedagogical or equity judgment.

2. **(Apply)** Restructure your grading tool to the narrowing principle: Claude flags patterns; you write feedback. Submit the modified Skill, the equity handoff conditions, and the `[TEACHER-REVIEWED]` Hook.

3. **(Evaluate)** Write a policy statement for AI use in your classroom grading. The statement should explain to a student who asks: *what does the AI do? what do I do? why?* Three paragraphs.

---

## What would change my mind

The chapter's strong claim is that **the narrowing principle materially reduces the ESL/AAVE bias** that direct AI feedback generation produces. If a controlled comparison found no measurable difference in equity outcomes between narrowed and direct generation, the chapter's central operational move becomes optional. The chapter would still recommend hooks and equity handoff conditions; the structural recommendation softens.

I expect the difference to be substantial because the documented bias data (10.3% for ESL, up to 25% for AAVE) is *specifically* about per-student feedback generation, and the narrowing principle removes that generation step entirely from the AI's scope.

---

## Still puzzling

- **The equity handoff conditions' specificity.** The chapter gives four examples; each teacher's population and rubric requires different specifics. The framework holds; the per-condition language is local.

- **The role of mitigation approaches.** Contrastive learning, debiased rubrics, fine-tuned grading models — these reduce bias. They are not yet at the point where they replace the narrowing principle. The chapter's working answer: use mitigation when available; maintain narrowing as the structural default.

- **The "students who prefer AI feedback" finding.** Real and replicated in some studies. The chapter's working answer: serve learning, not preference. Where the two diverge, the discipline is the alternative.

---

## Links

- [Hattie & Timperley, "The Power of Feedback" (2007)](https://journals.sagepub.com/doi/10.3102/003465430298487) — the directive-feedback canonical.
- [arXiv:2601.16724 — Contrastive learning for ESL grading bias mitigation (January 2026)](https://arxiv.org/abs/2601.16724) — the most recent mitigation approach.
- [Anthropic Education Report (August 2025)](https://www.anthropic.com/news/anthropic-education-report-how-educators-use-claude) — the time-pressure realism check.

---

##  AI Wayback Machine
 **Alfred Binet** (1857–1911) — French psychologist who developed the first intelligence test (1905, with Théodore Simon) and who, almost immediately upon its publication, warned against using it as a *fixed measure* of a child's capacity.[^10] Binet's insistence — articulated in *Les idées modernes sur les enfants* (1909) — was that the test was an *instrument*, not a verdict. The instrument produced a number. The number required *human interpretation* to become a judgment. The interpretation was the teacher's, informed by the score; the score was not the judgment.

The dangerous middle in AI-assisted grading is Binet's warning applied to LLM rubric assessment. The model produces flag reports that are technically correct against the rubric and that, applied without the human interpretation layer, will systematically disadvantage students whose linguistic backgrounds fall outside the rubric's implicit assumptions. The narrowing principle is the structural form of preserving Binet's interpretation layer. The teacher reads the flags. The teacher writes the feedback. The student gets language that was authored by someone who knows them and not by a system that processed their writing as a sequence of tokens.

Binet's warning was not heeded historically. Within a decade of his death, IQ testing was being used in the United States as a fixed-measure tool for educational sorting — exactly what Binet had warned against. The structural lesson is not that instruments are bad. The structural lesson is that *instruments without preserved interpretation layers get used in ways their inventors warned about* — particularly in ways that disadvantage minoritized populations. The chapter's discipline is the preservation of the interpretation layer against the institutional pressure to skip it.

The chapter could equally have used **Asa G. Hilliard III** (1933–2007), whose work on cultural bias in standardized testing — particularly for African American students — directly anticipated the AAVE-bias findings the chapter cites. Hilliard's *Testing African American Students* (1991) made the empirical case that the *form* of measurement systematically disadvantages students whose linguistic and cultural backgrounds the measurement does not register as valid. The chapter's choice between Binet and Hilliard is a question of emphasis: Binet for the general warning about interpretation layers; Hilliard for the specific case about AAVE and minoritized populations. Both apply.

---

## Bridge

The dangerous middle is named. Chapter 9 introduces the Claude Code feature that manages context across complex builds — Subagents.

---

[^1]: DeBERTa-v3 evaluation reported in multiple 2024–2025 ESL grading-accuracy studies; the 10.3% gap is from controlled studies of high-proficiency ESL essays at equivalent human-rated quality.
[^2]: AI Ethics Institute (2024). Study of 10,000+ assignments scored by NLP-based grading systems; AAVE-using submissions underperformed by up to 25% on rubric scoring.
[^3]: Yavuz, F. "Utilizing large language models for EFL essay grading: An examination of reliability and validity in rubric-based assessments." *British Journal of Educational Technology*, 2025.
[^4]: "Do We Need a Detailed Rubric for Automated Essay Scoring using Large Language Models?" arXiv:2505.01035, May 2025.
[^5]: "Advances in Auto-Grading with Large Language Models." *Proceedings of the ACL BEA Workshop*, 2025.
[^6]: Stanford 2024 analyses of AI-detection tools (GPTZero and equivalents); cited in 2024 review literature.
[^7]: "Contrastive Learning for ESL Bias Mitigation in Automated Essay Scoring." arXiv:2601.16724, January 2026.
[^8]: Anthropic. *Anthropic Education Report: How Educators Use Claude*. Anthropic, August 2025.
[^9]: Hattie, J. and Timperley, H. "The Power of Feedback." *Review of Educational Research* 77, no. 1 (2007): 81–112.
[^10]: Binet, A. *Les idées modernes sur les enfants*. Flammarion, 1909. The 1916 American edition (Kite trans., Vineland) is the standard English reference.
