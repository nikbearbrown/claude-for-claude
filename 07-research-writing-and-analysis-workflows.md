# Chapter 7 — Research, Writing, and Analysis Workflows

## TL;DR

- Claude can help with the work around the claim. You own the claim.
- Literature support means search terms, source organization, and extraction from documents you supply — not a literature review generated from Claude's training data.
- Writing support means outlining, critique, transitions, and clarity — not authorship. Publisher and journal policies consistently hold you accountable for every claim.
- Analysis support means planning, explaining, and pressure-testing — not replacing your methodological judgment.
- Audit trail: log what you asked, what Claude produced, what you verified, and what you changed.

---

## Opening Scene

A doctoral student in education policy has a conference deadline in ten days. She types: "Write me a literature review on school choice and student outcomes."

Claude obliges. In three minutes she has 800 words of professional academic prose, five paragraphs, nine citations with author names, journals, and years. She almost pastes it in.

Then she stops and looks at the third citation — a paper she recognizes because she read it for her qualifying exam. The paper Claude cited says something different from what Claude claims it says. The paper's finding was ambiguous. Claude's version is decisive.

She checks a second citation. It does not exist.

The prose is fine. The citations are not. And the central synthesis — the paragraph that would form the argument of her review — was built on those citations.

That student's close call is this chapter's central case. Claude can generate research prose that reads exactly like scholarship. Reading like scholarship and being scholarship are different things. This chapter teaches the difference and shows where Claude's assistance ends and your ownership begins.

---

## What This Chapter Lets You Do

By the end of this chapter you can:

- Route research and writing tasks to the right Claude surface
- Design a literature search workflow that Claude can support without substituting for
- Use Claude for manuscript development while preserving your accountability for every claim
- Plan quantitative and qualitative analysis with Claude's help without surrendering methodological judgment
- Build a simple AI-use log that satisfies disclosure expectations at most venues

---

## The Central Distinction: Support Versus Claim

Research and writing consist of two categories of labor. Claude is well-suited to one and prohibited from the other.

**Support work** includes: brainstorming research questions, drafting search terms, organizing a source matrix, summarizing papers you have supplied, identifying gaps in an argument, flagging unclear transitions, generating table captions, drafting alternative phrasings, and pressure-testing an analysis plan.

**Claim ownership** includes: deciding what question matters, selecting sources, evaluating whether a study's design justifies its conclusion, interpreting data, making causal arguments, ensuring consent and ethics compliance, disclosing your methods, and submitting work under your name.

The distinction is not about how hard the task is. Generating a search term is easy but can be AI-assisted. Interpreting an ambiguous regression coefficient is hard and cannot be delegated. The question is: if the claim turns out to be wrong, who is responsible? That person is the one who must own it.

Publisher policies are consistent on this point. ICMJE states that AI tools should not be listed as authors because they cannot take responsibility for accuracy, integrity, and originality (ICMJE, Recommendations for the Conduct, Reporting, Editing, and Publication of Scholarly Work). Nature's policy specifies that LLMs do not satisfy authorship criteria and that AI-assisted content must be fact-checked by accountable human authors (Nature Portfolio, AI Policy). Hosseini, Resnik, and Holmes (2023) argue that the ethics of AI disclosure rest on the same foundation: human accountability for the scholarly claim.

---

## Research Workflow Routing

Before you open Claude, decide which surface belongs on each research task.

| Research task | Claude chat | Claude Cowork | Claude Code | Human ownership |
|---|---|---|---|---|
| Brainstorm research questions | Strong | Usually unnecessary | Not needed | Final question and scope |
| Generate search terms | Strong | Possible | Not needed | Search strategy and databases |
| Summarize provided PDFs | Strong | Strong for multiple files | Not needed | Accuracy and synthesis |
| Build a source matrix | Possible | Strong | Possible if scripted | Inclusion criteria and interpretation |
| Clean a dataset | Limited | Possible | Strong if reproducible | Data quality and decision log |
| Choose analysis method | Advisory only | Advisory only | Advisory/script support | Method justification and assumptions |
| Draft a manuscript section | Strong | Strong for multi-file context | Not needed | Claims and citations |
| Generate figures | Possible | Strong for document context | Strong for data and code | Truthfulness and caption |
| Submit manuscript | Avoid | Avoid | Avoid | Human-only action |

Claude chat handles most conceptual, drafting, and critique tasks. Claude Cowork handles multi-file document work — pulling together PDFs, generating tables across a folder, assembling a draft from multiple sources. Claude Code handles scripted, reproducible analysis. None of the surfaces handle submission, interpretation, or authorship.

---

## Literature Support: What You Can and Cannot Delegate

### What you can ask Claude to do

- Translate a research question into database search terms, synonyms, MeSH terms, or Boolean combinations
- Draft inclusion and exclusion criteria for a screening protocol
- Create a structured source matrix (columns: citation, method, sample, measure, finding, limitation, relevance)
- Summarize the argument and main finding of a PDF you supply
- Compare two papers on a specific dimension you name
- Identify gaps or contradictions across summaries you provide
- Generate interview questions based on a theoretical framework you describe

### What you must not delegate

- Generating citations from memory or training data — Claude can hallucinate plausible-looking citations that do not exist. Every citation must come from a database, library tool, DOI lookup, or source document you have verified.
- Synthesizing literature without sources in hand — a synthesis built on Claude's training data is not a literature review; it is a fluent paraphrase of what Claude learned, which may be outdated, biased, or wrong.
- Deciding which papers are methodologically sound — that requires reading the paper, not reading a summary.

Nature Machine Intelligence (2023, "The AI Writing on the Wall") warns that AI may normalize a practice where humans curate generated text rather than develop and defend scientific claims. The chapter's opening scene is that warning made concrete.

### Worked example: from vague request to auditable workflow

**Bad:** "Write me a literature review on school choice and student outcomes."

**Better:**

Step 1 — Ask Claude to generate search terms:
> "I'm studying the relationship between school choice policies and student academic outcomes. Suggest ten search terms or Boolean combinations I could use in Google Scholar, ERIC, and PsycINFO. Include synonyms for school choice and for academic outcomes."

Step 2 — Run the search yourself in real databases. Download the sources.

Step 3 — Ask Claude to extract structured notes from each PDF you supply:
> "Here is the PDF of [paper]. Summarize in this format: research question, design, sample, key measure, main finding, main limitation, relevant to my topic."

Step 4 — Ask Claude to build a comparison table from the notes:
> "Here are five structured summaries. Build a matrix comparing them on design, sample, and main finding. Flag any contradictions."

Step 5 — You write the synthesis. You decide what patterns the evidence supports. You decide what the evidence does not support. You own the argument.

Step 6 — Ask Claude to pressure-test the draft:
> "Here is a paragraph of my lit review. What claims am I making that I have not yet cited? What objections could a reviewer raise?"

---

## Writing Support: Assistance Without Authorship

### Where Claude helps

- Identifying unclear sentences, buried transitions, or missing signposts
- Generating multiple versions of a sentence so you can choose the clearest
- Drafting a section outline from a set of claims you provide
- Acting as a skeptical reader: "What objections could a reviewer raise to this argument?"
- Checking whether your conclusion actually follows from your stated evidence
- Improving accessibility of technical prose for a general audience

### Where the author must stay

- Every substantive claim and its evidence source
- The argument structure — what follows from what
- Tone and commitment — whether the paper hedges or asserts
- Any claims about what other authors said — these must come from the actual text, not from Claude's memory of the text
- Revision decisions — Claude can flag; you decide

Language revision is a particularly important case. Nature Machine Intelligence (2023, "Writing the Rules in AI-Assisted Writing") emphasizes transparency and accountability in AI-assisted scientific writing. Disclosure norms vary by venue, but the practical minimum is: if Claude materially shaped any argument, method, or interpretation, disclose it. If Claude only corrected grammar, check your venue's policy — some require disclosure even for that.

---

## Analysis Support: Planning Without Surrender

### Quantitative analysis

Claude can help you think through an analysis plan: what question the data might answer, what assumptions a particular test makes, what diagnostics would reveal a problem, what the results would mean if significant or null. That is valuable.

Claude cannot replace:

- Knowing whether your measurement instrument is valid for your population
- Judging whether your sample size and design are adequate for causal claims
- Handling missing data decisions that depend on domain knowledge
- Deciding whether causal language is warranted or only associational
- Interpreting a coefficient in context — what does a two-point change on this scale mean for a real person?

The source matrix for this chapter (arXiv papers on qualitative LLM use) flags context collapse and distorted synthesis as real risks (SSRN 2026, "Large Language Models in Qualitative Research: Governance, Validity, and the Limits of Computational Assistance"). These risks apply equally to quantitative work: Claude can generate analysis prose that sounds methodologically sophisticated while missing the key assumption your data violate.

A safe default: describe the analysis to Claude, ask Claude to identify three assumptions the method requires, then verify each assumption yourself.

### Qualitative analysis

Claude can propose initial codes, compare coded excerpts, draft analytic memos, and surface candidate themes across supplied text. These are useful starting points.

They are not findings.

Qualitative research depends on interpretation, reflexivity, theoretical fit, and proximity to participant meaning. The arXiv papers (2024) and SSRN (2026) on LLMs in qualitative research consistently warn against letting computational fluency flatten participant voices into efficient labels. A theme is not a theme because Claude labeled it — it is a theme because a researcher grounded it in the data, tested it against negative cases, and connected it to theory.

Use Claude's proposed codes as hypotheses. Then check them against the actual text. Preserve your own memo trail. Keep disagreements and alternates in the record.

---

## Peer Review and Manuscript Feedback

Claude can act as a structured critical reader during revision: identifying weak transitions, unstated assumptions, possible reviewer objections, and unclear evidence chains. That is legitimate assistance during the revision phase of your own manuscript.

Claude should not be used for actual peer review of confidential manuscripts without understanding your journal's policy and your institution's norms. Research Integrity and Peer Review (2023) identifies confidentiality, bias, accountability, and disclosure as central concerns in LLM-assisted peer review. The key constraint: a manuscript under review is usually confidential, and uploading it to a third-party AI service may violate that confidence regardless of intent.

When using Claude for revision feedback on your own manuscript: treat its concerns as leads, not verdicts. Claude identifies possible weaknesses; you judge whether they are real. Claude suggests alternatives; you decide whether they change what you mean.

---

## Disclosure and the Audit Trail

Disclosure practices are still evolving across fields, journals, and institutions. The practical minimum that will serve you in almost any context:

**Keep a simple AI-use log** for any project that produces work others will rely on:

| Date | Tool | Purpose | Input type | Output used | Human verification |
|---|---|---|---|---|---|
| 2026-05-15 | Claude chat | Search term generation | Research question (text) | 8 of 10 terms | Searched databases; confirmed relevance |
| 2026-05-18 | Claude chat | PDF summaries | 5 papers (uploaded) | 5 summaries | Read each paper; corrected 2 summaries |
| 2026-05-22 | Claude chat | Manuscript critique | Draft section (text) | 3 structural suggestions | Applied 2; rejected 1 |

This log is your evidence that you supervised the work. It also makes disclosure straightforward: you know exactly what AI did and what you verified.

Check your venue's specific policy before submitting. Journal policies on AI are changing quickly — what a journal accepted in 2024 may not reflect its 2026 requirements. [verify — current as of writing]

---

## Common Mistakes

**"Claude can do the literature review for me."** A literature review is a scholarly argument about a body of evidence. Claude can help you build the infrastructure for that argument. The argument is yours.

**"A citation in an AI output is probably real."** Citations are generated text. They can look real while pointing to nothing. Verify every citation you use from a primary database or DOI.

**"If Claude found a theme, the theme is in the data."** Claude found a label that fits the words. Whether that label is grounded in participant meaning, theoretical context, and negative-case testing is a judgment only a researcher with the full dataset and interpretive background can make.

**"Language revision does not affect meaning."** It often does. When Claude rephrases a hedged claim as a confident one, or removes a limitation clause for readability, the meaning changes. Read revision suggestions as meaning-change proposals, not style corrections.

**"AI-generated peer feedback is objective."** It is generated from patterns in text. It reflects what critique typically sounds like, not what this manuscript specifically requires. Treat it as a brainstorm, not a verdict.

**"Disclosure is only necessary if AI wrote the final text."** Many venue policies extend to brainstorming, structuring, and analysis planning. Check the policy for your specific context.

---

## Try This

**Exercise 1 — Rebuild the Literature Workflow (hands-on)**

Pick a research question you are actually working on. Ask Claude to generate ten search terms. Run those terms in at least two real databases. Download three results. Ask Claude to summarize each PDF you supply, using a structured extraction template. Compare Claude's summaries to your own reading of each paper. Note where they differ. Write one paragraph of your own synthesis from the verified notes.

**Exercise 2 — Peer Reviewer Rehearsal**

Take a section of a paper you are writing or revising. Ask Claude: "Act as a skeptical peer reviewer. What are the three strongest objections to this argument? What evidence would you demand?" For each objection Claude raises, decide: is this a real problem with my argument? If so, what do I actually do about it? Write down your decisions.

---

## What Would Change My Mind

The claim that Claude cannot handle literature review authorship would weaken if:

- Citation grounding became a real capability — if Claude retrieved actual papers and quoted them with verified page numbers, the fabrication risk would diminish substantially. Several retrieval-augmented tools are moving in this direction, but the gap between "retrieved a document" and "correctly interpreted its argument" remains human territory. [verify — current as of writing]
- Qualitative analysis with LLMs developed reproducible, validated methods with clear protocols for preserving participant meaning. The literature on this is in early stages (arXiv 2024, SSRN 2026). If that literature matures into established methods, the guidance on qualitative coding would need updating.
- Journal and publisher AI policies converged on stable, clear standards. Right now they are evolving rapidly, which makes the audit-trail habit more important than any specific disclosure rule.

---

## Still Puzzling

- How much does AI-assisted literature work change what researchers actually know versus what their papers claim they know? If a researcher never reads the papers Claude summarized, what happened to their expertise? This is a genuine epistemic question the field has not resolved.
- When does AI assistance in qualitative coding become a methodological choice that needs to be reported in the methods section rather than only in an AI disclosure statement? The norms here are unsettled.
- Can an AI-use log actually protect researchers in cases of academic integrity disputes? The log is good practice, but its evidentiary status in formal review is not yet established.

---

## Bridge to Chapter 8

Chapter 7 assumed you chose what to share with Claude. Chapter 8 asks whether you should have shared it at all. Privacy, permissions, and sensitive work set the boundary before the workflow starts — and they matter differently depending on which Claude surface you are using.

---

## Sources Used

- Nature Machine Intelligence. "Writing the rules in AI-assisted writing." 2023. https://www.nature.com/articles/s42256-023-00678-6
- Nature Reviews Physics. "Science in the age of large language models." 2023. https://www.nature.com/articles/s42254-023-00581-4
- Nature Machine Intelligence. "The AI writing on the wall." 2023. https://www.nature.com/articles/s42256-023-00613-9
- ICMJE. "Use of AI by Authors." Recommendations for the Conduct, Reporting, Editing, and Publication of Scholarly Work in Medical Journals. https://www.icmje.org/recommendations/browse/artificial-intelligence/ai-use-by-authors.html
- Nature Portfolio. "Artificial Intelligence (AI)" policy page. https://www.nature.com/srep/journal-policies/ai
- Hosseini, Mohammad, David B. Resnik, and Kristi Holmes. "The ethics of disclosing the use of artificial intelligence tools in writing scholarly manuscripts." 2023. https://journals.sagepub.com/doi/10.1177/17470161231180449
- "Large Language Models in Qualitative Research: Can We Do the Data Justice?" arXiv. 2024. https://arxiv.org/abs/2410.07362
- "Large Language Model for Qualitative Research — A Systematic Mapping Study." arXiv. 2024. https://arxiv.org/abs/2411.14473
- "Large Language Models in Qualitative Research: Governance, Validity, and the Limits of Computational Assistance." SSRN. 2026. https://papers.ssrn.com/sol3/Delivery.cfm/6577019.pdf?abstractid=6577019&mirid=1
- Research Integrity and Peer Review. "Fighting reviewer fatigue or amplifying bias? Considerations and recommendations for use of ChatGPT and other large language models in scholarly peer review." 2023. https://link.springer.com/article/10.1186/s41073-023-00133-5
