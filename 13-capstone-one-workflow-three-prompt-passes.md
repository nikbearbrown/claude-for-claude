# Chapter 13 — Capstone: One Workflow, Three Prompt Passes

## One Workflow

You have a recurring task. You have to summarize research literature for a working group that meets monthly. Every time, you open a blank conversation, paste some abstracts, and ask Claude to help. Every time, the output is slightly different. Sometimes it is too long. Sometimes it buries the finding you most needed. Sometimes it includes a claim the source does not actually support. You review it carefully, fix what is wrong, and wonder whether there is a better way to run this.

There is. And you have been building it since Chapter 1.

This chapter runs one real workflow — a research brief — through three complete prompt passes. Pass One produces a first output and exposes the failures. Pass Two audits the output against stated criteria. Pass Three produces a revised specification. At the end, the specification becomes a prompt card ready for your library.

The workflow is not hypothetical. Every prompt block in this chapter is complete enough to run. The annotation shows the reasoning behind each decision. By the end, you will have seen the full method — specification, output, audit, revision, documentation — applied end to end.

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to:

- Apply the complete specification method to a real recurring task
- Run a structured output audit against defined criteria before revising
- Distinguish between failures of specification and failures of the model
- Produce a finalized prompt card that captures the workflow for reuse
- Recognize where human judgment remains irreplaceable in the loop

---

## The Workflow: Research Brief for a Working Group

**Task:** Given a set of abstracts or short excerpts from research papers, produce a one-page research brief: key findings, points of consensus, open disputes, and implications for practice.

**Audience:** Working professionals who are domain-expert but not academic. They want to know what the research says, not how the methodology worked.

**Recurring context:** This brief is produced monthly. Different papers each time. Same format and audience.

This is the kind of task where vague prompting produces results that vary from "good enough" to "needs complete rewriting." The goal of the three passes is to produce a specification stable enough that the output is predictable regardless of which papers are supplied.

---

## Pass One: First Specification

### The Before Version

Most people start here.

```
Here are some research abstracts. Can you summarize the main findings and tell me what they mean for practitioners?

[paste abstracts]
```

This produces output. Sometimes the output is useful. But there is no defined format, no constraint on length, no instruction about how to handle disagreement between sources, no prohibition on invention, and no evaluation criteria. The reviewer has to supply all of that judgment every time.

### The Pass One Specification

Here is a structured first attempt. It applies the anatomy from Chapter 1 — task, context, constraints, output format, evaluation criteria — to this workflow.

```
TASK: Produce a one-page research brief from the abstracts below.

AUDIENCE: Working professionals with domain expertise but no academic background.
They want to know what the evidence says and what it means for their practice.
They do not need methodology details.

OUTPUT FORMAT:
1. Key findings (3–5 bullet points, one finding per bullet, max 25 words each)
2. Points of consensus (1 short paragraph, max 100 words)
3. Open disputes or gaps in evidence (1 short paragraph, max 100 words)
4. Implications for practice (3–4 bullet points, one implication per bullet)
Total length: approximately 400 words.

CONSTRAINTS:
- Every key finding must be traceable to at least one of the abstracts provided
- Do not state a finding as definitive if the abstract uses hedged language ("suggests," "may," "preliminary")
- Do not infer implications beyond what the evidence supports
- If sources disagree, name the disagreement rather than synthesizing it away
- Do not add citations or references beyond what the abstracts name

EVALUATION CRITERIA:
The brief is acceptable when:
- Each key finding cites or clearly references the source abstract
- The hedging level of the output matches the hedging level of the source
- Implications are explicitly grounded in findings, not added from general domain knowledge
- Total word count falls within 10% of 400 words

[Paste abstracts below this line]
---
[ABSTRACTS]
```

This is a complete first specification. Run it on your actual material. Save the output exactly as produced.

---

## Pass Two: Output Audit

Do not revise the prompt yet. Audit the output first.

The audit has two parts: checking against your stated evaluation criteria, and noting failures that the criteria did not anticipate.

### Audit Checklist

Run through each criterion stated in Pass One:

| Criterion | Met? | Notes |
|-----------|------|-------|
| Each key finding traceable to an abstract | Check each bullet | Flag any finding that cannot be traced |
| Hedging level matches source hedging | Read carefully | Flag any finding stated more confidently than its source |
| Implications grounded in findings | Check each implication | Flag any implication that imports external knowledge |
| Disagreements named, not synthesized | Read consensus section | Flag any place where disagreement was buried |
| Word count within 10% of 400 | Count | Flag if significantly over or under |

### A Realistic Audit Result

When you run a specification like Pass One on real research material, a typical audit reveals two or three issues. Here is what frequently appears:

**Failure 1: Confident restatement of hedged findings.** A source abstract that reads "this intervention may reduce processing time" appears in the brief as "this intervention reduces processing time." The hedge was dropped. This is a constraint violation that the Pass One specification named but did not prevent. The constraint exists; the output ignored it. This points to a specification revision: the constraint needs to be more explicit, or an example needs to demonstrate what compliant hedging looks like.

**Failure 2: Implications exceed the evidence.** The brief includes an implication that is not directly derivable from any stated finding — it is reasonable professional knowledge, but it is not in the abstracts. The evaluation criterion says implications must be grounded in findings, but the prompt did not require Claude to name which finding each implication comes from.

**Failure 3: Consensus overstated.** When two of three abstracts agree and one dissents, the consensus paragraph minimized the dissent. The constraint says to name disagreements, but the output treated "two of three agree" as sufficient consensus.

### Distinguishing Specification Failures from Model Failures

Before revising, categorize each failure:

- **Specification failure:** The constraint was stated, but not operationalized well enough. The model was not told how to demonstrate compliance.
- **Model failure:** The constraint was clear and the model ignored it. [verify — current as of writing, as model behavior on constraint adherence varies]

Most failures in a structured prompt are specification failures. The constraint existed but was written at too high a level of abstraction. "Match the hedging level" is a principle; "when the source uses 'may,' use 'may'" is a rule.

---

## Pass Three: Revised Specification

The revised specification addresses each failure identified in the audit without padding the prompt with redundant instruction.

```
TASK: Produce a one-page research brief from the abstracts below.

AUDIENCE: Working professionals with domain expertise but no academic background.
They need the findings and their practice implications. They do not need methodology.

OUTPUT FORMAT:
1. Key findings (3–5 bullet points; each bullet: [Finding]. [Source reference].)
2. Points of consensus (1 paragraph, max 100 words; if sources disagree, name the dissent
   explicitly — do not bury it in a majority summary)
3. Open disputes or gaps (1 paragraph, max 100 words)
4. Implications for practice (3–4 bullet points; each implication must name the finding
   it is derived from)
Total: approximately 400 words.

CONSTRAINTS:
- Every key finding bullet must include a parenthetical source reference
  (e.g., "Abstract 2" or the author name if given)
- Preserve the exact hedging language of the source:
  - If the source says "suggests," write "suggests" — not "shows" or "demonstrates"
  - If the source says "preliminary evidence," carry that phrase forward
- If sources disagree, state the disagreement directly in the consensus section:
  "Abstract 1 found X; Abstract 3 found Y; the cause of this difference is not addressed
  in the material provided."
- Each implication bullet must begin with: "Because [finding], practitioners should
  consider [implication]."
- Do not add implications from outside the provided abstracts, even if they are
  professionally reasonable

EVALUATION CRITERIA:
Before responding, confirm each of the following:
- All key finding bullets include a source reference
- No finding uses stronger certainty language than its source
- If any sources disagree, the disagreement is named in the consensus section
- Each implication begins with the required phrase

[Paste abstracts below this line]
---
[ABSTRACTS]
```

### What Changed and Why

**Source references on each finding:** The Pass One constraint said findings should be traceable. The Pass Three constraint operationalizes that by requiring a parenthetical reference on each bullet. Traceability is no longer an implicit expectation — it is a visible output requirement.

**Hedging rules with examples:** "Match the hedging level" is replaced by "If the source says 'suggests,' write 'suggests.'" The rule is concrete enough to verify.

**Disagreement protocol with template language:** The constraint now includes the exact structure for disagreement: "Abstract 1 found X; Abstract 3 found Y." This prevents the model from making a judgment call about how to weigh the disagreement.

**Implication phrase requirement:** "Because [finding], practitioners should consider [implication]" forces attribution. An implication that cannot be written in this form because there is no matching finding is an implication that should not be there.

**Self-check before responding:** The evaluation criteria are presented as a confirmation step before output. This is a prompting technique borrowed from chain-of-thought approaches — requiring the model to check its own work before producing the final output. [verify — current as of writing, as model behavior on self-check prompts varies]

---

## The Finished Prompt Card

The Pass Three specification, documented for the library.

---

**Card ID:** RESEARCH-01  
**Purpose:** Produce a one-page research brief from a set of abstracts, for a practitioner audience. Preserves source hedging, names disagreements, grounds implications in evidence.  
**Last tested:** [Insert date]

**Inputs:**
- 2–6 research abstracts (plain text, each clearly separated)
- No additional context required; audience and format are specified in the prompt

**Constraints:**
- Source references required on every key finding
- Hedging language must match source hedging verbatim
- Disagreements must be named explicitly, not synthesized
- Each implication must cite the finding it derives from

**Output format:**
- Key findings: 3–5 bullets with source references
- Consensus/disputes: two paragraphs, max 100 words each
- Implications: 3–4 bullets in "Because [finding], practitioners should consider [implication]" format
- Total approximately 400 words

**Review criteria:**
- Every finding is traceable to a specific abstract
- No finding is more certain than its source
- Disagreements are named in the consensus section
- Every implication is grounded in a stated finding

**Example use:** [Attach Pass Three output from the first real use of this card]

**Known failure modes:**
- Struggles when abstracts use inconsistent terminology for the same construct; may conflate or split concepts that should be unified
- Self-check step is sometimes truncated; reviewer should verify source references manually

**Revision history:**
- Pass 1: Initial specification; evaluation criteria too abstract
- Pass 2: Audit identified hedging drop, ungrounded implications, overstated consensus
- Pass 3: Added source references per finding, hedging rules with examples, disagreement template, implication phrase requirement

---

## The Human Gate

The finished card is not autonomous. The reviewer still has to:

- Decide which abstracts to include (a judgment about relevance)
- Verify that source references in the output correspond to real abstracts (the model can hallucinate)
- Decide whether a named disagreement is worth flagging for the working group
- Determine whether the implications are actionable given the working group's specific situation

The prompt card specifies the work. It does not do the judgment. That boundary — between execution and judgment — is the organizing principle of every chapter in this book.

A prompt that eliminates the need for human review is not a better prompt. It is a prompt that has hidden the review step, which is a different and more dangerous thing.

---

## Common Mistakes in the Capstone

**Revising before auditing.** The temptation after a mediocre first output is to immediately try a different version of the prompt. The audit step is not optional — it is the step that tells you whether you have a specification problem or a model problem. Skipping it means revising blindly.

**Writing constraints so general they cannot be verified.** "Be accurate" is not a constraint. "Do not state a finding as definitive if the source uses hedged language" is a constraint. Constraints work when a reviewer can point to a sentence in the output and say: this violates Rule X.

**Using the capstone workflow on a one-time task.** The three-pass method has real overhead. It is worth that overhead when the task recurs. If you only need a research brief once, a good first prompt and careful review is sufficient. Invest in the card when you need it more than once.

**Treating Pass Three as final.** The revised specification is better than Pass One. It is not perfect. The known failure modes section of the card is your acknowledgment of that. The next time you use the card and find a new failure, the card gets updated.

---

## Try This

### Exercise 1: Run the Three Passes (hands-on)

Choose a recurring task you actually do — a meeting summary, a literature review, a status report, a feedback letter. Write your first specification using the Chapter 1 anatomy. Run it. Audit the output against your stated criteria. Write the revised specification. Save the card.

### Exercise 2: Audit a Card You Did Not Write

Take someone else's prompt card — the meeting summary card from Chapter 12, for example — and run it on a difficult input: notes that are ambiguous, participants who share a name, a meeting with no clear decisions. Audit the output. Update the known failure modes field.

### Exercise 3: The Criteria Before Output Test

Before running any prompt this week, write your evaluation criteria first. Write what acceptable output looks like before Claude produces anything. Then run the prompt and check the output against the criteria you wrote. Notice how often the criteria reveal a specification gap before you even see the output.

---

## What Would Change My Mind

The three-pass method assumes that specification failures can be isolated and fixed through constraint revision. If there were a class of task where the failure mode was genuinely unpredictable — where no specification change produced more reliable output — then the method would have a ceiling. So far, specification failures have proven more tractable than model failures. The audit step is what lets you tell which you are dealing with.

The self-check step in Pass Three — asking Claude to confirm criteria before responding — may become unnecessary if future model versions show more consistent constraint adherence without it. [verify — current as of writing] For now, it costs little and catches some violations.

---

## Still Puzzling

- At what complexity threshold does a three-pass workflow require a formal test suite rather than a manual audit? Agentic tasks may require this threshold sooner than conversational ones.
- Is there a right way to share prompt cards across an organization without introducing governance overhead that makes the library unusable? No standard practice has stabilized as of this writing [verify — current as of writing].
- How do you version a prompt card when the task itself changes significantly enough that the card is no longer the same card?

---

## Figure: The AI Wayback Machine — The Prompt Card's Ancestors

The prompt card is not new. Before AI, every profession that needed repeatable quality had its version of the same tool.

The **SOP binder** (standard operating procedure) told a new employee how to run a process correctly: inputs required, steps in order, quality check at the end, who reviews and signs off. The binder was the organization's institutional memory about how good work gets done.

The **style guide** told a writer what the publication's standards were: tone, citation format, heading hierarchy, what to do when sources conflict. The guide was the editor's judgment, encoded and shared.

The **pattern library** in software design catalogued reusable solutions to recurring problems — not code to copy verbatim, but structure that could be adapted. Each pattern had a name, a context, a problem it solved, a solution, and known consequences.

The **peer review protocol** in research told the reviewer what to check and in what order, so that judgment was applied consistently rather than idiosyncratically.

The prompt card inherits from all of these. It is what happens when you recognize that AI-assisted work is repeatable work, and repeatable work deserves the same documentation discipline that good practitioners have always applied.

The difference is speed. A style guide took years to develop. A prompt card can be initialized in an afternoon. But the discipline is the same: make the specification explicit, make the review criteria visible, make the failure modes honest, and keep it current.

---

## Closing: The Work Is Yours

The first sign of trouble is usually not failure. It is fluency.

The introduction opened there, and we return to it now because fluency — the smooth, polished output that looks trustworthy — is exactly what the three-pass method is designed to interrogate. Pass One produces fluency. Pass Two asks whether the fluency is earned. Pass Three makes the specification good enough that the next time, you can tell the difference faster.

The discipline this book has built, chapter by chapter, is not about learning to prompt better in the narrow sense of finding phrasing that makes Claude respond helpfully. It is about learning to specify work clearly enough that you can supervise the result. Task, context, constraints, examples, evaluation criteria, revision loop, prompt library, three-pass workflow — each of these is a tool for the same purpose: making your judgment more effective, not less necessary.

The machines have become fluent. The question the introduction asked — what would have to be true for this to be trusted? — is still yours to answer. That has not changed.

Begin with a prompt. End with a judgment. Keep the card.

---

## Sources Used

- Anthropic prompt engineering documentation (docs.anthropic.com)
- Anthropic Claude documentation (docs.anthropic.com)
- NIST AI Risk Management Framework (NIST AI RMF)
- Human-in-the-loop AI literature
- Evaluation and rubric design literature
- Documentation and SOP (standard operating procedure) practice
- Writing and process revision research
- Software acceptance criteria practice
- Source verification guidance
- Workflow design literature

#claude #prompt-engineering #capstone #workflow #specification #revision #prompt-library #human-in-the-loop
