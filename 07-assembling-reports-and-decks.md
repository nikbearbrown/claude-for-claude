# Chapter 7 — Assembling Reports and Decks

## TL;DR

- Cowork can assemble a polished report or deck from your source folder. Polished is not the same as trustworthy.
- Every major claim in the artifact must trace back to a source you approved.
- Provide a template, voice constraints, and a list of what must not be claimed — not just what should be included.
- Review figures, captions, and speaker notes separately. They fail differently than body text.
- You own the final artifact. You are responsible for what it says.

---

## The Scene

The grant progress report is due Friday. You have six months of meeting notes, a budget summary, three milestone documents, email threads about a delayed deliverable, and a draft from last year to use as a style reference.

You spend the morning writing the task brief, organizing the approved source folder, and uploading the template. Cowork produces a twelve-page draft in under ten minutes. It has section headers, a summary table, properly formatted citations, and an executive summary that sounds professional.

You read the executive summary. It says the project "exceeded initial projections in participant engagement." You pause. That number appears in a slide someone shared in a meeting in March — but when you check the meeting notes, the slide was a projection, not an outcome. Cowork found a number, found a metric name, and assembled a claim that sounds like a result. It is not.

You find two other problems in twenty minutes of careful reading: one figure caption that describes a trend the chart does not actually show, and a sentence in the risk section that understates a delay your funder already knows about. The delay is in your meeting notes. Cowork softened it.

The draft is useful. Three problems in twelve pages of professional-sounding prose would have been invisible to a tired reader on a Friday afternoon. They are visible to you because you know the project and you read with the question: does this match the evidence?

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to:

- Write a report/deck task brief that gives Cowork structure, source constraints, and explicit rules about claims.
- Review an assembled artifact for source traceability, omission, overclaiming, and audience fit.
- Check figures, captions, and speaker notes for specific failure modes.
- Decide when compression creates unacceptable omission risk.

---

## What Cowork Is Actually Doing

When you give Cowork a folder of source materials and ask for a report, it is doing several things at once: reading the sources, identifying structure and sections, synthesizing content, applying style, and producing an artifact. Each step introduces a place where the output can diverge from what the evidence actually supports.

**Synthesis is not neutral.** When Cowork combines information across multiple documents, it makes decisions about what is relevant, what is consistent, and how to connect ideas. Those decisions may be reasonable. They may also flatten disagreement, select the most recent or most emphatic source, drop information that complicates the narrative, or import an implication that no single source stated.

**Compression increases omission risk.** An executive summary is shorter than the source materials by design. What gets cut is not random — it tends to be caveats, uncertainty, context, and minority findings. Compression makes confident, clean prose. The human reader then makes decisions based on prose that was always already incomplete.

**Polish confers authority.** A professionally formatted document with a table of contents, section headers, and proper citations looks authoritative. This is true regardless of whether the claims are well-supported. Research on AI-assisted writing confirms that human responsibility for accuracy, integrity, and authorship does not transfer to the tool (ICMJE, "Use of AI by Authors"). The polished artifact is yours. Its errors are yours.

---

## The Report/Deck Brief

The task brief for an extraction task defines a schema. The task brief for a report or deck defines the argument boundaries.

```
Task: Draft grant progress report
Audience: Program officer, funder, mid-level familiarity with project
Purpose: 12-month progress report; formal funding document
Source materials: approved-sources/ (contains: meeting-notes/, milestones/, budget-summary.pdf, prior-report-2025.pdf)
Do not use: any file outside this folder; any projected numbers as outcomes; any information not in these sources

Required sections:
- Executive summary (max 1 page)
- Milestone status (table)
- Budget summary (tie to budget-summary.pdf only)
- Deliverable delays and current status
- Next 6 months

Template: prior-report-2025.pdf (match section structure and tone)
Voice: formal, evidence-grounded, no marketing language
Word count target: 2,500–3,000 words

Claims that must be included:
- The month-four delay in deliverable 3B and its current resolution status

Claims that must NOT be made:
- Do not claim any outcome not documented as completed in the milestone files
- Do not use projected figures as reported results
- Do not soften or omit the deliverable 3B delay

Citation rule: every statistic, metric, and milestone claim must include a parenthetical source note (document name, date if available)

Figure/table rule: any table in the output must match source data exactly — do not reinterpret figures from the source documents

Review checkpoints:
- Show me the proposed section outline before drafting
- Flag any source document you could not read clearly
- Flag any requested section where the source materials do not provide enough information to support the claim
```

The "claims that must NOT be made" field is not boilerplate. It is where you put your knowledge of the project that Cowork cannot have: what is politically sensitive, what the funder already knows, what would be misleading in context. Cowork cannot know that the delay was contentious. You can tell it that the delay must appear accurately.

---

## A Worked Walkthrough

**The task:** Produce a draft progress report and a short accompanying deck (five to eight slides) from an approved source folder.

**Step 1: Organize the source folder.**

Only approved materials belong in the source folder. If a document is in the folder, Cowork may use it. Do not include drafts, personal notes, sensitive financial details not intended for the funder, or documents from other projects. What goes in the folder is a content decision, not just a file-management decision (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

**Step 2: Write the brief.**

Use the template above. Spend extra time on three fields: "Claims that must NOT be made," "Citation rule," and "Review checkpoints." These fields are where you protect the artifact against the most common failures.

**Step 3: Review the plan.**

Cowork should show you the proposed structure before writing. This is where you catch wrong assumptions: a section it plans to leave thin because the sources are sparse, a section it plans to expand because it found many notes. Redirect before it drafts, not after.

**Step 4: Read the artifact — claim by claim.**

Do not read for flow. Read for claim. Every factual statement, every number, every metric, every milestone — stop and ask: which source document does this come from? Is that the right interpretation of that source?

This is slow. It is the work. A report that your funder acts on based on incorrect claims is worse than a report you have to revise. The cost of careful reading is an hour. The cost of a wrong claim in a funding document can be much larger.

**Step 5: Check figures and captions separately.**

Figures fail differently than text. A caption can describe a trend the chart does not show. A chart can use data from the wrong source. A legend can be mislabeled. Research on chart communication confirms that visual misleading is common even without intent — truncated axes, missing baselines, and selective framing all change the message a reader takes from a figure ("Misleading Beyond Visual Tricks," CHI 2023). For each figure in the output: does the caption describe what the figure actually shows, nothing more? Does the figure use the data source it claims?

**Step 6: Check speaker notes for privacy.**

If Cowork produces a deck, it may populate speaker notes from your source documents. Meeting notes, budget details, or internal conversations can appear in speaker notes when they were not intended for external distribution. Read the speaker notes separately and entirely. Delete or redact anything that should not travel with the deck (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

**Step 7: Check for omission.**

What the artifact does not say is harder to check than what it says. Go back to your source materials and ask: is there anything important in here that did not make it into the report? Negative findings, delays, budget issues, unresolved risks, and minority views tend to be compressed or dropped. If something is in your sources and not in the report, decide whether that omission is acceptable.

---

## The Verification Checklist

- Every major claim traces to a source document in the approved folder.
- Metrics and statistics have parenthetical source notes.
- No projected number appears as a reported result.
- Required claims are present and accurate.
- Prohibited claims are absent.
- Figures match the source data they represent.
- Captions state what the figures show — not more.
- Caveats and uncertainty survived compression.
- Speaker notes contain no unintended private material.
- The artifact matches the stated audience and purpose.
- You have read the full artifact, not only the executive summary.

The checklist is not a formality. It is the record that you reviewed the artifact before it left your hands. AI-assisted report writing is useful and increasingly common. What it does not change is that the person whose name goes on the report is responsible for its contents (Nature Machine Intelligence, "Writing the rules in AI-assisted writing," 2023).

---

## The Human Gate

Two questions before the artifact goes anywhere:

**First: Could I defend every claim in this document?** Not "does it sound right" but "can I point to the source and explain why this claim follows from it."

**Second: What would I not want to discover after this is sent?** A funder finding a discrepancy between the report and a metric they have from another source. A colleague noticing the delay was understated. A reader citing your figure in a context that reveals the caption overstated the finding. Run this mental check before sending. It is faster than the conversation you would have afterward.

---

## Common Mistakes

**Accepting the first draft as the final draft.** Cowork's first draft is a starting point with source material incorporated. It is not a reviewed document.

**Reviewing only the narrative.** Tables, figures, captions, and speaker notes have distinct failure modes. A clean narrative with a wrong table is a wrong report.

**Letting Cowork decide what to compress.** An executive summary written without explicit scope constraints will compress toward what sounds most impressive. That is not the same as what is most accurate.

**Using unsorted source folders.** If the source folder contains documents from multiple projects, multiple time periods, or unrelated contexts, Cowork may draw on all of them. The output will contain material you did not intend to include.

**Skipping the "must not claim" field.** This field is where practitioner knowledge lives. Cowork does not know your funder relationship, your political context, or what would be misleading even if technically accurate. You do. Put it in the brief.

**Over-relying on the template.** A template ensures structural consistency. It does not ensure claim accuracy. The template formats the artifact; you verify the content.

---

## Try This

**Exercise 1 — Claim tracing.** Take any report or document you have written or reviewed in the past year. Pick ten sentences at random. For each one, try to identify what source document or direct knowledge the claim came from. How many can you source? How many feel right but you can't point to evidence? Now apply the same test to a Cowork-assembled draft. What is different?

**Exercise 2 — Compression audit.** Write a task brief asking Cowork to produce an executive summary from a report you already have. Then compare the summary to the full report: what was dropped? Were the dropped items minor details, or caveats and complications that matter? What would a reader of the summary not know that a reader of the full report would?

**Exercise 3 — Brief discipline.** Before your next real report or deck task, write the full brief using the template in this chapter — including the "claims that must NOT be made" field. After you finish the task, look back at the brief and check whether each field was useful. Which fields caught a problem? Which fields could you have skipped?

---

## What Would Change My Mind

This chapter argues that human claim-review is mandatory because polished formatting obscures evidence gaps. If future systems routinely produced artifacts with inline source citations that linked every claim to a specific passage in a specific document — and that linkage was verifiable in seconds — the claim-by-claim review burden would shift from reading the whole document to auditing the citation links. That would be a meaningful improvement. The current state [verify — current as of writing] does not reliably provide this; citation notes in Cowork outputs vary in specificity and accuracy. Until they are both specific and reliable, the human must read the claims, not just the footnotes.

---

## Still Puzzling

The multimedia learning research (Mayer 2002) has clear implications for slide design: reduce extraneous content, align words and visuals, guide attention deliberately. The research on how AI-generated captions are actually integrated into human writing suggests these are often used as starting points that authors then modify, rather than as final text ("Understanding How Paper Writers Use AI-Generated Captions in Figure Caption Writing," arXiv 2025). The gap that remains open is this: at what level of compression does a deck become misleading even if every individual slide is accurate? There is no established threshold, and the answer probably depends on audience, domain, and what decisions the deck is meant to support.

---

## Bridge to Chapter 8

You have assembled the artifact. At some point you will need to find it again.

The next chapter takes on the underappreciated risk of file organization work: Cowork can help sort, rename, and restructure folders, but moving files is a state change in your working environment. Before any bulk file operation, you need a backup, a dry run, and a manifest. Chapter 8 teaches the reversible approach.

---

## AI Wayback Machine

**Edward Tufte** spent decades arguing that most data presentations obscure more than they reveal — through chartjunk, overloaded slides, truncated axes, and disconnected captions. His phrase "evidence corruption" applies to slides that prioritize visual appeal over analytical honesty. His critique of the PowerPoint culture in engineering organizations was not a design complaint; it was a claim about how presentation format shapes what decision-makers believe. A deck that looks good and misleads is worse than a messy document that tells the truth.

![Edward Tufte](../images/edward-tufte-jj2.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

**Run this:**

```
Who is Edward Tufte, and what is his central critique of how people present data? How does his concept of "evidence corruption" apply to AI-generated reports and slide decks? Keep it to three paragraphs.
```

→ Search **"Edward Tufte data visualization"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask about Tufte's analysis of the Space Shuttle Columbia disaster and how PowerPoint slides contributed to the failure to communicate engineering risk.
- Ask what Tufte recommends instead of bullet-point slides for presenting complex evidence.

What changes? What gets better? What gets worse?

---

## Sources Used

- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic, "Create and edit files with Claude," Claude Help Center. https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude [verify — current as of writing]
- Nature Machine Intelligence, "Writing the rules in AI-assisted writing," 2023. https://www.nature.com/articles/s42256-023-00678-6
- ICMJE, "Use of AI by Authors." https://www.icmje.org/recommendations/browse/artificial-intelligence/ai-use-by-authors.html
- Mayer, "Cognitive Theory of Multimedia Learning," 2002. https://www.psychology.mcmaster.ca/bennett/psy720/readings/m1/m1r3.pdf
- "PowerPoint in the Psychology Classroom: Lessons from Multimedia Learning Research," 2011. https://journals.sagepub.com/doi/pdf/10.2304/plat.2011.10.2.95
- "Misleading Beyond Visual Tricks: How People Actually Lie with Charts," CHI 2023. https://vdl.sci.utah.edu/publications/2023_chi_misleading/
- "A computational analysis of accessibility, readability, and explainability of figures in open access publications," EPJ Data Science, 2023. https://epjdatascience.springeropen.com/articles/10.1140/epjds/s13688-023-00380-y
- "Understanding How Paper Writers Use AI-Generated Captions in Figure Caption Writing," arXiv, 2025. https://arxiv.org/abs/2501.06317
