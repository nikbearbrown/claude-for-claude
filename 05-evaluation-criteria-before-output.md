# Chapter 5 — Evaluation Criteria Before Output

## The Moment You Realize You Have No Standard

A grant writer submits a proposal drafted with Claude's help. The program officer rejects it for reasons the writer could have anticipated: the need statement used the wrong poverty-rate figures, the evaluation plan lacked measurable indicators, and the budget narrative did not match the project description. None of these were obvious problems when she read the draft. The prose was clean. The sections were complete. The structure was right.

What was missing was not in the output. It was never in the prompt. She had not told Claude — or herself — how she would know whether the draft was ready to submit. She had specified the task, the context, and the format. She had not specified the criteria for success.

This chapter teaches you to write those criteria before the output exists, not after it disappoints you.

---

## What This Chapter Lets You Do

After reading this chapter, you will be able to:

- State acceptance criteria in a prompt so Claude knows what the output must satisfy
- Include failure criteria — conditions that would make the output wrong — before generation begins
- Use rubrics as prompt components, not just post-hoc grading tools
- Build source-check and calculation-check requirements into the specification
- Ask "what would make this wrong?" as a prompt design habit
- Understand why evaluation criteria make you a better specifier, not just a better reviewer

This is the chapter most directly tied to the book's thesis: a prompt is a work order, and a good work order includes the acceptance test.

---

## The Core Idea: Criteria-First Specification

In software engineering, test-driven development is a practice where programmers write the tests before writing the code. The tests define what the code must do. The code is written to pass the tests. The human reviews the result against the tests, not against vague intuition.

Criteria-first prompting applies the same logic to Claude. You write the criteria the output must meet before Claude generates the output. Claude uses those criteria as part of the specification. You use them as the review checklist. The criteria make the task clearer and the review faster (NIST AI Risk Management Framework 2023; acceptance criteria and test-driven development practice).

This is not about demanding perfection from Claude. It is about being honest, before generation, about what you actually need. The act of writing criteria often reveals requirements you had not consciously articulated. That discovery is valuable on its own.

---

## Four Types of Evaluation Criteria

**Accuracy criteria** specify what the output must get right. "Every factual claim must be traceable to a provided source." "Calculations must be checked: show the arithmetic." "No statistic may appear without its date and source."

**Completeness criteria** specify what the output must include. "The output must address each of the five program components." "Every recommendation must be accompanied by a rationale." "The summary must cover the method, the finding, and the limitation — in that order."

**Format criteria** specify what the output must look like. "Under 500 words." "Three sections with explicit headers." "One table summarizing all quantitative findings." "No bullet points — prose only." These overlap with constraints from Chapter 3 but are placed here in the context of review: format criteria are checkable.

**Failure criteria** specify conditions that would make the output wrong regardless of its other merits. "If any number in the output cannot be traced to the provided data, the output fails." "If the tone shifts from neutral to advocate, the output fails." "If the output includes a recommendation not supported by the evidence, the output fails." Failure criteria are powerful because they name the catastrophic rather than the optimal.

---

## Before and After: Adding Criteria to a Prompt

### Weak Prompt

> Write a one-page executive summary of the attached program evaluation report.

**What Claude produces:** A one-page summary that covers what Claude considers the most important material in the report. It may miss key findings the commissioning agency considers primary. It will use its judgment about which numbers to foreground. It may round figures. It will probably end with something that reads like a conclusion even if the report itself is inconclusive.

The output will look like a good executive summary. Whether it is a good executive summary of this report, for this agency, for this decision — that is a question the prompt did not ask Claude to answer.

### Why It Fails

The prompt is a task with no success condition. Claude has no way to know:

- Whether "one page" means 250 words or 500
- Which findings the commissioning agency considers primary
- Whether conclusions should be stated or whether the summary should remain descriptive
- How to handle findings that the report qualifies or disputes
- Whether the format matches the agency's template

So Claude applies defaults. The output reflects what Claude thinks an executive summary should look like, not what this executive summary needs to be.

### Revised Prompt

> Write a one-page executive summary of the program evaluation report attached below. The summary must meet the following criteria:
>
> **Completeness:** Cover the program's stated goals, the evaluation method, the three primary outcome measures (from Table 2), and any limitations the report identifies as significant.
>
> **Accuracy:** Do not round or paraphrase any numbers from Table 2. Use the exact figures. Do not add interpretations the report itself does not offer.
>
> **Tone:** Neutral and descriptive. The summary presents findings; it does not advocate for or against the program. If the evidence is mixed, say it is mixed.
>
> **Format:** Under 400 words. Use the following four headers: Program Overview, Evaluation Method, Key Findings, Limitations.
>
> **Failure condition:** If the report's own limitations section qualifies a finding as preliminary or inconclusive, the summary must reflect that qualification. A summary that presents a qualified finding as definitive fails this criterion.
>
> [Report text]

**What Claude produces:** A summary structured by the four named headers, covering the goals, method, three specific outcome measures from Table 2 with exact figures, and the limitations. The tone does not advocate. Qualified findings are presented as qualified.

### What Changed

The revision added four positive criteria (completeness, accuracy, tone, format) and one failure criterion (misrepresentation of qualified findings). Each criterion is checkable. When reviewing the output, the writer can run five specific checks rather than reading for general impressiveness.

---

## Rubrics as Prompt Components

A rubric is a structured set of criteria that grades a performance against defined standards. Rubrics are widely used in education and academic peer review — they make evaluation consistent, transparent, and teachable (rubric design literature; academic peer review and checklist methods).

A rubric in a prompt is the same tool in a new context. Instead of grading a student's essay, you are specifying the standard for Claude's output. The rubric functions as both specification and review tool.

A simple rubric for a policy memo might read:

> **Evaluation rubric for this memo:**
>
> | Criterion | Required standard |
> |---|---|
> | Claim support | Every claim traces to a cited source in the materials provided |
> | Concision | No sentence over 25 words; no paragraph over 80 words |
> | Audience | Assumes no technical background; all jargon defined at first use |
> | Neutrality | Presents options without recommendation unless explicitly asked |
> | Completeness | Addresses each of the three policy questions posed below |

Including this rubric in the prompt does two things: it gives Claude explicit criteria to work toward, and it gives the writer a checklist for review. The same document serves both purposes.

---

## The "What Would Make This Wrong?" Question

The most productive evaluation prompt design question is: what would make this output wrong?

This question is uncomfortable because it requires acknowledging failure modes before they happen. But that discomfort is the point. Failure modes you can name in advance are failure modes you can prevent with criteria.

Common answers to "what would make this wrong?":

- "It would be wrong if any number is inaccurate." → Add accuracy criterion.
- "It would be wrong if it sounds like an endorsement instead of an analysis." → Add tone/neutrality criterion.
- "It would be wrong if it misses the third section of the report entirely." → Add completeness criterion.
- "It would be wrong if the conclusion overstates the evidence." → Add a failure criterion flagging overstatement.
- "It would be wrong if it uses private identifiers from the dataset." → Add a privacy constraint (see Chapter 3).

Writing two or three "what would make this wrong?" failure criteria before generating output is one of the highest-value prompt design habits in this book.

---

## Source-Check and Calculation-Check Criteria

Two specific criteria deserve their own treatment because they address the two most common factual failure modes in Claude's outputs: fabricated or misattributed sources, and incorrect arithmetic.

**Source-check criteria** require Claude to trace every factual claim to the materials provided. A source-check criterion looks like:

> "For each factual claim in this output, add a parenthetical noting the source within the provided materials (e.g., 'Report p. 4'). If a claim cannot be traced to a provided source, mark it [unverified] rather than including it unmarked."

This does not guarantee that every claim is accurate — it makes the sourcing visible so the human can verify. That is the goal: make the review possible, not automated.

**Calculation-check criteria** require Claude to show its arithmetic. A calculation-check criterion looks like:

> "Show all calculations as intermediate steps. Do not present only final figures. If a calculation involves rounding, state the rounding rule used."

Claude's arithmetic is not always reliable [verify — current as of writing], particularly for multi-step calculations or unit conversions. Showing steps makes errors visible; presenting only results hides them. The human reviewing a calculation with visible steps can spot the error. The human reviewing a final figure cannot.

---

## Criteria-First as a Writer's Practice

There is a benefit to criteria-first prompting beyond better Claude outputs: it makes you a clearer specifier. Writing criteria forces you to articulate what success means before you see any output. That articulation is useful independent of Claude.

Grant writers who write acceptance criteria before drafting learn something about what the funder requires. Analysts who write failure criteria before running an extraction learn something about what they will not accept. Teachers who write rubrics before generating materials learn something about what the materials must do.

The practice transfers. The discipline of writing "what does good look like?" and "what would make this wrong?" before producing anything is a general thinking skill. Claude prompting is the occasion to develop it.

---

## The Human Gate

Criteria-first prompting does not automate review. It structures it.

Each criterion in the prompt becomes a review gate. After the output is produced:

- Run the accuracy checks: is every figure traceable?
- Run the completeness checks: is every required section present?
- Run the tone check: does the output hold the specified register throughout?
- Run the failure-condition check: does the output misrepresent any qualified finding as definitive?

This is faster than reading the output for general quality. It is also more reliable: you are testing against named criteria, not judging against impressionistic standards that vary with your mood or time of day.

The human's responsibility is to set criteria that matter, and to actually run the checks. Claude can produce an output that meets all stated criteria and still be unsuitable for use — because the criteria themselves were incomplete, or because the task required judgment that no criterion can substitute for. The goal of criteria-first is not to remove judgment from review. It is to make the judgment harder to skip.

Human factors research on automation bias (Mosier & Skitka 1996; Parasuraman & Manzey 2010) consistently shows that when an AI system presents confident-looking output, users are less likely to apply independent verification. Explicit criteria are a structural counter to that tendency: they make verification a named step in the process rather than an optional afterthought.

---

## Common Mistakes

**Stating criteria only after the output disappoints you.** "The summary should have been more neutral" is a lesson from a failed output. "The summary must maintain neutral tone throughout and not advocate for a particular conclusion" is a criterion that prevents the failure. The first is retrospective. The second is prospective.

**Writing criteria that are not checkable.** "The output should be high quality" is not a criterion — it is a wish. "Every sentence in the output must make a single, verifiable claim" is a criterion. If you cannot check it, it is not doing work.

**Omitting failure criteria.** Positive criteria tell Claude what to achieve. Failure criteria tell Claude what conditions constitute a failure regardless of other merits. Both are necessary. A memo that meets four criteria but misrepresents the evidence on the fifth has still failed.

**Treating rubrics as substitutes for substantive knowledge.** A rubric can tell Claude to match the required format and cite sources. It cannot tell Claude whether the interpretation of those sources is domain-appropriate. The human reviewing the output must bring the domain knowledge. The rubric structures the review; the human brings the standard.

**Writing criteria at the end of a long prompt.** Criteria buried after pages of context may receive less weight than criteria stated prominently and early. If acceptance criteria are critical, consider placing them at or near the beginning of the prompt, or in a clearly labeled section before the task [verify — current as of writing for prompt position effects].

---

## Try This

**Exercise 1 — Criteria Before Draft**
Choose a task you regularly ask Claude to help with — a summary, an email, a report section. Before writing any task description, write the acceptance criteria: at least two positive criteria (what it must include or achieve) and one failure criterion (what would make the output unacceptable regardless of other qualities). Then write the task description incorporating those criteria. Compare this output to the last time you ran the task without explicit criteria.

**Exercise 2 — "What Would Make This Wrong?"**
For a prompt you are about to write, sit for two minutes and answer: what are the three ways this output could most plausibly fail? Convert each answer into a failure criterion. Add all three to the prompt. After receiving the output, check each one. Did any of the failure modes appear?

**Exercise 3 — Rubric as Prompt**
Design a rubric for a recurring output type in your work — a meeting summary, a literature note, a client report section. Use the table format from the example above: criterion name and required standard. Use this rubric in at least three prompts. After each use, revise one criterion based on what you learned. At the end of three iterations, you have a tested rubric.

---

## What Would Change My Mind

This chapter argues that stating evaluation criteria before output makes outputs more reviewable and reduces the risk of plausible-but-wrong results. I would revise that claim if:

- Evidence showed that criteria-first prompts produced outputs that were technically compliant but missed the real task — suggesting that criteria formalization introduces its own rigidity errors
- Research showed that the psychological and cognitive costs of writing criteria exceeded the review savings, particularly for low-stakes tasks — which would suggest that criteria-first is a high-overhead approach suited only for high-stakes use
- Claude's capacity to self-evaluate against stated criteria became reliable enough to substitute for human review [verify — current as of writing], which would change the role of criteria from review scaffolding to automated checking

Until then: write the criteria before you write the task. The discipline pays in review time and in the quality of what you decide to ask for in the first place.

---

## Still Puzzling

- How many criteria is too many? At some point, a long list of criteria may overwhelm the prompt's context, or produce outputs that satisfy all criteria mechanically while missing the spirit of the task.
- Can criteria be learned and reused across similar tasks automatically, rather than being rewritten each time? This is partly a workflow and prompt-library question (see Chapter 12).
- Does the order of criteria in a prompt affect which receive more weight in Claude's output? Preliminary evidence from prompt engineering practice suggests position matters, but systematic research is thin [verify — current as of writing].

---

## Bridge to Chapter 6

You have now built the core scaffold: task, context, constraints, examples, and evaluation criteria. What happens when the output does not meet the criteria? Chapter 6 teaches iteration and revision loops — structured patterns for diagnosing a failure, writing a targeted revision prompt, and improving the output without losing the original specification.

---

## Sources Used

- Anthropic prompt engineering documentation. https://docs.anthropic.com/ [verify — current as of writing]
- NIST AI Risk Management Framework (2023). National Institute of Standards and Technology.
- UNESCO generative AI guidance (2023). United Nations Educational, Scientific and Cultural Organization.
- Mosier, K. L., & Skitka, L. J. (1996). Human decision makers and automated decision aids: Made for each other? In R. Parasuraman & M. Mouloua (Eds.), *Automation and Human Performance*. Lawrence Erlbaum.
- Parasuraman, R., & Manzey, D. H. (2010). Complacency and bias in human use of automation. *Human Factors*, 52(3), 381–410.
- Beck, K., et al. (2001). Manifesto for agile software development. Acceptance criteria and test-driven development practice.
- Popham, W. J. (1997). What's wrong — and what's right — with rubrics. *Educational Leadership*, 55(2), 72–75.
- Academic peer review and checklist methods. Moher, D., et al. (2009). Preferred reporting items for systematic reviews and meta-analyses: The PRISMA statement. *PLOS Medicine*, 6(7).
