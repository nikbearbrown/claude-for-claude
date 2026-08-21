# Chapter 12 — Capstone: The Supervised Agentic Project

Three hours before a grant proposal deadline. A program officer at a small research nonprofit asks Cowork to pull together a summary of three recent funding announcements from a folder of PDFs and draft a one-page comparative brief. Cowork produces something that looks authoritative. It has a table. It has bullets. The language is confident. She attaches it to the proposal and submits.

Two days later, a colleague reads the brief. One of the funding announcements in the table does not match the actual source. The eligibility criteria are wrong — copied from an earlier version of the announcement that happened to be in the folder alongside the current one. The brief was polished, specific, and incorrect.

Nothing in the output announced the problem. The artifact looked like finished work. The error was in the gap between what the agent had access to and what the program officer assumed it would use.

This is the book's central lesson in its most concentrated form. The agent did exactly what it was capable of doing. The supervision — scoping the folder, verifying which version of each document was current, checking the table against the originals — was the part that did not happen.

This chapter provides a structure for making sure it does.

---

## Why the Final Artifact Is Not the Measure

GenAI assessment research has identified a consistent failure mode: evaluating agentic or AI-assisted work by the quality of the output alone (arXiv, 2025, "Navigating the New Landscape"). A polished deliverable can hide unverified claims that happen to sound plausible, data inputs that were out of scope or outdated, reasoning steps the agent compressed or skipped, actions the agent took that no human approved, and omissions the agent made silently.

Microsoft Research's study on generative AI and critical thinking (CHI 2025) documents the mechanism: self-reported reductions in cognitive effort when users over-rely on AI output. The check does not happen because the output seems authoritative. The opening scene is not unusual. It is the predictable result of that dynamic.

The capstone in this chapter treats workflow artifacts as the evidence of supervision. The final artifact matters — but only if the process behind it was sound. Anyone can get an agent to produce something plausible. The capability being practiced here is knowing whether it is trustworthy.

---

## The Ten Artifacts

A supervised agentic project produces ten artifacts. Together they answer: what was the task, what was the agent allowed to do, what happened, and how do I know the output can be trusted?

**Project brief.** What is the goal? What is the deliverable? Who is the audience? What would success look like, and what would failure look like? A brief that cannot answer these questions is not ready to delegate to an agent.

**Data boundary.** What inputs is the agent allowed to use? Name specific files, folders, or sources. Name what is explicitly excluded: credentials, PII, confidential data, outdated versions of documents. The program officer in the opening scene needed this artifact. She did not have it.

**Action surface map.** Which surface will you use — Claude AI, Claude Cowork, Claude Code? Which tools and connectors are enabled? What can the agent read, write, execute, or send? What is it explicitly blocked from? This is Chapter 3 applied to a specific project.

**Agent plan.** Before the agent acts, require a stated plan: what steps it will take, what tools it will use, in what order, and what it will produce. Review the plan for missing steps, bad order, or overreach before approving any action. This is Chapter 7 applied.

**Approval gates.** Where will the human decide before the agent continues? At minimum: before any external-facing action, before any irreversible change, before any output leaves the defined scope. Approval gates are pre-specified, not improvised in the moment.

**Failure pre-mortem.** Before execution, list the three to five most likely ways this delegation could fail. Consider: stale data, fabricated details, scope creep, irreversible actions, misunderstood instructions, silent omissions. Writing one before execution is the habit that makes failure prevention possible. This is Chapter 9 applied.

**Verification evidence.** What evidence will you collect to know the output is trustworthy? For a literature summary: source checks against originals. For code: tests that pass before merge. For a data table: row counts and formula audits against the source. Verification evidence is defined before the agent acts, not after the output arrives. This is Chapter 8 applied.

**Final artifact.** The deliverable — the report, the code, the summary, the analysis. What the agent produced and the human approved for use after verification.

**Audit note.** A short written record of what happened: what you delegated, what you did not delegate, what tools and permissions were used, what went wrong or changed, what evidence you checked, what you accepted or revised, and what you would do differently next time. The audit note is not optional. It is the document that turns agentic work into organizational knowledge rather than invisible automation.

**Transfer reflection.** What did this project teach you about supervised delegation? What would you set up differently? What failure mode appeared that you did not anticipate? Following Perkins and Salomon (1992), the transfer reflection explicitly connects what you learned in this project to how you will approach the next one. "The agent made a synthesis error" is description. "Next time I will include 'flag disagreements' in the plan request" is transfer.

<!-- → [TABLE: Capstone Packet — two columns: Artifact | Purpose — all ten rows with a Done? checkbox column] -->

---

## A Worked Example: Research Brief

The project: summarize three recent peer-reviewed papers on AI governance published in the last two years, comparing their recommendations for organizational oversight.

**Project brief.** Goal: a three-page comparative brief. Deliverable: a structured document with source citations. Audience: a team making policy decisions. Success: accurate, cited, clearly comparative. Failure: uncited claims, outdated papers, fabricated details, missing differences between the papers' positions.

**Data boundary.** Inputs: three specific PDFs downloaded from verified journal sources, stored in a controlled folder. Excluded: preprints that have not undergone peer review, papers outside the specified date range, any web search by the agent. The folder contains exactly three files. Nothing else.

**Action surface map.** Surface: Claude Cowork. Connector: the specific research folder only. Tools: document reading and text generation. No web search connector. No email connector. No file-write access outside the draft folder. [verify — current as of writing]

**Agent plan.** Request a plan before execution. Expected: read each paper in sequence, extract governance recommendations, compare across papers, draft the brief with citations. Review the plan: does it include source verification steps? Does it treat the three files as the only inputs? If the plan mentions searching for additional sources, reject and revise before proceeding.

**Approval gates.** Gate 1: plan reviewed and approved before any reading begins. Gate 2: draft reviewed against the source PDFs before finalization. Gate 3: citations verified against originals before the brief leaves the folder.

**Failure pre-mortem.**
- Most likely: the agent synthesizes claims not directly supported by the specific papers.
- Second: the brief omits one paper's central disagreement with the others.
- Third: citations are formatted plausibly but do not match actual page locations in the originals.
- Fourth: the agent offers "relevant context" from its training data rather than sticking to the three sources.

**Verification evidence.** Check each citation against the original PDF. Check that each paper's primary recommendation is represented without distortion. Check that differences between papers appear as differences, not smoothed into consensus. Record what was checked and what was found.

**Final artifact.** A three-page brief with accurate citations, comparative structure, and a human-verified claim set.

**Audit note (abbreviated).**

*What I delegated:* Initial extraction and comparative structuring from three source PDFs.

*What I did not delegate:* Final claim verification, judgment about which disagreements mattered, framing the brief for its audience.

*Tools/permissions used:* Cowork with read access to the research folder. No web access.

*What went wrong:* The draft treated one paper's conclusion as if it were shared by all three. Found during Gate 2 review. Revised before approval.

*Evidence checked:* All three citations against source PDFs. The divergent paper's conclusion verified against the full conclusion section.

*What I accepted, rejected, or revised:* Accepted the comparative structure. Revised the characterization of Paper 2's position. Rejected one sentence that was ambiguous about whose view it represented.

*What I would do differently:* In the plan request, explicitly ask the agent to flag where papers disagree rather than only summarizing where they agree.

**Transfer reflection.** The failure pre-mortem predicted synthesis errors. It was right. The verification step caught the problem. Next time I will add to the plan request: "explicitly identify where these papers take different positions." The audit note goes into the team shared folder so the next person running a similar brief has a model to follow.

---

## Tracks

The capstone structure works across different access levels. The ten artifacts are the same regardless of track. The tools change; the supervision does not.

**Chat-only.** Use Claude AI to work through the capstone structure in conversation. Produce all ten artifacts as text documents. This track practices the thinking without requiring Cowork or Code access.

**Cowork workflow.** Choose a file-heavy project: report assembly, document comparison, data extraction, folder summarization. Use Cowork with a tightly defined source folder. Produce the action surface map and approval gates as explicit Cowork configuration. [verify — current as of writing]

**Claude Code workflow.** Choose a project involving a codebase: a failing test, a feature request, a documentation gap. Use Claude Code on a non-production branch. Require tests before any merge. Produce verification evidence as test results and diff review. [verify — current as of writing]

**MCP capability review.** Choose a project that evaluates whether to add a new MCP server to your workflow. The deliverable is not a file — it is a governance recommendation: approve, restrict, or reject, with reasoning. Produce the action surface map as a tool-by-tool permission analysis.

---

## The Closing Argument

You started this book with the observation that a polished artifact announces nothing about whether the work behind it was sound. An agent can produce fluent text, passing code, formatted tables, and plausible-sounding citations. None of that is evidence that the output is correct, appropriately scoped, or worth acting on.

The book's argument has been consistent: agentic AI is delegated action under constraints, and the capable human is the one who defines the constraint before the agent acts, reviews the plan before the agent moves, supervises execution through defined gates, verifies with evidence rather than impression, and takes responsibility for the final decision.

These are not limitations on what agents can do. They are the practices that make agent-assisted work trustworthy.

The audit note is the last artifact in the capstone packet. It is also the first document for the next project. What you learned from this delegation goes into the next one's failure pre-mortem. What your team learned goes into the next policy review. The supervised agentic project is not a one-time assignment. It is a practice you are beginning.

Begin it with the same question the introduction asked: not whether the output is impressive, but what would have to be true for it to be trusted.

---

*LLM Exercise: You are designing a supervised agentic project for the following task: "Use an agent to compile a competitive landscape analysis from ten recent industry reports stored in a shared folder." Produce the first five artifacts — project brief, data boundary, action surface map, agent plan review criteria, and failure pre-mortem — as if you were preparing to run this project. Be specific enough that a colleague could use your artifacts to run the same project without asking you questions.*

---

## Sources

- Perkins, D. N. and Salomon, G. "Transfer of Learning." 1992. https://jaymctighe.com/wp-content/uploads/2011/04/Transfer-of-Learning-Perkins-and-Salomon.pdf
- "Navigating the New Landscape: A Conceptual Model for Project-Based Assessment in the Age of GenAI." arXiv, 2025. https://arxiv.org/abs/2508.11709
- NIST. *Artificial Intelligence Risk Management Framework (AI RMF 1.0)*. 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
- Anthropic. "Get started with Claude Cowork." Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork
- Anthropic. "Use Claude Cowork safely." Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely
- Anthropic. "Claude Code overview." Claude Code Docs. https://code.claude.com/docs
- Microsoft Research. "The Impact of Generative AI on Critical Thinking." CHI 2025. https://www.microsoft.com/en-us/research/publication/the-impact-of-generative-ai-on-critical-thinking-self-reported-reductions-in-cognitive-effort-and-confidence-effects-from-a-survey-of-knowledge-workers/

---

*Tags: #claude #agentic #ai #capstone #supervision #verification #audit #delegation #Medhavy*
