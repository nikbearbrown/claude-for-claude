# Chapter 9 — Research Packets and Meeting Notes

## TL;DR

- Cowork can transform scattered notes, sources, and meeting transcripts into structured artifacts — research packets, decision logs, action registers, briefing documents.
- The central risk is synthesis without accountability: Cowork can make notes sound more decisive, coherent, and sourced than they actually are.
- Every citation, action item, decision, and name needs human confirmation before the artifact is used or shared.
- "Cowork organizes. You own the claims."

---

## Opening Scene: The Meeting That Changed Itself

You took rough notes in Tuesday's strategy meeting. Three agenda items, some back-and-forth, a few things agreed and a few things tabled. You give Cowork your notes and ask for a summary with action items.

The result looks polished. Decisions are in bold. Owners are named. Deadlines appear. The thing that was merely discussed is now listed as "agreed." The thing that was explicitly tabled has been quietly resolved into a next step. One person who only asked a question now has a task assigned to them.

Nothing Cowork wrote is fabricated exactly — it is all in the spirit of your notes. But the spirit is not the record.

You send the summary to the team. Three people push back. One person is confused about the task attributed to them. The tabled item comes up again the next week because no one realized it had been formally closed.

This is not a Cowork malfunction. It is what synthesis looks like when it runs ahead of the facts. The artifact was produced. The judgment was not.

---

## What This Chapter Lets You Do

By the end of this chapter, you will be able to:

- Use Cowork to assemble research lead packets, meeting summaries, decision logs, and briefing documents from scattered source materials.
- Distinguish organizing, summarizing, synthesizing, and deciding — and know which ones require human confirmation.
- Set up Cowork task briefs that preserve uncertainty rather than papering over it.
- Verify citations, action items, decisions, and sensitive information before using or sharing any artifact.
- Recognize when meeting notes or research materials are too sensitive to feed into a personal Cowork session.

---

## Core Concept: Cowork as Organizer, Not Owner

Research packets and meeting notes are, on paper, ideal Cowork tasks. They are scattered. They are text-heavy. They have a predictable artifact form — a table, a summary, a structured document. They take hours to do by hand. Cowork can accelerate that.

The problem is not what Cowork does poorly. It is what it does well. A language model is very good at making material sound coherent, organized, and decided. That is exactly the trait that makes meeting summaries dangerous: coherent-sounding is not the same as accurate.

Anthropic's safety guidance for Cowork is explicit that sensitive material — notes, documents, research files — requires care about what enters the session and what is shared from it (Anthropic, "Use Claude Cowork safely"). That guidance points in two directions: what you put in, and what you take out.

**Four operations to keep distinct:**

1. **Organizing** — grouping, sorting, formatting existing material without changing its meaning. Safest Cowork operation.
2. **Summarizing** — condensing material, which requires selection. Cowork may drop nuance, uncertainty, or minority views.
3. **Synthesizing** — proposing patterns or themes across material. Requires the most human judgment to verify.
4. **Deciding** — turning discussion into commitments, discussion into agreement, options into choices. This is the human's job. Cowork must not decide.

Privacy scholar Helen Nissenbaum's contextual integrity framework helps explain why notes that seem ordinary can be sensitive (Nissenbaum, contextual integrity framework). Notes flow appropriately when the context of their creation matches the context of their use. A meeting note created in a private team conversation does not automatically belong in a Cowork summary that will be shared up the chain. The context changed; the integrity question follows.

The Microsoft Research study on generative AI and critical thinking found that knowledge workers who lean on AI tools for synthesis report reduced cognitive engagement with the material (Microsoft Research, CHI 2025). This is the pattern to interrupt. Cowork builds the packet; you apply the judgment about what the packet actually says.

---

## Worked Walkthrough: Three Artifact Types

### Artifact 1: The Research Lead Packet

**The situation:** You are starting a literature search on AI use in nonprofit grant reporting. You have a folder of rough notes, saved web pages, a few PDFs, and a running doc of search terms you have tried.

**What Cowork can produce:**
- A candidate source table (title, author, year, brief description, relevance note)
- A list of search terms and gaps
- Open questions to guide the next phase

**The task brief:**

> Read the files in /research-notes/ai-grants/. Produce a candidate source table with columns: Title, Author/Organization, Year, Description (2 sentences max), Relevance to AI and nonprofit grant reporting, and a Verified? column I will fill in manually. List search terms found in my notes, then identify three to five open questions the notes do not answer. Do not invent sources. If a citation is incomplete, mark it [incomplete — verify]. Do not mark any source Verified; leave that column blank.

**Human gate before using:**
- Open every candidate source. Confirm it exists and says what the table claims.
- Fill in the Verified? column only after checking.
- Add the limitation column for any source with a scope mismatch.

**Why the brief matters:** Telling Cowork to leave the Verified column blank is not belt-and-suspenders caution. It is the difference between a research lead and a literature review. A lead is a starting point. A review is a claim. These are different documents with different burdens of proof.

The ICMJE guidelines make the accountability point clearly: human authors remain responsible for the accuracy of all cited content regardless of whether AI tools helped assemble it (ICMJE, "Use of AI by Authors"). Cowork helped you organize. You own every citation.

### Artifact 2: The Meeting Summary

**The situation:** You led a 45-minute team meeting. You have rough notes in a doc and a voice memo transcript. You want a structured summary with decisions, action items, and open questions.

**What Cowork can produce:**
- A decisions section
- An action items register
- An unresolved items list
- A brief narrative summary

**The task brief:**

> Read meeting-notes-2026-05-28.docx and transcript.txt. Produce a meeting summary with four sections: Decisions, Action Items, Unresolved Items, and Summary (3 sentences max). For every Decision and Action Item, include a "Evidence in notes" column showing the exact phrase from the source that supports it. Mark any item where the evidence is indirect or unclear with [uncertain — verify]. Do not infer owners or deadlines if the notes do not state them explicitly. If you cannot find a direct attribution for something, list it in Unresolved Items instead.

**The recommended template** for the artifact:

| Item | Evidence in notes | Owner | Due date | Confidence | Human confirmed? |
|---|---|---|---|---|---|
| (decision or action item) | "exact phrase from notes" | (if stated) | (if stated) | High / Uncertain | [ ] |

**Human gate before sharing:**
- Read the original notes alongside the summary.
- Check every Decision: was this agreed or discussed? These are not the same.
- Check every Action Item: did the person named actually accept the task?
- Check Unresolved Items: are there items that should be here but were resolved into the other sections?
- Change the owner of the Human confirmed? column from Cowork to yourself by filling it in.

The research on LLMs in qualitative work notes that AI tools can help organize and compare material but that interpretation requires domain knowledge, context, and reflexivity the model does not have (arXiv, 2024, "Large Language Models in Qualitative Research"). A meeting summary is qualitative interpretation. You were in the room. Cowork was not.

### Artifact 3: The Briefing Document

**The situation:** You need to prepare a two-page brief for an executive on a new regulatory development. You have three policy documents, your own notes from two calls, a draft analysis from a colleague, and a few news articles.

**What Cowork can produce:**
- Background section
- Key points
- Risks and open questions
- Source list with links

**The task brief:**

> Read the five files in /briefing-source/. Produce a two-page briefing document with sections: Background (4-6 sentences), Key Points (bulleted, max 6), Risks and Open Questions (bulleted), and a Sources section listing every file used. For each Key Point, note which source supports it in parentheses. If a point is supported by only one source, mark it [single-source — verify]. If sources conflict, list the conflict in Risks and Open Questions rather than choosing one version. Do not estimate or fill in gaps with general knowledge.

**Human gate before sending:**
- Check every Key Point against its source file. Does the source actually say this?
- Confirm the Sources list is complete — no material from your session context that did not come from the files.
- Check for omissions: is anything important in the source files absent from the brief? What did Cowork leave out, and why might it matter?

This last check is the one most often skipped. Cowork will produce a coherent brief. Coherent does not mean complete. The single most common error in AI-assisted briefing documents is not invention — it is the omission of material that complicates the story (NIST AI RMF Generative AI Profile, 2024).

---

## The Human Gate

Every research packet or meeting artifact has a point at which it stops being a Cowork output and becomes something you are responsible for. That transition is not optional. It happens when you use the artifact, share it, or allow it to shape a decision.

Before that transition, verify:

1. **Citations:** Does each source exist? Does it say what the artifact claims?
2. **Decisions:** Were they agreed or discussed? Did anyone object?
3. **Action items:** Did the named person accept the task? Is the deadline real?
4. **Sensitive information:** Should any names, details, or quotes be removed before sharing?
5. **Uncertainty:** Is the artifact claiming more confidence than the source material warrants?
6. **Completeness:** What is missing that a reader would need?

Research integrity guidance is consistent on this point: transparent and accountable AI-assisted writing requires that the human author takes responsibility for content, citations, and accuracy (Nature Machine Intelligence, 2023). Cowork is not a co-author. It is a research assistant whose work you have reviewed and stand behind.

---

## Common Mistakes

**Treating a candidate source as a verified source.** Cowork's source table is a lead list, not a bibliography. Every entry needs to be checked in the actual database, document, or web page before you cite it.

**Letting a discussion item become a decision.** Meeting notes contain "we should," "it might be worth," and "let's think about." Cowork's summary may turn these into "decision: we will." Read every decision against the source notes.

**Inferring action item owners.** If the notes do not say who will do something, Cowork should not assign it. If it does, that assignment is a guess, not a commitment.

**Sharing without removing sensitive information.** Meeting notes can include personal health details, personnel discussions, client information, or confidential strategy. Before sharing, check whether any of that survives in the summary. The contextual integrity question: does this information belong in the destination context? (Nissenbaum, contextual integrity framework).

**Treating Cowork-generated themes as findings.** If you ask Cowork to group qualitative notes into themes, the themes are organizational proposals, not research findings. The distinction matters for any work that will be published, presented, or used to make decisions (arXiv, 2024, "Large Language Model for Qualitative Research — A Systematic Mapping Study").

**Skipping the uncertainty review.** Uncertainty is often the correct output. If the source material is ambiguous, conflicting, or incomplete, the artifact should say so. A summary that sounds resolved when the situation is unresolved is a liability.

---

## Try This

**Exercise 1 (hands-on):** Take the notes from your last team meeting — any format, however rough. Write a task brief using the template from Artifact 2 above. Ask Cowork to produce a summary with the Evidence in notes column filled in. Then open your original notes and read them side by side with the output. Find one item that Cowork resolved that should have stayed in Unresolved Items. Write a one-sentence note explaining what was missing.

**Exercise 2 (hands-on):** Identify a topic relevant to your work that you have been meaning to research. Gather whatever you have — saved links, notes, a few documents. Write a brief asking Cowork for a candidate source table. Use the instruction "Do not mark any source Verified; leave that column blank." Open three of the candidates and check whether they say what the table claims. Note what you find.

**Exercise 3 (reflection):** Think of a briefing document or summary you have produced in the past year. What would it mean to ask, for each key point: "What source supports this, and have I checked it?" What would you have caught?

---

## What Would Change My Mind

The claim here is that synthesis without accountability is the central risk of Cowork-assisted knowledge artifacts. I would revise that if future work showed reliable methods for Cowork to flag its own uncertainty at the claim level — citing confidence scores that actually calibrated to accuracy. If that evidence emerged, the verification workflow could be lighter.

I would also revise the treatment of qualitative data if research showed that LLM-generated themes reliably preserved researcher intent and contextual nuance. The current evidence suggests they do not, but that literature is still developing (arXiv, 2024, "Large Language Models in Qualitative Research"). The caution here is calibrated to present evidence, not permanent skepticism.

---

## Still Puzzling

Two open questions this chapter does not fully resolve:

**When do meeting notes become an official record?** This varies by organization, sector, and sometimes law. In some contexts, a meeting summary sent to participants becomes an official record. In others, it is informal. Knowing which context you are in changes what "human gate" means. The chapter teaches the verification habit; organizational policy governs the stakes.

**How should consent work for personal or sensitive notes?** If you are working with interview notes from research participants, notes that include identifiable details, or records of private conversations, Cowork introduces a third party into a two-party context. The research on LLMs and qualitative data does not yet have settled guidance on this (arXiv, 2024, "Large Language Models in Qualitative Research"). Use the most conservative rule available: identifiable, sensitive material from others does not go into a personal Cowork session without explicit consent or institutional approval.

---

## Bridge to Chapter 10

Chapter 9 showed Cowork at its most useful for knowledge work — organizing, assembling, structuring artifacts from scattered materials. Chapter 10 takes that productivity and asks the harder question: what should not go into Cowork at all?

The previous chapters established how to prepare a workspace, write a brief, review a plan, and verify outputs. Chapter 10 pulls all of that back and asks you to refuse or redesign the task before it starts. The judgment is not "did Cowork do this correctly?" It is "should this task have been delegated in the first place?"

---

## Sources Used

- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- ICMJE, "Use of AI by Authors." https://www.icmje.org/recommendations/browse/artificial-intelligence/ai-use-by-authors.html
- Microsoft Research, "The Impact of Generative AI on Critical Thinking," CHI 2025. https://www.microsoft.com/en-us/research/publication/the-impact-of-generative-ai-on-critical-thinking-self-reported-reductions-in-cognitive-effort-and-confidence-effects-from-a-survey-of-knowledge-workers/
- Nature Machine Intelligence, "Writing the rules in AI-assisted writing," 2023. https://www.nature.com/articles/s42256-023-00678-6
- NIST, "Artificial Intelligence Risk Management Framework: Generative Artificial Intelligence Profile," 2024. https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf
- Nissenbaum, Helen, contextual integrity privacy framework. https://crypto.stanford.edu/portia/papers/RevnissenbaumDTP31.pdf
- "Large Language Models in Qualitative Research: Can We Do the Data Justice?" arXiv, 2024. https://arxiv.org/abs/2410.07362
- "Large Language Model for Qualitative Research — A Systematic Mapping Study," arXiv, 2024. https://arxiv.org/abs/2411.14473
- Research Integrity and Peer Review, "Fighting reviewer fatigue or amplifying bias?" 2023. https://link.springer.com/article/10.1186/s41073-023-00133-5
