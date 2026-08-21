# Chapter 12 — Capstone: The Weekly Operations Packet

## TL;DR

- The capstone is an end-to-end Cowork workflow: gather source files, prepare the workspace, write the brief, review the plan, generate the packet, verify it, write the audit note, and build the workflow card.
- Every step requires a human decision, not just a human presence.
- The output you deliver is the packet. The evidence you keep is the brief, the plan, the verification checklist, the audit note, and the workflow card.
- The test of the capstone is not whether the packet looks good — it is whether you can explain every line of it, trace every number to its source, and hand it to someone who was not in the room.

---

## Opening Scene

It is Friday afternoon. Your operations director needs the weekly packet by end of day: status on each active project, a metrics table, a list of action items with owners and due dates, an open-questions section, and a risk flag if anything needs escalation. You have a folder of team notes from the week, a metrics CSV from the database export, and forty-five minutes.

In earlier weeks, this took two hours. You would open each note file, pull out the relevant sentences, paste them into a template, format the table, check the action items, and write a cover summary. The time was mostly mechanical — moving text from one place to another — with maybe fifteen minutes of actual judgment: deciding what counted as an escalation risk, catching the date that was off by a week, noticing that one project's status note was missing.

With Cowork, the mechanical work takes minutes. The judgment work is still yours. That shift is what this capstone is about.

This chapter walks you through the full workflow — not as a list of features, but as a sequence of human decisions backed by Cowork execution.

---

## What This Chapter Lets You Do

By the end of this chapter you can:

- Run a complete, end-to-end Cowork workflow from source files to verified output.
- Distinguish the execution steps (what Cowork does) from the judgment steps (what you decide).
- Produce a verified weekly operations packet with full source traceability.
- Write an audit note documenting what Cowork did, what you approved, what you verified, and what remains uncertain.
- Turn the completed workflow into a reusable card for the next run.

---

## The Capstone in Context

This capstone is designed to be an authentic task, not an abstract exercise. Brown, Collins, and Duguid (1989) argued that learning situated in real work produces knowledge that transfers to new contexts; exercises designed to look like work but avoid its constraints transfer much less reliably. Whether you use a real source packet or the synthetic one provided below, the workflow is the same. The skills are the same. The judgment calls are the same.

Collins, Brown, and Newman (1989) describe cognitive apprenticeship as a process of modeling, scaffolding, articulation, and reflection. This chapter models the complete workflow, scaffolds each step with explicit decisions, asks you to articulate your choices in the audit note, and closes with a transfer reflection. The goal is not to produce one weekly packet — it is to understand the workflow well enough to adapt it to your next non-weekly, non-packet, non-operations task.

---

## Source Material: Real or Synthetic

### Option A: Use real source materials

If you have a current week's materials available in a format that does not contain sensitive, regulated, or confidential information, you can use them. Before doing so, complete the following check:

- [ ] The source files contain no personally identifiable information that has not been approved for AI-assisted processing.
- [ ] The source files contain no regulated data (health, legal, financial, personnel) unless your organization has approved this workflow for that category.
- [ ] You have copied the files to a dedicated working folder and are not working in the live shared drive.
- [ ] You have a backup of the originals.

If any item is unchecked, use Option B.

### Option B: Use the synthetic packet

The following synthetic source materials represent a small nonprofit's operations week. They are designed to be realistic without containing actual sensitive data.

**Synthetic source file 1: weekly-notes.md**

```
Project: Community Garden Expansion
Status: On track. Site plan approved by city. Next step: contractor meeting Tuesday.
Action: Malia to schedule contractor meeting by Monday.
Open question: Will the city require a second permit for the irrigation system?

Project: Youth Tutoring Program
Status: Delayed. Volunteer coordinator position still open. Classes running at 60% capacity.
Action: Priya to post updated job listing by Wednesday.
Risk: Capacity may drop further if we reach week 3 without a coordinator.

Project: Annual Gala Planning
Status: Ahead of schedule. Venue confirmed. Catering quotes received.
Action: Devon to compare catering quotes and recommend by Friday.
Action: Malia to send save-the-date to board list this week. (Completed.)
Open question: Should we add a silent auction component? Needs board input.
```

**Synthetic source file 2: metrics-export.csv**

```
Project,Budget Spent,Budget Total,Volunteers This Week,Tasks Completed,Tasks Open
Community Garden Expansion,12400,18000,4,3,5
Youth Tutoring Program,8750,14000,11,2,8
Annual Gala Planning,3200,9500,2,4,2
```

**Synthetic source file 3: meeting-notes.md**

```
Weekly Operations Call — Friday
Present: Malia, Priya, Devon, Director

Decisions:
1. Board will be consulted on silent auction question before gala planning meeting next week.
2. Volunteer coordinator job listing to go live this week at current salary band.

Action items confirmed in meeting:
- Priya: job listing live by Wednesday EOD.
- Devon: catering recommendation by Friday EOD.
- Malia: contractor meeting scheduled by Monday EOD. (Board silent auction question forwarded to Director.)

Director noted: Youth Tutoring capacity risk should appear on next week's packet as an escalation flag.
```

---

## The Full Workflow, Step by Step

### Step 1: Prepare the workspace

Create a dedicated folder for this workflow run. Copy the source files into it. Do not work in the shared drive or the live notes folder.

**Workspace boundary note** (write this now, before touching Cowork):

> "This workflow run uses three source files: weekly-notes.md, metrics-export.csv, and meeting-notes.md. All three are copies in /Operations/capstone-run-[date]/. The originals remain in /Operations/weekly-notes/. No other files or folders are in scope. The output will be saved to /Operations/weekly-packets/."

The workspace boundary note does not go to Cowork. It goes to you — and to anyone who needs to audit this run later (Anthropic, "Use Claude Cowork safely").

### Step 2: Write the task brief

Write the brief before opening Cowork. The brief should not be improvised in the prompt field.

**Sample brief for the synthetic packet:**

> "Using only the three files in /Operations/capstone-run-[date]/ — weekly-notes.md, metrics-export.csv, and meeting-notes.md — produce a weekly operations packet with the following sections:
>
> 1. Executive summary (two to three sentences, status overview only)
> 2. Key project updates (one sentence per project, from weekly-notes.md)
> 3. Metrics table (all rows from metrics-export.csv, no modifications to figures)
> 4. Decisions made (from meeting-notes.md only — do not infer)
> 5. Action items (from meeting-notes.md only, with owner and due date as stated — do not infer)
> 6. Risks and escalation flags (from the notes only — do not evaluate or add your own)
> 7. Open questions (from the notes only — do not generate additional questions)
> 8. Source notes (list the three source files with their dates)
>
> Do not access any file outside this folder. Do not infer action items, decisions, or open questions. Stop and flag if you find a file that appears to contain personnel or HR content. Output: weekly-packet-[date].md"

The brief is explicit about sources, exclusions, and inference rules. It tells Cowork what to stop on. It names the output file.

### Step 3: Open Cowork and submit the brief

Open Cowork [verify — current as of writing]. Grant access to the working folder only — not the parent folder, not the shared drive. If Cowork asks to access additional sources, decline and note it in your audit log.

Paste the brief. Submit it.

### Step 4: Review Cowork's plan before approving

Cowork will typically produce a plan before acting — a list of proposed steps. Review it against the following checklist (Anthropic, "Plans, Approvals, and Redirection"):

**Plan review checklist:**
- [ ] The plan lists only the three specified source files. No other folders or files appear.
- [ ] The plan proposes no external searches, browser access, or connector use beyond what was specified.
- [ ] The plan describes each output section and matches the brief.
- [ ] No step involves inferring or generating content not found in the source files.
- [ ] The plan includes a step to produce the output file with the correct naming convention.

If any item fails, redirect Cowork before approving. Do not approve a plan that reaches outside the specified workspace (Anthropic, "Assign tasks from anywhere in Claude Cowork" [verify — current as of writing]).

### Step 5: Let Cowork run the task

Once you approve the plan, Cowork executes. Your job during execution is to watch for unexpected behavior — access requests, clarifying questions, or stops — and respond to them. Do not step away.

### Step 6: Verify the output

The packet is not done when Cowork produces it. The packet is done when you have worked through the verification checklist.

**Verification checklist for the weekly operations packet:**

- [ ] Executive summary: every claim is traceable to one of the three source files.
- [ ] Key project updates: one update per project; no project is missing; no project appears that is not in the source files.
- [ ] Metrics table: row count matches the CSV (three rows). Every figure matches the CSV exactly — spot-check all numbers.
- [ ] Decisions made: every decision appears in meeting-notes.md. No decision is paraphrased in a way that changes its meaning. No decision was inferred.
- [ ] Action items: every action item has an owner who appears in the meeting notes. Every due date is stated explicitly in the notes — no due date was inferred. Count: the meeting notes contain four action items; the packet should contain four.
- [ ] Risks and escalation flags: the Youth Tutoring capacity risk appears as specified by the Director. No additional risks were added by Cowork.
- [ ] Open questions: both open questions from the source notes appear. No question was generated by Cowork.
- [ ] Source notes: all three source files are listed.
- [ ] No file content from outside the working folder appears in the packet.
- [ ] No personnel or HR material appears in the packet.

For the synthetic packet, this verification takes about ten minutes. For a real packet, it may take longer. The time is not wasted — it is the work (Microsoft Research, "The Impact of Generative AI on Critical Thinking," CHI 2025).

### Step 7: Correct what the verification finds

Mark what requires correction. Make the corrections yourself — do not ask Cowork to self-correct without reviewing the result of that correction against the same checklist. Each correction is a human decision about what the packet should say.

Note every correction in your audit log. A packet with three corrections is not a failure — it is evidence that verification worked.

### Step 8: Write the audit note

The audit note is written by you, not by Cowork. It is the human record of what happened during this run.

**Audit note template:**

```
Audit Note — Weekly Operations Packet — [Date]

What Cowork used:
  Source files: [list all three, with dates]
  Folder accessed: [exact path]
  No external sources accessed.

What Cowork created:
  weekly-packet-[date].md

What Cowork did not do:
  [Note any brief instructions Cowork did not follow, or areas where it stopped 
   and flagged rather than acting]

What I approved:
  [The plan, after confirming it listed only the three source files and 
   proposed no external access]

What I verified:
  [List each item from the verification checklist that you confirmed]

What required manual correction:
  [List each correction, or "None"]

What was uncertain:
  [Any item you could not fully verify, or any judgment call you made]

What should change before next run:
  [Any update to the task brief, folder structure, or workflow card]
```

The audit note goes into the working folder alongside the packet and source files. If the packet is questioned later — by your director, your organization, an auditor — the audit note is your evidence that a human was in the loop and did specific things (NIST AI RMF 1.0, 2023).

---

## What You Are Responsible For

The packet carries your name. That is not a formality.

You are responsible for:

- Every number in the metrics table being accurate as of the source file you used.
- Every action item having the right owner and a stated (not inferred) due date.
- Every escalation flag being a real risk, not a Cowork interpretation of what a risk might be.
- The packet not containing any content from outside the approved source files.
- Knowing what Cowork did and did not do.

Cowork assembled the packet. You are accountable for it.

This is not a burden added by this book. It is the condition under which the packet is worth anything. An output no one is accountable for is not an output — it is a liability (Anthropic, "Use Claude Cowork safely").

---

## Build the Workflow Card

After the audit note is complete, build the workflow card for this workflow using the template from Chapter 11. Fill in every field. The first time you fill it in, the "last reviewed" date is today and the "revision notes" field says "initial version."

The card is the difference between a capstone you did once and a workflow you can run every week.

Store the card in the working folder or in a workflow-cards folder your team can access. If someone else runs this workflow next Friday, they should be able to do it from the card without asking you.

---

## Transfer: This Workflow Is Not Just This Workflow

Perkins and Salomon (1992) distinguish near transfer (applying a skill to a nearly identical task) from far transfer (applying the underlying structure to a different domain). The goal of this capstone is far transfer.

The sequence — workspace boundary, task brief, plan review, execution, verification checklist, audit note, workflow card — applies to:

- Monthly invoice extraction
- Research digest assembly
- Meeting notes and action logs
- File reorganization runs
- Team handoff documents
- Grant report drafts from program notes

Every Cowork workflow that is worth running once is worth building a card for. Every card that is worth filling in once is worth updating after each run.

The weekly operations packet is a vehicle. The method is what you keep.

---

## What Not to Automate

This capstone has been deliberately supervised: you write the brief, you review the plan, you work through the checklist, you write the audit note. Chapter 10 covered the full scope of what not to delegate. A few reminders specific to operations workflows:

**Do not automate the audit note.** Asking Cowork to write its own audit note defeats the purpose. The audit note is your account of what happened, written from your review — not Cowork's self-report.

**Do not schedule this workflow without a human-in-the-loop trigger.** Scheduled Cowork tasks [verify — current as of writing] can run without a plan review unless you design a review step into the workflow. If the weekly packet runs automatically on Friday morning, who reviews it before it goes out? That person must be named in the workflow card, and the card must include a "do not deliver if not reviewed" rule.

**Do not let a clean packet lower your guard.** A packet that looks good can still be wrong. The checklist is not a formality for runs when something seems off — it is the standard for every run. The Microsoft Research survey of knowledge workers found that generative AI use is associated with self-reported reductions in cognitive effort and critical thinking (Microsoft Research, "The Impact of Generative AI on Critical Thinking," CHI 2025). The checklist is the structural counter to that reduction.

---

## Common Mistakes

**Treating the capstone as a test of Cowork's output quality.** The capstone tests your ability to design, supervise, verify, and document a complete workflow — not whether Cowork writes a good executive summary.

**Skipping the workspace boundary note.** The boundary note is the first human decision. Without it, everything that follows is on shakier ground.

**Verifying from memory rather than from source.** "I think the Youth Tutoring budget is right" is not verification. Open the CSV. Count the rows. Match the number.

**Writing the audit note from Cowork's output.** The audit note describes what you did, not what the packet contains.

**Calling the capstone done when the packet is delivered.** The capstone is done when the workflow card is complete and the working folder contains the packet, the source files, the brief, and the audit note.

---

## Try This

### Exercise 1: Run the full workflow (hands-on)

Use the synthetic source material from this chapter. Follow every step — workspace boundary note, task brief, plan review, execution, verification checklist, audit note, workflow card. Time yourself. After you finish, ask: which step took the most judgment? Which step would be easiest to skip under time pressure? Write one paragraph about why that step is the one most worth protecting.

### Exercise 2: Find the errors

Before running the full workflow, try to identify at least three ways the synthetic source material could cause a problem if it were not verified. Write them down before you see Cowork's output. Compare your list to what the verification checklist actually catches.

### Exercise 3: Adapt the workflow to your context

Take the workflow card you build in this capstone and adapt it to a recurring task in your own work. Change the input folder, the source file types, the output sections, the verification items, and the stop conditions. Keep the structure. What stays the same? What has to change?

---

## What Would Change My Mind

The capstone argues that full human oversight — plan review, verification checklist, audit note, workflow card — is necessary for every run of a recurring workflow. If future practice showed that well-designed Cowork projects with strong initial setup reliably produce verifiable outputs with much lower error rates, a lighter verification protocol might be defensible. The argument here is not that the checklist length is fixed — it is that verification is not optional. What specific items to check, and how intensively, should be calibrated to the risk and the track record of the workflow.

If organizations develop shared infrastructure — audit logs embedded in Cowork projects [verify — current as of writing], automatic flagging of out-of-scope file access, version-controlled workflow cards — some of what this chapter asks humans to do manually could be handled structurally. Until that infrastructure exists, the manual record is the only record.

---

## Still Puzzling

The right balance between authenticity and safety in capstone design is not settled. Using real materials teaches real judgment but risks exposing sensitive data. Synthetic materials are safe but may not transfer as readily. Some readers will have access to materials that are genuinely safe to use; others will not. The synthetic packet in this chapter is a pragmatic answer, not a final one.

Whether the audit note belongs to the individual practitioner or to the organization is also unsettled. In regulated industries, audit documentation is an organizational record. In individual practice, the audit note is primarily a learning and quality tool. The chapter treats them as the same document, but the governance implications are different.

---

## Closing

You started this book with a polished artifact. The central question was whether it was trustworthy.

The weekly operations packet you just built is polished. You know whether it is trustworthy, because you made the decisions that made it so: you named the sources, scoped the workspace, reviewed the plan, checked every action item against the meeting notes, wrote the audit note in your own words, and built a card that will let you run this workflow again next Friday.

That is not the tool's accomplishment. It is yours.

The method in this book — workspace boundary, task brief, plan review, verification, audit note, workflow card — will not stay still. The tools will change. The products will update. The connectors that exist today may be retired or renamed. The features flagged throughout this book as [verify — current as of writing] will need checking. That is expected.

What does not change is the structure of the problem: Cowork can execute. You keep the gate. The work of execution is not nothing — it is genuinely useful, genuinely faster, and genuinely worth learning to use well. The work of keeping the gate is not overhead — it is how the output earns its right to leave the workspace.

Go back to the polished artifact. Ask what would have to be true for it to be trusted. You know how to answer that now.

---

## AI Wayback Machine

**Donald Schön** (1930–1997) was an organizational theorist and educator who argued that professional knowledge is not primarily transmitted in classrooms — it is built through what he called "reflection-in-action": the capacity to think about what you are doing while you are doing it, and to revise your practice based on what you notice.

Schön was skeptical of the idea that professional work could be reduced to technical application of established knowledge. The most important competence, he argued, was knowing how to learn from a specific situation — including from what went wrong, what surprised you, and what you would do differently.

The audit note in this chapter is a reflection-in-action document. It does not report the output. It records what you noticed, what you corrected, and what you would change. Without it, the run teaches you nothing you can carry forward.

**Run this:**

```
Who was Donald Schön, and what did he mean by "reflection-in-action"? 
Keep it to three paragraphs. End with the single most surprising thing 
about his ideas or their influence on professional education.
```

Search **"Donald Schön"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask about the difference between technical rationality and reflective practice, and which one describes what the audit note requires.
- Ask how Schön's "swampy lowland" problem — messy, indeterminate, complex situations — applies to AI-assisted knowledge work.

What changes? What gets better? What gets worse?

---

## Sources Used

- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork
- Anthropic, "Organize your tasks with projects in Claude Cowork," Claude Help Center, 2026. https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-cowork [verify — current as of writing]
- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely
- Anthropic, "Assign tasks from anywhere in Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13947068-assign-tasks-to-claude-from-anywhere-in-cowork [verify — current as of writing]
- Brown, J. S., Collins, A., and Duguid, P., "Situated Cognition and the Culture of Learning," 1989. https://www.jstor.org/stable/1176008
- Collins, A., Brown, J. S., and Newman, S. E., "Cognitive Apprenticeship," 1989. https://apps.dtic.mil/sti/pdfs/ADA178530.pdf
- Perkins, D. N. and Salomon, G., "Transfer of Learning," 1992. https://jaymctighe.com/wp-content/uploads/2011/04/Transfer-of-Learning-Perkins-and-Salomon.pdf
- "Navigating the New Landscape: A Conceptual Model for Project-Based Assessment in the Age of GenAI," arXiv, 2025. https://arxiv.org/abs/2508.11709
- Microsoft Research, "The Impact of Generative AI on Critical Thinking," CHI 2025. https://www.microsoft.com/en-us/research/publication/the-impact-of-generative-ai-on-critical-thinking-self-reported-reductions-in-cognitive-effort-and-confidence-effects-from-a-survey-of-knowledge-workers/
- NIST AI RMF 1.0, 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
