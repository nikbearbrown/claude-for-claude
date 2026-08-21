# Chapter 7 — Prompts for Research and Literature Work

The citation looks right. Author, year, journal name, volume, page range. It has the shape of a real source. The sentence it supports is plausible, the topic is one Claude clearly knows something about, and the prose around it reads like a literature review.

The source does not exist.

This is the most important thing to understand before using Claude for any research or literature task: the model does not retrieve sources from a database. It generates text that has the statistical shape of scholarly writing, including citations. A citation from Claude is a hypothesis about what a relevant source might look like. Some of those hypotheses correspond to real publications. Some do not. The model cannot reliably tell the difference, and neither can you without checking.

That constraint does not make Claude useless for research. It makes precision about what you are asking Claude to do essential.

---

## What This Chapter Lets You Do

After this chapter you will be able to:

- Use Claude to expand and refine search terms for database queries
- Summarize sources you have already verified and provided
- Build a literature matrix from provided sources
- Draft a gap analysis from materials you have assembled
- Write prompts that prevent Claude from inventing sources or claims
- Apply a verification checklist before using any Claude-generated research output

---

## The Core Distinction: Leads vs. Evidence

Research work requires you to hold two categories separate throughout every interaction with Claude.

**A lead** is a direction worth investigating. A lead might be a research theme, a search term, a potential framework, or a suggested author whose work sounds relevant. Leads are cheap and useful. A wrong lead costs you time in a database search; it does not corrupt your argument.

**Evidence** is a verified source that says what you claim it says, in the form you cite, from a location you can point to. Evidence requires verification. Claude cannot provide evidence directly — it can only provide leads that may or may not become evidence after you check them.

The single most common error in Claude-assisted research is treating a lead as evidence. This happens because Claude's fluency makes its output feel authoritative. The voice of scholarship does not prove the substance of scholarship.

Every prompt in this chapter is designed around that distinction. When a prompt asks Claude to suggest search terms, it is asking for leads. When a prompt asks Claude to summarize a source, the source must have been provided by you — otherwise Claude is generating text that sounds like a summary but is fabrication.

---

## Before / After: A Research Prompt Walkthrough

### The Task

You are beginning a literature review on organizational change fatigue in healthcare settings. You need search terms and a sense of the relevant frameworks.

### BEFORE — Source-Authority Prompt

```
Give me a literature review on organizational change fatigue in
healthcare. Include key authors, landmark studies, and a list of
major theoretical frameworks with citations.
```

What this prompt will produce: a literature review that looks thorough, names real-sounding authors and journals, provides citations in proper format, and describes theoretical frameworks confidently. Some of those sources may be real. Some will not be. The ones that are real may not say what Claude claims they say. You cannot tell which is which from reading the output.

This is not Claude being dishonest. It is the model doing what it does: generating plausible continuations of the kind of text you asked for. The output fits the pattern of a literature review. The research behind it was not done.

### AFTER — Leads-First Prompt

```
I am beginning a literature review on organizational change fatigue
in healthcare settings. I have not yet searched any databases.

Help me with two things:

1. Search term expansion: Give me 8–12 search terms or phrases I
   should try in PubMed, PsycINFO, and Google Scholar. Include both
   narrow clinical terms and broader organizational/management terms.
   Do not cite sources — I will find them myself using these terms.

2. Conceptual scaffolding: What theoretical frameworks are commonly
   applied to change fatigue in organizational contexts? Name the
   frameworks (e.g., "Job Demands-Resources model") without claiming
   specific citations. I will look up the primary sources.

These are leads, not evidence. I will verify each source independently.
```

What this prompt produces: usable search terms you can run in actual databases, and named frameworks you can then search for in the literature. Neither requires fabrication because neither requires Claude to claim a source exists. The output is explicitly framed as leads, which is the only honest framing available.

---

## Working With Sources You Have Provided

Once you have gathered real sources — PDFs, excerpts, abstracts you have verified — Claude becomes a powerful summarizer, extractor, and synthesizer. The key constraint: you must provide the text. Claude works from what is in the conversation, not from its training memory of what a source might say.

**Summarization prompt:**

```
Here is the abstract and introduction of a paper I have verified
and will cite: [paste text]

Summarize the paper's main argument and methodology in 100 words.
Do not add claims not present in the text I provided.
If something is unclear from the provided excerpt, say so rather
than inferring.
```

**Extraction prompt:**

```
I am building a literature matrix. Here are excerpts from three
papers I have verified: [paste excerpts with author/year labels]

For each paper, extract:
- Main research question
- Sample or data source
- Key finding
- Limitation the authors acknowledge

Use only information present in the excerpts. If a field is not
stated in the excerpt, write "not stated in excerpt."
```

The "not stated in excerpt" instruction is critical. Without it, Claude will infer plausible-sounding values from context — which is exactly how fabrication enters through a legitimate-looking summarization task.

---

## The Literature Matrix

A literature matrix organizes sources across consistent dimensions so you can see patterns, gaps, and conflicts at a glance. It is one of the most useful formats Claude can help you build — provided the sources are yours.

**Matrix-building prompt:**

```
I have attached/pasted excerpts from [N] papers on [topic].

Build a literature matrix with these columns:
| Author (Year) | Research Question | Method | Sample | Key Finding | Limitations |

Use only information stated in the excerpts I provided.
Mark any cell "not stated" if the excerpt does not contain that information.
Do not add rows for sources I have not provided.
```

Once the matrix is built from your verified sources, you can ask Claude to identify patterns:

```
Based only on the matrix above — no additional sources — what
patterns do you see across the findings column? What gaps appear
in the methods column?
```

The gap analysis from a provided matrix is legitimate interpretation. The gap analysis from Claude's training memory of what the literature contains is fabrication wearing the costume of analysis.

---

## The Source Verification Checklist

Before you incorporate any Claude-generated research output into your work, apply this checklist. This is the human gate: the review step that keeps a lead from becoming an unverified claim in your writing.

| Check | Action if Failed |
|---|---|
| Does the source exist? | Search the journal, database, or DOI directly |
| Does the source say what Claude claimed? | Read the abstract or full text |
| Is the citation format correct? | Check author names, year, volume, pages |
| Did I provide this source, or did Claude generate it? | If generated, treat as lead only |
| Does the quote appear in the text? | Find the page number; paste-check the string |

[verify — current as of writing] Claude's citation hallucination rate varies by model version, domain specificity, and how the prompt was framed. Research as of early 2024 found hallucinated references in a substantial fraction of LLM-generated citations (Walters and Wilder 2023, Alkaissi and McFarlane 2023). The rate is not zero in any current model. Treat verification as mandatory, not optional.

---

## COPE and Publisher Guidance

The Committee on Publication Ethics (COPE) and many academic publishers now have explicit policies on the use of AI in research and writing. The core consensus across these policies is that AI tools cannot be listed as authors (because authorship requires accountability for the work), and that researchers are responsible for the accuracy of every claim in their submissions, including those assisted by AI tools (COPE 2023).

This means: if a Claude-assisted literature summary contains a fabricated citation that makes it into a submitted paper, the author is responsible for that fabrication. "Claude generated it" is not a defense. The verification step is not optional discipline — it is the professional and ethical obligation that shifts when you use AI in research.

If you are working in an academic or publishing context, check your institution's and target journal's current AI-use disclosure policies. They are changing frequently. [verify — current as of writing]

---

## Search Handoff Prompts

One of Claude's most underused capabilities in research work is generating search queries formatted for specific databases. This is a lead-generation task with low hallucination risk because you are asking for query strings, not source claims.

**PubMed/database query prompt:**

```
I am searching PubMed for studies on [topic]. My current search
string is: [paste current string]

Suggest three alternative search strings using MeSH terms where
appropriate. Include at least one broader and one narrower variant.
Do not suggest specific papers — I will run these queries myself.
```

**Snowball search prompt:**

```
I have verified these three papers on [topic] by reading them:
[Author Year], [Author Year], [Author Year]

Based on typical citation patterns in this area, what theoretical
concepts or adjacent topics might their reference lists contain?
I will use these as search terms, not as citations.
```

---

## Common Mistakes

**Asking for a literature review without providing sources.** The output will look like a literature review. It is not a literature review. It is Claude's statistical projection of what a literature review on that topic might say.

**Treating named frameworks as citations.** When Claude names a theoretical framework (e.g., "Lewin's change model"), that is probably a real framework. But the specific source, page, year, and claim about it still require verification. A real framework name does not validate Claude's claims about what it says.

**Forgetting "not stated in excerpt."** Without this instruction, Claude will fill matrix cells from inference rather than provided text. The matrix looks complete. The completeness is partly fabricated.

**Using the same verification standard for summaries of provided text vs. generated citations.** These are different tasks with different risk profiles. A summary of text you provided is low-risk; a citation Claude generated is high-risk. Apply the checklist accordingly.

**Accepting a plausible DOI.** A DOI has the format of a real identifier. Claude can generate a plausible-looking DOI that does not resolve to anything. Check the DOI directly.

---

## Try This

**Exercise 1 — Search term expansion.**
Choose a topic you are currently researching or curious about. Write a leads-first prompt that asks for search term expansion across two or three databases. Run at least three of the suggested terms in a real database and report which produced usable results. Did Claude's term suggestions align with what the database actually indexed?

**Exercise 2 — Verification stress test.**
Ask Claude to generate a literature review on a narrow topic, including citations. Then check every citation: does the source exist, does it say what Claude claimed, is the year correct? Record the accuracy rate. This exercise is not about catching Claude being bad — it is about calibrating your intuition for when Claude's output requires what level of verification.

**Exercise 3 — Matrix from provided sources.**
Take three to five papers you have already read and verified. Paste their abstracts into a conversation. Ask Claude to build a literature matrix using the template in this chapter. After it produces the matrix, check every "not stated" cell against the text — did Claude miss information that was present? Check every filled cell — did Claude infer anything that was not stated? What does this tell you about where the matrix requires your attention?

---

## What Would Change My Mind

This chapter treats citation verification as mandatory because the hallucination rate is nonzero. If a future version of Claude (or a Claude tool with verified database access) could provide citations with auditable provenance — a retrievable chain from the claim to the actual source text — the calculus would change. Leads vs. evidence would collapse into a single category, and some of the verification burden would move to the system. That is not the current situation. [verify — current as of writing] If that changes, this chapter's core constraint changes with it.

---

## Still Puzzling

The line between "summarize what I provided" and "supplement with what you know" is not always clear in practice. When you provide four sources and ask for a synthesis, does Claude stay within those sources or silently add claims from training? Empirically, it tends to blend — particularly when provided sources are sparse. The "do not add claims not present in provided text" constraint helps, but the extent to which Claude reliably honors it without a strong anchoring instruction is not fully characterized. This deserves more testing.

---

## Bridge

Research produces the raw material; writing turns it into an argument. Chapter 8 takes the same specification discipline into the writing and editing process, where the risks are different: not fabricated citations, but voice drift, fact-invention in prose, and the slow blurring of what you actually claimed versus what Claude generated on your behalf.

---

## Sources Used

- Anthropic Claude documentation. https://docs.anthropic.com/
- Alkaissi, H., and McFarlane, S. I. (2023). Artificial hallucinations in ChatGPT: Implications in scientific writing. *Cureus*, 15(2), e35179.
- Walters, W. H., and Wilder, E. I. (2023). Fabrication and errors in the bibliographic citations generated by ChatGPT. *Scientific Reports*, 13, 14045.
- Committee on Publication Ethics (COPE). (2023). Authorship and AI tools. https://publicationethics.org/
- UNESCO. (2023). Guidance for generative AI in education and research. https://www.unesco.org/
- NIST AI Risk Management Framework (NIST AI RMF). https://www.nist.gov/system/files/documents/2023/01/26/AI%20RMF%201.0.pdf
- Systematic review methodology (general field practice; see Cochrane Handbook for Systematic Reviews of Interventions)
