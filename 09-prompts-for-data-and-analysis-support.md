# Chapter 9 — Prompts for Data and Analysis Support

**Capability built:** Ask Claude to help plan and interpret analysis without surrendering statistical judgment.

---

## Opening Scene

A policy analyst pastes a dataset summary into Claude and types: "What does this tell us?" Claude returns three paragraphs describing trends, noting that income correlates with outcome, and suggests that "the results indicate a significant relationship." The analyst copies the paragraph into a report. The problem: the variable labeled "income" in the dataset is categorical, not continuous. The "significant relationship" Claude described was never tested — Claude inferred it from the pattern of numbers in the table. The analysis sounds authoritative. None of it was verified.

This is not a failure of ambition. It is a failure of the work order. The prompt asked Claude to interpret data without defining the variables, specifying what analysis had actually been run, or requiring Claude to flag what it could not know. Claude did what poorly specified prompts always do: it filled the gap with plausible-sounding prose.

This chapter is about building prompts that get real analytic support — variable review, analysis planning, assumption checking, code explanation, and results interpretation — while keeping statistical judgment in human hands.

---

## What This Chapter Lets You Do

After this chapter you will be able to:

- Write prompts that produce a usable data dictionary review rather than a generic description
- Ask Claude to help select an analysis approach given your specific data and question
- Prompt Claude to check statistical assumptions before you commit to a test
- Get plain-language explanations of code and output without trusting Claude's arithmetic
- Draft limitations sections that are honest about what the data cannot support

The through-line is GIGO — garbage in, garbage out — restated for prompting: even a well-written prompt cannot rescue a bad variable definition, an invalid measure, or an unverified calculation. Better prompts make the human's job of checking easier, not unnecessary.

---

## Core Concepts

**Data dictionary.** A document that defines each variable: its name, what it measures, how it was collected, its units, and its known limitations. Claude can help draft or review this, but you must supply the definitions. Claude cannot know what "Q3_score" means in your dataset.

**Analysis plan.** A statement written before running the analysis that names the question, the variables, the proposed test, and the assumptions the test requires. Writing this before touching the data is standard practice (Gelman et al., statistical modeling guidance). Prompting Claude to review your plan — rather than generate one from scratch — keeps judgment in the right place.

**Assumption checking.** Every statistical test rests on assumptions: normality, independence, equal variance, sufficient sample size, and so on. The ASA guidance on statistical practice is explicit that assumption checking is not optional. A prompt that asks Claude to list the assumptions for a proposed test and then asks "which of these have I verified?" does more useful work than one that asks for a result.

**Code explanation versus calculation verification.** Claude can read code and explain what it is doing. That is useful. Claude cannot reliably verify that your code produced the correct number. The number must be checked against the source data, a second calculation, or a reference implementation. Research on LLM reliability in quantitative reasoning finds that models produce plausible-looking arithmetic errors with high confidence (research on hallucination in code/data tasks). Trust the explanation; verify the output.

**Limitations paragraph.** Every analysis has limits: what the sample cannot generalize to, what the design cannot establish causally, what the measure cannot capture. Claude can draft this paragraph from your stated constraints. But you must state the constraints — Claude cannot know your sampling frame from the numbers alone.

---

## Before / After Prompt Walkthrough

### The Before Prompt (what most people send)

> Here is my data summary. What does it show and what analysis should I run?

This prompt fails in three ways. First, it asks Claude to interpret variables it has not been given definitions for. Second, it asks for an analysis recommendation without specifying the research question, which means Claude will invent one. Third, it does not ask Claude to flag what it cannot know.

### The After Prompt (a work order)

> I am analyzing survey data from 240 adult participants collected in March 2026. Below is a summary table of my three main variables.
>
> **Variable definitions:**
> - `age_group`: categorical, four bands (18–29, 30–44, 45–59, 60+), self-reported
> - `wellness_score`: continuous, 0–100, sum of 12 Likert items from the validated WHO-5 scale
> - `program_participation`: binary (1 = attended at least one session, 0 = none)
>
> **My research question:** Do participants in the program have higher wellness scores than non-participants, controlling for age group?
>
> **Task:** Review my variable definitions for any obvious problems before I proceed. Then list the assumptions required by the analysis I have described, and tell me which ones I will need to verify in my data before running the test. Do not recommend a specific test yet — I want to check the assumptions first.
>
> **What you cannot know:** You do not have access to the raw data, only this summary. Flag anything you cannot assess without seeing the data.

What changed:

1. Variable definitions are explicit — Claude now knows what each variable is and is not.
2. The research question is stated — no need for Claude to invent one.
3. The task is sequenced — assumption review before test recommendation, not the reverse.
4. The constraint is named — Claude is told what it cannot know and required to flag it.

The output from this prompt is a list of assumption checks the analyst can actually run. The output from the first prompt is a paragraph that sounds like analysis but is not.

---

## The Human Gate: What You Must Verify

Claude's analysis support is most reliable when used as a planning and explanation tool rather than a calculation engine. The human gate for this chapter is explicit.

**Verify every number independently.** If Claude tells you the mean of a column is 47.3, check it. If Claude says the p-value is 0.04, verify it in your statistical software or script. LLMs produce plausible numbers; plausible is not the same as correct (research on hallucination in code/data tasks).

**Verify causal language.** Claude will often use causal framing — "leads to," "causes," "the effect of" — when describing correlational patterns. This is a known risk. Cross-sectional survey data does not establish causality. Before any paragraph Claude writes leaves your report, check that causal language is not smuggled in where correlational language belongs. [verify — current as of writing]

**Verify assumption checks against your data.** Claude can tell you that a t-test assumes approximately normal distributions in each group. Only your data can confirm or disconfirm normality. Claude's statement of the assumption is useful. The check itself belongs to you.

**Verify what "significant" means in context.** Claude may use "statistically significant" to mean p < 0.05 without noting that statistical significance says nothing about practical importance. The ASA guidance on statistical practice is clear that significance is not sufficiency.

---

## Common Mistakes

**Asking Claude to interpret data it has not been given.** If you paste a table without variable definitions, Claude will guess. Sometimes it guesses right. It will not tell you it guessed.

**Using Claude's output as proof the analysis is correct.** Claude explaining your regression output in plain English is not validation of the regression. The explanation helps you understand the output. Validation requires checking the underlying code, data, and assumptions.

**Asking "what does this mean?" instead of "what did I measure?"** Interpretation questions invite confabulation. Definitional questions force precision. Start with "what is this variable?" before "what does it show?"

**Treating a limitations paragraph as boilerplate.** Claude can produce a limitations paragraph that sounds thorough but omits the specific limitations of your study. The paragraph must name your sampling frame, your specific measurement limits, and your specific design constraints — not a generic list of things studies sometimes cannot do.

**Skipping the analysis plan.** Asking Claude for analysis recommendations before writing down your own research question inverts the work. The question defines the analysis, not the reverse. Write the question first; then ask Claude to help select an approach.

---

## Try This

**Exercise 1 — Variable review prompt.** Take a dataset you are working with (or a public dataset). Write a prompt that defines at least three variables as precisely as you can (name, measurement type, scale, collection method, known limits). Ask Claude to identify any potential problems with the variable definitions before any analysis is run. Compare what Claude flags against your own list of known issues. Note any gaps in either direction.

**Exercise 2 — Assumption checklist prompt.** Write out your research question and proposed analysis in one paragraph. Then write this prompt: "List every assumption my proposed analysis requires. For each assumption, tell me what I would need to check in my data to confirm it is met, and what the consequence would be if it is violated." Run the prompt. Then work through the checklist against your actual data.

**Exercise 3 — Limitations audit.** After your analysis is complete, prompt Claude: "Here are the constraints on my study: [sampling frame], [measurement method], [design type], [any known data quality issues]. Draft a limitations section that addresses each of these specifically. Do not include generic limitations that do not apply to this study. Flag any limitation you cannot write without knowing more about the study." Then review each sentence against the actual constraints.

---

## What Would Change My Mind

This chapter assumes Claude's quantitative reliability is limited enough to require independent verification of all calculations. If systematic evidence emerges that Claude performs arithmetic at human-expert reliability for a defined class of problems — not just plausibly, but verifiably — that would justify loosening the verification requirement for those specific cases. The bar should be an external benchmark study with error rates, not Anthropic's documentation of a new feature. [verify — current as of writing]

The chapter also assumes that prompts for analysis support should lead with variable definitions before questions. If research on structured prompting for data tasks showed that free-form questions consistently yielded better-specified outputs than definition-first prompts, the sequence advice would change.

---

## Still Puzzling

When is Claude's analysis support accurate enough that the verification overhead exceeds the risk? For large routine calculations with clear correct answers, some version of spot-checking may be more appropriate than full re-verification. The right threshold is not obvious, and it probably varies by task type, stakes, and analyst expertise.

There is also an open question about spreadsheet-specific prompts. Most working professionals do their data work in Excel or Google Sheets, not in Python or R. The assumption-checking and interpretation prompts in this chapter transfer, but the code-explanation patterns are less directly useful to someone whose "code" is a set of cell formulas. A future revision should include spreadsheet-specific examples.

---

## Bridge

Analysis support prompts earn their value at the planning stage — before the test is run, when a misspecified question can still be fixed. The next chapter applies the same specification discipline to a different kind of cognitive labor: learning. When the goal is understanding, not just output, the prompt must be designed so the human does the thinking. The stakes of skipping that design are different from analysis errors, but the mechanism of the failure is the same.

---

## Sources Used

- Anthropic Claude documentation. https://docs.anthropic.com/
- ASA guidance on statistical practice
- Tukey, exploratory data analysis
- Gelman et al., statistical modeling guidance
- NIST AI Risk Management Framework
- Reproducible research guidance
- Data documentation and data dictionary best practices
- Research on LLM reliability in quantitative reasoning
- Research on hallucination in code/data tasks
- Visualization and statistical communication guidance
