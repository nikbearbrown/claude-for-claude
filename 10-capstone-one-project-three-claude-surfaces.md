# Chapter 10 — Capstone: One Project, Three Claude Surfaces

## TL;DR

- The capstone routes one real project through the correct Claude surfaces, with explicit routing decisions, approval gates, verification evidence, and a reflection that transfers to the next project.
- The deliverable is not just the final artifact — it is the process record that shows your judgment at each step.
- Claude can draft, assemble, run scripts, and organize files. The claim, the evidence, the permission, and the final action stay human-owned.
- This chapter ends the book by asking you to do the full loop once, on work that matters to you.

---

## Opening Scene

Here is a project that happens in real life: a researcher needs a briefing note summarizing recent literature on a specialized topic, formatted for a non-specialist audience, with a source matrix appended. It is about four hours of focused work. Not a dissertation, not a quick search — something in between.

Without Claude, the researcher searches databases, reads abstracts, builds the matrix by hand, drafts the note, edits it twice, and sends it. With Claude, the same project runs differently. But how?

The naive version: paste a prompt into Claude chat, get back a summary, send it. The confident version: open Claude chat, get a summary, assume the sources are real, assume the claims are accurate, assume the format is correct, send it. The skilled version: decide which tasks belong in chat, which belong in Cowork, what the human gate is for each, and what evidence you will collect to verify the result before it leaves your name.

This chapter is the skilled version.

It works through a single project from first decision to final delivery, showing how the routing, the approval gates, and the verification actually operate. Then it gives you a template to run the same loop on your own project.

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to:

- Decompose a real project into tasks and route each task to the correct Claude surface
- Set up a project workspace, write task packet specifications, and run approval gates
- Collect verification evidence at each hand-off
- Produce an AI-use log and a reflection that transfers to your next project
- Recognize when to use all three surfaces and when to use fewer

---

## Before You Begin: Project Selection

The capstone works best on a project that is:

- Small enough to complete in a few hours
- Rich enough to need at least two Claude surfaces
- Real enough that the output matters — a document you will actually use, share, or submit
- Safe enough that you are not routing genuinely sensitive, proprietary, or human-subjects data through AI systems under time pressure

If you completed Exercise 4 in Chapter 9, you already have a candidate. If not, choose now from this list:

- A briefing note on a topic in your field for a non-specialist audience
- A course module outline with three learning objectives, two examples, and a quiz
- A data story using a public dataset with a chart and a one-page narrative
- A small operational workflow document for a recurring process in your work
- A literature matrix and annotated source list for a project you are already researching

If you do not have access to Claude Code or Claude Cowork, read the "Constrained Version" sidebar below and run the simulation version with Claude AI only.

---

## The Worked Example: A Research Briefing Note

The worked example is a research briefing note. The project: summarize recent evidence on the use of AI tools in higher education advising, for an audience of non-specialist administrators, with a source matrix appended. Approximately four pages plus a two-column source table.

Walk through this example in detail. Then apply the same structure to your chosen project.

---

### Step 1: Define the Project and Declare Boundaries

Before opening any Claude surface, write a one-paragraph project brief.

**Project brief (example):**
> I am producing a four-page briefing note on AI tools in higher education advising for non-specialist administrators. My final artifact includes a narrative section and a two-column source matrix (source + one-sentence finding). I will use peer-reviewed and institutional sources published in the past three years. No student records, personnel files, or confidential institutional data will be included. I will not send this outside the organization without human review of all factual claims.

**Data and privacy boundary (example):**
> Inputs: approved public papers, public institutional reports, my own notes. Forbidden: names of individual students or advisors, internal audit data, proprietary institutional reports marked confidential. Claude may not send or share outputs; all distribution is human-initiated.

Write this before you touch Claude. The boundary note is your pre-commitment. Brown, Collins, and Duguid (1989) established that skills learned in authentic contexts transfer better than abstract practice — and the most authentic thing about this project is that your boundary decisions are real decisions with real consequences, not hypotheticals.

---

### Step 2: Decompose the Project and Route Each Task

Now list the tasks the project requires and assign each to a surface.

**Surface-routing map (example):**

| Task | Surface | Why |
|---|---|---|
| Clarify the scope and define the audience | Claude AI (chat) | Thinking and refinement, no file manipulation needed |
| Generate search terms and suggest sources | Claude AI (chat) | Idea generation; I select and verify the sources |
| Build the source matrix from approved PDFs | Claude Cowork | File-heavy, repetitive, structured output from a folder |
| Draft the narrative briefing note from the matrix | Claude Cowork | Document assembly from verified sources |
| Review and revise the narrative for accuracy | Human-only | Claim verification and editorial judgment |
| Check figures or statistics for any data claims | Human-only | Calculation verification; no data processing delegated |
| Final formatting and distribution decision | Human-only | Irreversible action; human gate required |
| Generate a reproducible citation cleanup script | Claude Code (optional) | Only if citations need programmatic standardization |

Notice the last row: Claude Code appears only if needed. Not every project needs all three surfaces. The routing decision is never "use all three to show I have learned the book." It is always "which surface belongs on which task?"

Perkins and Salomon (1992) argued that transfer requires both near practice and explicit bridging. The routing map is the bridging artifact: it forces you to name why each task goes where it goes.

---

### Step 3: Write Task Packet Specifications

For each AI-assisted task, write a task packet before you open the Claude surface. A task packet answers four questions:

1. What should Claude produce?
2. What context does Claude need?
3. What are the constraints?
4. What will I verify before using the output?

**Task packet 1: scope clarification (Claude AI)**

> **Produce:** Three to five clarifying questions about the scope of this briefing note that would help me write a sharper specification. I will answer the questions; Claude does not answer them.
> **Context:** The topic is AI tools in higher education advising, for non-specialist administrators, approximately four pages plus a source matrix.
> **Constraints:** Questions should surface ambiguities in audience, scope, time range, and output format. Do not draft the briefing note.
> **Verification:** I will answer the questions and revise the project brief before any further delegation.

**Task packet 2: source matrix assembly (Claude Cowork)**

> **Produce:** A two-column table. Column 1: full citation (Author, Year, Title, Journal/Source). Column 2: one-sentence finding directly relevant to AI in advising.
> **Context:** I will place approved PDFs and one institutional report in a named folder. Cowork reads that folder only.
> **Constraints:** Use only documents in the designated folder. Do not invent citations or findings. If a document does not contain a relevant finding, leave the finding cell blank with the note "no relevant finding in this source."
> **Verification:** I will check every citation against the source file name and verify a sample of three findings by reading the original passage.

**Task packet 3: draft briefing note (Claude Cowork)**

> **Produce:** A four-page narrative briefing note for non-specialist administrators. The note should include: a one-paragraph overview, three to four thematic findings from the source matrix, two specific examples with source citations, and a one-paragraph implications section.
> **Context:** The verified source matrix from Task 2. No other sources.
> **Constraints:** Every factual claim must include an in-text citation corresponding to a source in the matrix. Do not make claims the matrix does not support. Do not recommend specific commercial products. Do not exceed four pages.
> **Verification:** I will check every in-text citation against the matrix, verify that no claim appears in the note without a source in the matrix, and check the implications section for unsupported recommendations.

The task packet forces you to think about verification before you generate output. Collins, Brown, and Newman (1989) described this as the "articulation" phase of cognitive apprenticeship: making tacit decisions explicit so they can be examined. Writing the verification step before you run the task is how you stay the decision-maker rather than the reviewer.

---

### Step 4: Run the Workflow With Approval Gates

Now run the tasks in order.

**Gate 1: before Cowork touches any files.**
Review the files you have placed in the folder. Confirm that nothing forbidden is present. Confirm the folder is the correct one. Start Cowork only after this review (Anthropic, "Use Claude Cowork safely," Claude Help Center [verify — current as of writing]).

**Gate 2: before accepting the source matrix.**
Cowork produces the matrix. Before you move to the draft, check a sample of three rows:
- Does the citation match a real document in your folder?
- Does the one-sentence finding appear in that document?
- Did Cowork add any sources that were not in your folder?

If the matrix has errors, correct them now. The draft will inherit every matrix error, so catching them here is cheaper than catching them in the narrative.

**Gate 3: before accepting the draft.**
The narrative arrives. Before you revise it, check:
- Every in-text citation: does it correspond to a row in the matrix?
- Three specific factual claims: are they accurately representing what the source says, or has the framing shifted?
- The implications section: does it recommend anything the sources do not support?

Mark corrections in your own words. Do not ask Claude to verify its own output as a substitute for independent checking. Asking Claude "is this accurate?" is not independent verification.

**Gate 4: before any external distribution.**
The final artifact is human-owned. No agentic tool sends it. You read it, you decide it is ready, you send it from your own account, under your own name, with your own judgment behind it.

Anthropic's Cowork safety documentation makes this explicit: scheduled tasks, browser actions, email sending, and file deletion require user permission and review (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]). The capstone applies this at every gate, not just the last one.

---

### Step 5: Collect Verification Evidence

Your AI-use log documents each gate. Keep it simple:

| Task | Surface used | Gate check | Result | Action taken |
|---|---|---|---|---|
| Scope clarification | Claude AI | Reviewed questions, answered all five | Two questions surfaced scope issues | Revised project brief |
| Source matrix | Claude Cowork | Checked 3 of 7 citations; found 1 blank finding row | One source had no relevant finding | Kept blank row; removed from narrative |
| Draft narrative | Claude Cowork | Checked all 7 in-text citations; verified 3 claims | One claim overstated; implications section added one unsupported recommendation | Revised claim; removed unsupported recommendation |
| Final artifact | Human | Read in full; checked format, citations, and implications | Ready | Distributed |

This log is the capstone's process artifact. Researchers on GenAI assessment design (arXiv 2508.11709, 2025) argue that assessments in the age of generative AI should evaluate process evidence — task routing, verification, disclosure, and reflection — not only final output quality. The log is that evidence.

---

### Step 6: The Reflection

The reflection is not a feelings paragraph. It is a structured transfer exercise.

Answer these six questions in writing:

1. **Which tasks were easy to route?** (What made them easy?)
2. **Which task tempted overdelegation?** (What would have happened if you had delegated it?)
3. **What did Claude get wrong?** (What was the error, how did you find it, what did you do?)
4. **What evidence changed your mind?** (Was there a gate check that surprised you?)
5. **What would you automate less next time?** (Which approval gate was too permissive?)
6. **What would you prepare better next time?** (Context, instructions, boundary note, folder structure?)

Then add two transfer questions:

7. **One future project where this workflow transfers:** Name it. Describe the routing.
8. **One future project where this workflow should not transfer:** Name it. Explain why — what makes it unsuitable for AI assistance, or for this particular surface routing?

Donald Schön described the reflective practitioner as someone who learns by reflecting in and on action. The six-plus-two reflection format does both: it asks what happened during the project and what you will carry forward.

---

## The Capstone Artifact Bundle

Your capstone is complete when you have produced:

1. **Project brief** — one paragraph, including the data/privacy boundary note
2. **Surface-routing map** — one table, with rationale for each routing decision
3. **Task packet specifications** — one packet per AI-assisted task
4. **AI-use log** — one table, with gate checks and actions taken
5. **Verification checklist** — the human-gate checks you ran (sources, calculations, claims, figures)
6. **Final artifact** — the actual deliverable (the briefing note, the module outline, the data story, the process document)
7. **Reflection note** — six structured questions plus two transfer questions

Items 1 through 5 document your process. Item 6 is what you produced. Item 7 is what you learned.

If you only submit item 6, you have produced a polished artifact. You have not demonstrated that your workflow is trustworthy.

---

## Constrained Version: Chat-Only Simulation

If you do not have access to Claude Cowork or Claude Code, run the same project using Claude AI (chat) only and manual file work.

**What changes:**
- Replace Cowork file assembly with manual copy-paste from verified sources into a structured table you build yourself
- Replace Claude Code scripts with manual spreadsheet work or find-and-replace
- Keep all four gates; they apply in chat-only workflows too

**What does not change:**
- The project brief and boundary note
- The task packet specification for each chat session
- The gate checks before you accept each output
- The AI-use log and reflection

The skill is surface selection and verification, not platform mastery. The constrained version tests the same discipline.

---

## Common Mistakes

**Treating the final artifact as the capstone deliverable.** A polished briefing note or slide deck is not evidence of a trustworthy workflow. It is the output of one. The process artifacts — routing map, task packets, log, verification checklist, reflection — are what show that the workflow was governed.

**Using all three surfaces because the chapter says to.** If your project does not need Claude Code, do not use Claude Code. Unnecessary surface use adds complexity and risk without adding value. The routing decision should always be driven by the task, not the chapter.

**Letting Cowork or Code run to completion before reviewing intermediate outputs.** Each gate exists because errors in early steps compound in later steps. A source matrix error that goes uncaught becomes a narrative claim error. A narrative claim error that goes uncaught becomes a public mistake.

**Asking Claude to verify its own work.** Self-verification is not independent verification. "Does this citation look right?" asked of the system that generated the citation is not a gate check. An independent source check is.

**Writing a reflection that describes what happened without transferring to future practice.** The two transfer questions (what workflow transfers, what workflow should not transfer) are not optional. They are the evidence that the capstone did what capstones are supposed to do: build transferable judgment, not just complete a task (Brown, Collins, and Duguid 1989; Perkins and Salomon 1992).

**Confusing AI disclosure with AI blame.** The AI-use log is a professional practice, not an admission of wrongdoing. Knowledge workers in research, education, consulting, and operations who document their AI use are practicing accountability, not confessing weakness.

---

## The Human Gate in the Capstone

Every chapter in this book returned to the same idea: the human gate is where the work becomes trustworthy. The capstone makes that concrete at each hand-off.

In chat, the gate is the task packet review and the scope clarification before you generate anything consequential.

In Cowork, the gate is the folder check before the tool touches files, the intermediate review before the draft inherits the matrix, and the final read before anything leaves your desk.

In Code, the gate is the diff review, the test run, and the decision about whether the change is acceptable — before the merge.

In all three surfaces, the irreversible action belongs to the human. Distributing, publishing, committing, sending, deleting — those are human decisions, human names, human responsibility.

The capstone asks you to hold all four gates under real conditions, on real work, once. After that, it is a habit.

---

## Try This

**Exercise 1 (full capstone):** Run the worked example with your own topic.
Choose a topic in your field. Produce a three-to-four-page briefing note for a non-specialist audience. Follow steps 1 through 6 exactly as described. Submit all seven bundle items. The reflection is required.

**Exercise 2 (routing audit):** Take a project you completed recently using Claude in any form.
Write a retroactive surface-routing map: what tasks did you run through Claude, and which surface? Were those the right assignments? Write a retroactive AI-use log: where did you run gate checks, and where did you skip them? What would you change?

**Exercise 3 (peer review):** Exchange routing maps with a colleague or classmate.
Review each other's routing decisions without looking at the final artifacts. For each task: does the surface assignment make sense? Is the verification path adequate? Would you route anything differently? Discuss the disagreements. The disagreements are where the learning is.

---

## What Would Change My Mind

The capstone is structured around process evidence because the field of GenAI assessment is moving toward process over product (arXiv 2508.11709). If research showed that process artifacts do not predict output quality or that learners game the log without improving their verification practice, the bundle design would need revision.

I also assume that three-surface routing is the right frame for most knowledge work projects. If most real projects cleanly belong to one surface, the multi-surface routing exercise would be over-engineered. The empirical case for three distinct surfaces rests on their genuinely different affordances — chat for conversation, Cowork for file-heavy automation, Code for codebase work. If those affordances converge significantly as the products evolve, the routing frame will need updating.

The capstone reflection format borrows from Schon's reflective practitioner model. If evidence showed that structured reflection prompts produce formulaic rather than genuine transfer, the format would need loosening.

---

## Still Puzzling

The capstone assumes that learners will generalize from one authentic project to future projects. Perkins and Salomon (1992) argued that transfer requires explicit bridging — hence the two transfer questions. But how much explicit bridging is enough, and for which learners, is not fully settled.

The question of appropriate automation is also open. The capstone rewards what the research notes call "appropriate automation, visible human judgment, and defensible final work." But defining "appropriate" remains context-dependent. A workflow that is appropriate for a public briefing note is probably not appropriate for a medical decision-support tool or a legal judgment. The boundary shifts with stakes, domain, reversibility, and expertise. This book has drawn that boundary conservatively. Readers who move into higher-stakes domains should redraw it.

Finally, the AI-use log as a professional practice is still emerging. Norms for disclosure, documentation, and accountability in AI-assisted knowledge work are being negotiated across research, journalism, education, law, and public policy. The log format in this chapter is a reasonable starting point, not a settled standard.

---

## Closing: The Last Human Gate

You opened this book at a polished artifact that might not be trustworthy. Chapters 1 through 9 gave you a set of tools for telling the difference: task diagnosis, surface selection, specification, context design, privacy discipline, verification, and workflow design.

This chapter asked you to use all of them, once, on something real.

That is the last human gate. Not the one at the end of a workflow step, not the one before you send an email. The one where you decide whether you trust your own process.

Claude is not one tool. It is an ecosystem. The capable user knows which surface belongs on each task, what to delegate, what to verify, and where human judgment must remain in charge. You have now done that work.

The question is what project is next.

---

## AI Wayback Machine

**Run this:**

```
Who was John Dewey, and how does his idea of learning-by-doing connect to a project-based capstone in an AI course? Keep it to three paragraphs. End with the single most surprising thing about his ideas or career.
```

Search "John Dewey" on Wikipedia to verify what Claude tells you.

**Now make the prompt better:**

- Ask Claude to describe what Dewey's version of a capstone project would look like if the tools were AI systems rather than carpentry benches.
- Ask what Dewey would say about a student who submitted a polished AI-generated project without a process log.

What changes? What improves? What does Claude get wrong or leave out?

---

## Sources Used

- Anthropic, "Understanding Claude's personalization features," Claude Help Center. https://support.claude.com/en/articles/10185728-understanding-claude-s-personalization-features [verify — current as of writing]
- Anthropic, "How can I create and manage projects?" Claude Help Center, March 16, 2026. https://support.claude.com/en/articles/9519177-how-can-i-create-and-manage-projects [verify — current as of writing]
- Anthropic, "Claude Code overview," Claude Code Docs. https://code.claude.com/docs [verify — current as of writing]
- Anthropic, "Configure permissions," Claude Code Docs. https://code.claude.com/docs/en/permissions [verify — current as of writing]
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Brown, John Seely, Allan Collins, and Paul Duguid. "Situated Cognition and the Culture of Learning." Educational Researcher, 1989. https://www.jstor.org/stable/1176008
- Collins, Allan, John Seely Brown, and Susan E. Newman. "Cognitive Apprenticeship: Teaching the Crafts of Reading, Writing, and Mathematics." 1989. https://apps.dtic.mil/sti/pdfs/ADA178530.pdf
- Perkins, David N., and Gavriel Salomon. "Transfer of Learning." International Encyclopedia of Education, 1992. https://jaymctighe.com/wp-content/uploads/2011/04/Transfer-of-Learning-Perkins-and-Salomon.pdf
- "Navigating the New Landscape: A Conceptual Model for Project-Based Assessment in the Age of GenAI." arXiv, 2025. https://arxiv.org/abs/2508.11709
