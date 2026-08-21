# Chapter 10 — What Not to Delegate

## TL;DR

- Some tasks must not be delegated to Cowork regardless of how the brief is written or how capable the tool appears.
- The categories: regulated data, sensitive personal records, personnel and legal judgment, irreversible actions, external commitments, and ambiguous or untrusted source material.
- The default posture when uncertain: refuse or redesign before starting, not after the output arrives.
- The decision rule: if the task involves sensitive data, regulated information, someone else's personal records, an irreversible action, or a judgment about a person — stop and redesign.

---

## Opening Scene: The Tempting Request

You are a university staff member. A colleague sends you a folder of student accommodation files — disability documentation, medical letters, academic adjustment notes for thirty students. She says: "I need to categorize these by risk level so the advisor team knows who to prioritize. Can Cowork do this?"

The question sounds reasonable. The folder is sitting right there. Cowork can read PDFs. You know roughly what categories mean. The task would take three hours manually.

Stop.

The request asks Cowork to process identifiable student disability records, make risk categorizations about individuals, and produce output that will shape advising priorities. All three of those moves require institutional policy, approved systems, and human professional judgment. None of them belong in a personal Cowork session.

The right answer is not a better prompt. It is refusal, followed by a redesign conversation: which parts of this task can be done safely, with what tools, with what governance?

This chapter is about developing the discipline to stop before the folder is opened.

---

## What This Chapter Lets You Do

By the end of this chapter, you will be able to:

- Name the categories of tasks that must not be delegated to personal Cowork.
- Apply a concrete decision rule before starting a Cowork task.
- Distinguish "Cowork can do this" from "Cowork should do this."
- Redesign a problematic task into a safer form — or escalate it to the right channel.
- Recognize when "human review at the end" is not enough protection.

---

## Core Concept: The Boundary of Supervised Delegation

Every previous chapter in this book has been about how to delegate well: how to prepare a workspace, how to write a brief, how to review a plan, how to check the output. This chapter argues that the discipline is incomplete without its complementary skill — knowing when not to delegate at all.

Anthropic's guidance for Cowork is direct: sensitive data, sensitive apps, irreversible actions, and consequential judgment require extra care or a different approach entirely (Anthropic, "Use Claude Cowork safely") [verify — current as of writing]. That guidance does not say "use caution"; it says some things should not enter the workflow.

The reason is structural. An agentic tool that can act on your files and connected services carries a different risk profile than a chat session. A bad answer in a chat session is a bad answer. A bad output from a Cowork session involving sensitive data may expose that data, produce a flawed categorization of a person, or take an action that cannot be undone. The OWASP Top 10 for LLM Applications names excessive agency, sensitive information disclosure, and overreliance as primary risk categories for agentic AI tools (OWASP, "Top 10 for LLM Applications 2025"). This chapter addresses all three.

The systems-safety literature has a useful concept here: control constraints. Nancy Leveson's work on system safety argues that dangerous systems need explicit stop conditions — defined circumstances where the system must pause and return control to a human (her framework applies here by analogy). In Cowork, nondelegation is a stop condition. It is not a failure of productivity; it is a feature of responsible design.

---

## The Nondelegation Categories

### Category 1: Regulated Health Data

Health records, medical documentation, treatment notes, insurance information, and anything that constitutes Protected Health Information (PHI) under U.S. law fall under HIPAA's Privacy Rule (HHS, "The HIPAA Privacy Rule"). Regulated clinical data belongs in approved clinical systems, not personal Cowork sessions.

The rule is not "be careful with health data." The rule is: personal Cowork is not an approved system for PHI. If your organization has an approved enterprise AI workflow for health information, use that, with the governance it requires.

**Redesign if possible:** Cowork can help you organize public policy documents, draft general process checklists from non-identifiable sources, or structure publicly available regulatory text. It cannot process patient records.

### Category 2: Student Records

Private student data — grades, accommodations, disciplinary records, financial aid, or anything covered by FERPA — requires institutional policy review and approved systems (U.S. Department of Education, "Protecting Student Privacy").

The accommodation folder scenario from the opening scene is this category. Categorizing students by "risk level" is not a formatting task; it is a professional judgment about individuals that carries legal and ethical weight. Cowork does not have the context, the credentials, or the accountability to make that judgment.

**Redesign if possible:** Cowork can help you draft a template, organize public policy text, or structure a process document using synthetic or fictional examples. It cannot process identifiable student records.

### Category 3: Personnel Decisions

Hiring, firing, performance review ratings, promotion decisions, discipline, or compensation adjustments are human decisions with legal, ethical, and organizational accountability attached. Cowork should not make these decisions or produce outputs that effectively make them.

The FTC has been explicit that AI tools must uphold confidentiality commitments and that automated decisions about individuals require particular care (FTC, "AI Companies: Uphold Your Privacy and Confidentiality Commitments," 2024). Personnel decisions are precisely where automated recommendations have real consequences for real people.

**Redesign if possible:** If a manager has written a policy-compliant performance review and wants help formatting it for clarity, that may be acceptable — check your organization's policy. But the assessment, the rating, and the decision must come from a qualified human.

### Category 4: Legal, Medical, and Financial Judgment

Cowork can help you organize a checklist of questions to bring to a lawyer. It cannot tell you whether a contract is binding. It can help you locate public information about a medication. It cannot advise you on treatment. It can organize receipts into categories. It cannot prepare a tax filing that carries professional or legal liability.

The distinction the NIST Privacy Framework draws between information management and consequential judgment is useful here (NIST Privacy Framework). Cowork belongs on the information-management side of that line. Legal, medical, and financial advice belongs with qualified professionals and approved systems.

**Redesign if possible:** Use Cowork to organize, to identify the questions you need to ask a professional, to structure public information. Use the professional to provide the judgment.

### Category 5: Irreversible File Operations

Deleting files, emptying folders, overwriting originals, sending files externally, submitting forms, completing purchases, or making account changes are final actions. Cowork can identify candidates for deletion, draft a submission, or prepare a purchase request. The human takes the final action — with a backup in place and eyes open (Anthropic, "Use Claude Cowork safely") [verify — current as of writing].

This is not about Cowork's reliability. It is about maintaining a human step before any action that cannot be undone. The research on visual prompt injection attacks for computer-use agents demonstrates that agentic AI can be manipulated by content it encounters in the browser or in documents — making autonomous irreversible actions higher risk than they appear (VPI-Bench, arXiv, 2025/2026).

**Redesign for all irreversible actions:** Draft/propose mode only. Cowork plans or identifies; human executes.

### Category 6: External Commitments

Sending an email to a client, posting to a public account, submitting a report to a funder, notifying a regulator — these are commitments made on behalf of you or your organization. Cowork can draft the email. You send it after reviewing accuracy, tone, confidentiality, and the commitments it makes.

The rule: Cowork drafts. Humans send. No exceptions for speed.

### Category 7: Ambiguous or Untrusted Source Material

If your source files are contradictory, incomplete, poorly scanned, or of uncertain provenance, Cowork should not resolve that ambiguity by inference. The correct output for ambiguous source material is an exception log — a list of items where the data was unclear — not a best guess.

This matters most for extracted data (receipts, forms, handwritten notes), where a plausible-looking wrong value is worse than a flagged gap. It also matters for any task where the source could be adversarial: documents from unknown external parties, web content from unreliable sites, or files whose integrity you cannot verify.

**Redesign:** Require Cowork to log uncertainty rather than resolve it. Treat exception logs as primary outputs, not failure states.

---

## The Decision Rule

Before starting any Cowork task, run it through this checklist:

**Stop and redesign if the task involves:**
- Identifiable health information about a person
- Student records or educational privacy-protected data
- Personnel evaluation, discipline, or promotion
- Legal advice, medical advice, or financial judgment
- Any action that cannot be undone without backup and human approval
- An external commitment sent in your name or your organization's name
- Source material that is ambiguous, untrusted, or of unknown provenance
- Credentials, passwords, API keys, or authentication information

**If any item checks yes:**
1. Do not proceed with the original task.
2. Identify which of the three redesign moves applies (see below).
3. If redesign is not possible, escalate to your organization's policy or a qualified professional.

---

## Three Redesign Moves

When a task hits a stop condition, do not abandon the work — redesign it.

**Move 1: Reduce the data.** Work with redacted, aggregated, anonymized, or synthetic versions of the source material. Instead of actual student records, use invented examples that have the same structure. Instead of real client contracts, use a generic template. Cowork can work effectively with stand-in data; it does not need the originals.

**Move 2: Reduce the action.** Shift from execute to draft. Instead of "summarize and send," write "draft a summary for my review." Instead of "delete files matching these criteria," write "identify candidate files for deletion in a list I will review." Cowork produces proposals; the human approves and acts.

**Move 3: Increase governance.** Some tasks genuinely require an approved enterprise AI workflow, a qualified professional, or explicit organizational policy sign-off. If a task cannot be made safe through data reduction or action reduction, it belongs in that channel. Using the wrong tool is not a workaround; it is a liability.

---

## The Nondelegation Table

| Task type | Default posture | Safer redesign |
|---|---|---|
| Protected health data | Do not use personal Cowork | Approved clinical workflow |
| Student records | Do not use personal Cowork | Aggregated or synthetic examples if policy allows |
| Personnel decisions | Human-only judgment | Formatting support on manager-written, policy-compliant text only |
| Legal / financial / medical advice | Human professional | Organize public checklist; identify questions for expert |
| Deletion / submission / purchase / send | Human-only final action | Cowork drafts proposal; human reviews and acts |
| Credentials / passwords / API keys | Never share | Use placeholder text |
| Ambiguous or untrusted sources | Do not guess | Exception log; flag for human review |

---

## Why "Human Review at the End" Is Not Enough

The most common rationalization for proceeding with a problematic task is: "I will check the output before I do anything with it."

This reasoning fails for at least three reasons.

First, data exposure happens when the information enters the session, not when the output is shared. Feeding a student's disability documentation into Cowork has already occurred before any output exists (Anthropic Privacy Center, "I would like to input sensitive data into my chats with Claude") [verify — current as of writing]. Reviewing the summary does not undo that.

Second, human review is less effective after the fact for consequential judgments. If Cowork has categorized thirty students by "risk level," a reviewer will tend to check whether the categories look plausible — not whether the categorization should have happened at all. The frame has already been set.

Third, the NIST AI RMF Generative AI Profile notes that human oversight must be meaningful, not nominal (NIST AI RMF Generative AI Profile, 2024). A review that happens after sensitive data has been processed, or after an irreversible action has been queued, is not the same as a review that happens before. Refusal and redesign are the meaningful oversight.

---

## Common Mistakes

**"If Cowork can do it, it is okay."** Capability is not authorization. Cowork can read a student accommodation file. That does not mean it should.

**"The task is safe if the output stays on my computer."** Cowork processes information to produce the output; the processing is the risk, not just the destination of the file.

**"Redaction is always enough."** Redacting a name from a medical record may not remove other identifying details. Redaction requires domain expertise about what constitutes identification — a task that itself requires human judgment.

**"Legal or medical disclaimers in the prompt make the output acceptable."** Writing "this is not legal advice" in a prompt does not transfer liability or improve the quality of a judgment Cowork is not equipped to make.

**"Sensitive data only means passwords."** Personnel notes, client strategies, unpublished research, internal financial projections, personal health disclosures in meeting notes — all of this is sensitive. The category is broader than most people initially assume.

**"The urgency justifies the shortcut."** Speed pressure is the most common reason people bypass the decision rule. The redesign moves exist precisely to preserve momentum without exposure. Do the redesign.

---

## Try This

**Exercise 1 (reflection):** Think of three tasks from your own work in the past month. Run each through the decision rule checklist above. Did any of them contain a stop condition you did not recognize at the time? What would redesign have looked like?

**Exercise 2 (hands-on):** Write a task brief for one of your legitimate Cowork tasks, then add a section at the bottom: "Stop conditions: Cowork must not proceed if it encounters [list your specific stop conditions for this task type]." Run the brief and check whether Cowork flags anything. If it does not flag anything, manually check the source files for the stop conditions you listed.

**Exercise 3 (redesign practice):** Take the accommodation folder scenario from the opening scene. Write a redesign brief: what could be done with synthetic data? What part of the categorization task genuinely requires a human professional? What would you tell your colleague about why the original approach does not work?

---

## What Would Change My Mind

The categorical refusals in this chapter are calibrated to current AI capabilities, current product configurations, and current organizational policy environments. I would revise specific entries if:

- Anthropic or an enterprise AI provider establishes approved, audited workflows for specific regulated data categories with documented compliance with HIPAA, FERPA, or equivalent frameworks. That would shift some items from "do not use personal Cowork" to "use only the approved enterprise workflow."
- Research demonstrated that LLM-generated personnel assessments, with appropriate constraints and human review, are more consistent and less biased than unaided human judgment. That evidence would change the redesign for personnel support — though the accountability argument would remain.

The irreversibility argument is harder to revise. Human final action before irreversible operations is not about AI capability; it is about organizational accountability. I do not expect that argument to change with product improvements.

---

## Still Puzzling

**Where exactly does "drafting for a human" end and "deciding for a human" begin?** If Cowork produces a performance review draft and the manager accepts it with minor changes, who made the assessment? This is a live organizational question, not a settled one. The conservative answer: the manager should have written the substantive assessment first, then used Cowork to help format or strengthen language. If Cowork produced the assessment and the manager approved it, that is closer to deciding than drafting.

**How should organizations communicate what is and is not approved?** Most individuals using Cowork today are ahead of their organizational AI policies. The guidance in this chapter represents a conservative default while those policies catch up. When organizational guidance arrives, follow it — and push for it to be specific about which tools, which data categories, and which workflows are covered.

---

## Bridge to Chapter 11

Chapters 9 and 10 together define the practical boundary of Cowork: what it can help assemble (knowledge artifacts, organized notes, structured documents), and what must not be delegated to it. The next question is sustainability. Chapter 11 shows how to turn a successful, safe Cowork workflow into something repeatable — a standard operating procedure your team can use without starting from scratch each time.

The discipline you have practiced across this book — defining the workspace, writing the brief, reviewing the plan, verifying the output, knowing when to stop — is the discipline that gets documented in a reusable workflow card. The habits you build now are the ones your successors will inherit.

---

## Sources Used

- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic Privacy Center, "How long do you store personal data?" updated March 16, 2026. https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-personal-data [verify — current as of writing]
- Anthropic Privacy Center, "I would like to input sensitive data into my chats with Claude." https://support.claude.com/en/articles/8325621-i-would-like-to-input-sensitive-data-into-my-chats-with-claude-who-can-view-my-conversations [verify — current as of writing]
- FTC, "AI Companies: Uphold Your Privacy and Confidentiality Commitments," 2024. https://www.ftc.gov/policy/advocacy-research/tech-at-ftc/2024/01/ai-companies-uphold-your-privacy-confidentiality-commitments
- HHS, "The HIPAA Privacy Rule." https://www.hhs.gov/hipaa/for-professionals/privacy/index.html
- NIST Privacy Framework. https://www.nist.gov/privacy-framework
- NIST, "Artificial Intelligence Risk Management Framework: Generative Artificial Intelligence Profile," 2024. https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf
- OWASP, "Top 10 for LLM Applications 2025." https://owasp.org/www-project-top-10-for-large-language-model-applications/
- U.S. Department of Education, "Protecting Student Privacy." https://studentprivacy.ed.gov/
- VPI-Bench, "Visual Prompt Injection Attacks for Computer-Use Agents," arXiv, 2025/2026. https://arxiv.org/abs/2506.02456
