# Chapter 8 — Prompts for Writing and Editing

There is a version of this that goes badly. You paste your draft. You type "improve this." Claude returns a polished version — smoother sentences, tighter transitions, a confident voice. You read it. It sounds good. You submit it.

Three weeks later a reviewer asks about a claim in the third paragraph. You go back to the draft. The claim was not in your original. It is plausible — it fits the argument — but you never wrote it, and you cannot trace it to a source. Claude added it in the improvement pass. You approved it without noticing.

This chapter is about using Claude for writing and editing in a way that keeps your ownership of every claim, every fact, and every piece of evidence you stake your name on.

---

## What This Chapter Lets You Do

After this chapter you will be able to:

- Distinguish between drafting help, restructuring, line editing, and tone adaptation — and prompt for each separately
- Ask Claude to diagnose writing problems before it revises
- Constrain edits so your claims and evidence survive unchanged
- Use Claude for specific writing tasks: abstracts, introductions, methods sections, discussion calibration, reviewer responses
- Recognize when Claude has added a claim you did not provide
- Apply authorial ownership language in every editing prompt

---

## The Writing Ownership Problem

When you ask Claude to "improve" your writing without constraints, two things happen that you may not notice:

**Voice drift:** The prose gradually becomes more confident, more polished, and more generic. Claude's statistical average voice is fluent and clear. It is not your voice. If your writing has particular rhythms, choices, or stances, unconstrained editing smooths them out.

**Claim invention:** Claude makes your argument sound more complete by filling gaps. A paragraph that was genuinely uncertain becomes more assertive. A claim that was hedged loses its hedge. A connection you implied is stated explicitly — in a form you did not choose and may not be able to defend.

Neither of these is Claude being deceptive. They are the natural outputs of an "improve this" instruction given to a fluent generator. The instruction does not say "preserve my voice" or "add no new claims." So the model does what the instruction permits.

The fix is to say what you want preserved as clearly as you say what you want changed.

---

## The Writing Task Taxonomy

Before you write any editing prompt, name which kind of task you are requesting. Each task type has different risks and requires different constraints.

| Task Type | What Claude Changes | What Must Be Protected |
|---|---|---|
| Diagnose | Nothing — assesses and lists | — |
| Restructure | Order, organization, headings | Claims, evidence, all content |
| Line edit | Sentence clarity, word choice | All claims, facts, hedges |
| Tone adaptation | Register, formality, audience fit | All content |
| Draft from outline | Creates new text from your structure | Factual accuracy (you supply facts) |
| Reviewer response | Tone, organization of response | Your scientific/scholarly position |

Mixing task types in a single prompt makes the constraints ambiguous. "Clean up and reorganize" is two tasks. Separate them.

---

## Before / After: A Writing Prompt Walkthrough

### The Task

You have drafted an abstract for a research report. It is 280 words and too long for the 200-word limit. The structure is right but some sentences are redundant. You want it cut to 200 words without losing any of the findings or hedges.

### BEFORE — Open Editing Prompt

```
Rewrite this abstract to make it better and shorter. [paste abstract]
```

What this produces: a shorter abstract. Claude will cut what sounds redundant to it, tighten prose, and may consolidate findings in ways that lose precision. You cannot tell from the output what was cut, whether hedges survived, or whether a finding was softened to make the word count work.

### AFTER — Constrained Editing Prompt

```
I need to cut this abstract from 280 words to 200 words.

Constraints:
- Do not add any claims or findings not present in my draft
- Do not remove or soften any hedge language (e.g., "may suggest,"
  "preliminary evidence," "was not significant")
- Do not change the four-part structure: background / purpose /
  findings / implications
- Keep all reported statistics exactly as written

Strategy: Find redundant phrases and over-long transitions to cut first.
If you must cut content rather than language, identify which content
and ask me before cutting.

After the revision, give me:
1. Word count of your version
2. A list of what you cut (language, not content) and why
3. Any content cuts you identified but did not make (flag for my decision)

[paste abstract]
```

What this produces: a tighter abstract that you can inspect. The change log shows you what moved. The flagged content cuts give you decision points rather than fait accompli. You can compare your draft to the revision and check every hedge, every statistic, every finding.

This prompt takes ninety seconds to write and saves the kind of editorial damage that takes three weeks to discover.

---

## The Diagnose-First Pattern

Before asking Claude to change your writing, ask it to diagnose. This separates the assessment from the revision, which gives you two advantages: you can evaluate whether Claude's diagnosis matches your own judgment, and you can choose which diagnosed problems to fix and which to leave.

**Diagnose-first prompt:**

```
Read this draft and diagnose it against these three criteria:
1. Clarity: Can a reader who is unfamiliar with this project understand
   the main argument from the introduction alone?
2. Hedge accuracy: Do the hedges in the findings section match the
   strength of the evidence described in the methods section?
3. Structure: Does the order of sections follow the logic of the argument?

List specific problems you find. Do not revise yet. I will decide
what to address after reading your diagnosis.

[paste draft]
```

You review the diagnosis. You agree with some items, disagree with others, and use the list as a structured starting point for your own editing pass — or as the basis for a targeted revision prompt.

This pattern prevents the situation where you accept Claude's fixes for problems it identified but you would not have chosen to fix.

---

## Task-Specific Prompts

### Abstract Critique

```
Critique this abstract as if you were a journal reviewer in [field].
Identify:
- Any gap between what the title promises and what the abstract delivers
- Any finding stated with more confidence than the methods justify
- Any missing element expected in a [journal name] abstract
  (e.g., context / objective / design / results / conclusions)
Do not rewrite. List the gaps so I can revise.
```

### Introduction Restructuring

```
My introduction has three sections: [describe them]. The argument
is not landing in order — readers need [X] before they can understand [Y].

Restructure the three sections into a new order that serves the reader.
Do not change the content of any section. Do not add or remove claims.
Tell me the new order you recommend and why, then show me the restructured
version with sections clearly labeled.
```

### Methods Clarity Edit

```
Line-edit this methods section for clarity for a reader who is trained
in the field but not in this specific technique.

Constraints:
- All technical terms must be kept exactly as written (no substitutions)
- All procedure steps must remain in original order
- All reported parameters, instruments, and sample characteristics
  must remain unchanged
- Do not add explanatory text beyond what is in the original

Mark every change you make with [EDIT] so I can review each one.
```

### Tone Adaptation

```
I have written this section for a specialist audience. I need a version
for [non-specialist audience: e.g., policy-makers, general readers, students].

Constraints:
- The same argument must be present in both versions
- Do not remove caveats or limitations — translate them, do not drop them
- Do not add new examples from outside my draft
- The adapted version should be approximately the same length

After the adapted version, note any place where adapting the tone
required a trade-off in precision, so I can review those passages.
```

### Reviewer Response Draft

```
Here is a reviewer comment on my manuscript: [paste comment]
Here is my scientific position on this comment: [state your position]

Draft a response that:
- Acknowledges the reviewer's concern respectfully
- States my position clearly without being defensive
- Describes what change (if any) I am making to the manuscript
  and where

Do not change my scientific position. Do not add concessions I have
not stated. Keep the response under 150 words.
```

---

## Authorial Ownership Language

Every editing prompt should include language that states the ownership boundary. You can standardize this as a header you paste at the top of any writing-task prompt:

```
AUTHORIAL OWNERSHIP CONSTRAINTS
- I own all claims, findings, and evidence in this draft
- Do not add claims not present in my text
- Do not remove or soften hedges
- Do not add examples, citations, or facts I have not provided
- Mark every change so I can review it
```

This is not a magic phrase that guarantees Claude will follow it. It is a specification that tells Claude what the task requires and gives you a standard against which to evaluate the output. When Claude violates a constraint, the violation is visible against the standard — and the revision prompt becomes more specific.

[verify — current as of writing] Claude generally honors explicit authorial constraints better when they are stated prominently at the top of the prompt rather than embedded in the middle. Prominence appears to increase compliance, but this is not guaranteed.

---

## AI Disclosure in Writing Contexts

Many academic journals, publishers, funding agencies, and employers now require disclosure of AI-assisted writing. Policies vary and are changing. [verify — current as of writing]

The operative principle across most current policies (as of writing): you are responsible for every claim in a document with your name on it, regardless of whether AI helped draft or edit it. Disclosure does not transfer responsibility; it is transparency about process.

If you are working in an academic, research, or professional context:

1. Check your institution's current policy on AI-assisted writing
2. Check your target publication or submission context's policy
3. Document which parts of your work involved AI assistance if disclosure is required
4. Understand that "Claude wrote it" is not a defense for an inaccurate claim — you submitted it

The prompts in this chapter are designed to keep authorial control precisely because the responsibility does not move. Use them accordingly.

(COPE 2023; see also publisher-specific author guidelines for your target venue [verify — current as of writing])

---

## Common Mistakes

**Asking for "improvements" without naming which kind.** Improvement is not a task. Restructuring, clarity editing, tone adaptation, and hedge-checking are tasks. Each has different risks.

**Not asking for a change log on line edits.** A line editor who does not show you the redline is asking you to trust every change. Do not trust every change.

**Letting voice drift accumulate across passes.** The first revision sounds like a polished version of you. The third revision sounds like Claude. Run Claude's edits through your own read-aloud test — if you would not have written it, decide whether to keep it.

**Using the output without reading the full text.** The claim Claude added is always somewhere in the middle. Read the whole output, not just the section you were focused on.

**Accepting hedges that weakened.** "The data suggest" becoming "the data show" is a claim upgrade. "May be associated with" becoming "is associated with" is a claim upgrade. These are not stylistic choices — they change the epistemic status of your finding. Check every hedge.

**Submitting reviewer responses without checking your stated position.** Claude's draft will be diplomatically worded and may, in the interest of diplomacy, soften your position on a scientific disagreement. Read the response against your stated position, not against how good the prose sounds.

---

## The Editing Prompt Checklist

Before you send a writing or editing prompt, verify:

| Check | Why |
|---|---|
| Named which task type (diagnose / restructure / line edit / tone / draft) | Mixed tasks create mixed constraints |
| Stated preservation constraints explicitly | Unconstrained edits drift |
| Requested a change log or markup | Review is only possible if changes are visible |
| Stated authorial ownership | Sets the standard for evaluating the output |
| Identified decision points for human review | Some calls belong to you, not to Claude |

---

## Try This

**Exercise 1 — Diagnose before you revise.**
Take a piece of your own writing — a memo, report, email, or draft section — and write a diagnose-first prompt against three specific criteria you care about (clarity for your audience, hedge accuracy, logical structure). Review Claude's diagnosis before asking it to revise anything. Did it find problems you would have found? Did it find problems you would not have? Did it miss problems you can already see? This calibration exercise tells you where Claude's editorial judgment aligns with yours and where it diverges.

**Exercise 2 — Constrained abstract cut.**
Find an abstract or executive summary you have written that is over its target word count. Write the constrained editing prompt from this chapter (with your actual constraints for your context). After you receive the revision, check every hedge and every statistic. Count: how many hedges survived? How many statistics survived unchanged? If any changed, was it flagged?

**Exercise 3 — Voice test.**
Take a paragraph you wrote that you consider representative of your voice. Ask Claude to line-edit it. Read the result aloud. Then read your original aloud. What changed? Which version would you rather have submitted? Now write a revision prompt that asks Claude to restore the elements of the original voice that were lost, without undoing the clarity improvements. This exercise maps where your voice lives and how to protect it.

---

## What Would Change My Mind

This chapter argues for constrained, task-specific prompts over open editing instructions. The main counter-argument is efficiency: a sophisticated user who reads carefully may be able to catch claim invention and voice drift through review, making the overhead of detailed constraints unnecessary for experienced reviewers. If readers with strong editorial judgment consistently get good results from lighter prompts — without the claim-addition failures this chapter warns against — then the constraint overhead is higher than it needs to be. That is an empirical question about how carefully readers actually review Claude's output, and the answer is probably: less carefully than they think they do.

---

## Still Puzzling

The boundary between "line editing" and "rewriting" is clear in principle and murky in practice. A line editor who changes every sentence in a paragraph has arguably restructured the argument, even if no sentences were added or removed. When does improving clarity cross into changing meaning? This is not a Claude-specific problem — it is the fundamental tension of any editing relationship. The constraint-based prompts in this chapter push the problem to the surface (by asking for change logs and flagging edits) rather than solving it. The author still has to exercise judgment on where the line falls for their specific document and stakes.

---

## Bridge

You have now worked through the full foundations arc and the first two chapters of applied prompting: research work (verifying leads before they become evidence) and writing work (keeping claims and voice under your control while Claude handles execution). Chapter 9 takes the same discipline into data and analysis support, where the risks shift again — from fabricated citations and voice drift to statistical judgment that the model should not be making on your behalf.

---

## Sources Used

- Anthropic Claude documentation. https://docs.anthropic.com/
- Committee on Publication Ethics (COPE). (2023). Authorship and AI tools. https://publicationethics.org/
- NIST AI Risk Management Framework (NIST AI RMF). https://www.nist.gov/system/files/documents/2023/01/26/AI%20RMF%201.0.pdf
- Writing process and revision research (general field practice)
- Technical communication style guidance (general field practice; see, e.g., Williams and Bizup, *Style: Lessons in Clarity and Grace*)
- Plain language guidance (plainlanguage.gov)
- Research on automation bias and overreliance (Parasuraman and Manzey 2010)
- Peer-review response best practices (general field practice)
