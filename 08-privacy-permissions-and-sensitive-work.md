# Chapter 8 — Privacy, Permissions, and Sensitive Work

## TL;DR

- Privacy is a configuration question, not a vibe. The question is not "Is Claude private?" but "What account, plan, product, setting, data type, retention policy, connector, and agreement apply to this workflow?"
- Different Claude surfaces expand what Claude can see and do: chat involves conversation and uploads; Claude Code may include repository content; Cowork may include local files, apps, browser, computer use, and cross-app data flow.
- Minimize before you share: dedicated working folders, redacted copies, synthetic examples, and least-privilege access.
- Regulated data — health, financial, education, legal records — belongs in governed systems only. Do not use personal Claude accounts for employer, client, patient, or regulated data without explicit organizational approval.
- Product settings and privacy policies change. Check the current Anthropic Privacy Center and your organization's AI policy before starting sensitive workflows.

---

## Opening Scene

A nonprofit program manager needs to assemble a quarterly report for funders. She decides to use Claude Cowork to pull information from her Documents folder and draft the narrative sections. She grants access to the folder and starts the session.

She does not think much about what else lives in that folder. But it is her main working folder. It contains: the client intake files with names and phone numbers, last year's tax records, a spreadsheet of staff salaries, email exports from a fundraising dispute, and interview transcripts from a study she conducted with participant consent for internal use only.

None of that was supposed to go into a funder report. None of it is supposed to be accessible to a workflow tool beyond the specific task. Nothing malicious happened. No data was publicly exposed. But the access boundary was wrong from the start.

This chapter is about setting that boundary before the workflow begins, not after something goes wrong.

---

## What This Chapter Lets You Do

By the end of this chapter you can:

- Classify your work data by sensitivity level and assign it a default Claude posture
- Create a limited working folder and configure least-privilege access for file and workflow tasks
- Understand conceptually how privacy posture differs across Claude chat, Claude Code, and Claude Cowork
- Apply the "should Claude see this?" decision tree to any upcoming workflow
- Know when a workflow requires organizational approval rather than individual judgment

---

## Privacy Is a Configuration Question

The most important reframing in this chapter: privacy is not a question about whether Claude is trustworthy. It is a question about what your specific account, plan, product configuration, retention setting, and organizational agreement actually govern.

Anthropic's Privacy Center (March 2026) is explicit that data retention and access depend on the specific product and plan — not on a single universal Claude policy. [verify — current as of writing] The relevant questions are:

- What plan am I using — personal, Pro, Team, Enterprise, or API?
- What product surface is this — Claude chat, Claude Code, or Claude Cowork?
- What does my organization's AI policy say about this type of data?
- What connector, plugin, or MCP am I using, and what does that component's terms say?
- Is memory enabled, and what does that mean for cross-session retention?

Anthropic's Privacy Center support article on sensitive data (March 2026) addresses who may view conversations and what safeguards apply — but the answer varies by plan and context. [verify — current as of writing]

The FTC made the same point institutionally in 2024: AI companies and their deployers must honor privacy and confidentiality commitments, especially when users reveal internal documents or user data (FTC 2024). That obligation runs from the platform to you. You are responsible for choosing a configuration that honors your data obligations.

---

## What Each Surface Can See and Do

Understanding the privacy boundary starts with understanding that Claude surfaces are not interchangeable.

**Claude chat** — You share text and uploaded files within a conversation. The surface sees what you paste or upload. The conversation history may persist depending on your settings. Memory, if enabled, can carry context across sessions. [verify — current as of writing]

**Claude Code** — Claude Code may access repository content, read and write files within the scope you allow, run commands, and interact with your codebase. Anthropic's Claude Code data usage documentation notes that commercial terms, API arrangements, and optional data-retention configurations differ materially by account type. [verify — current as of writing] A developer working on personal Claude Code on proprietary code may be in a different privacy posture than a team using enterprise arrangements.

**Claude Cowork** — Cowork expands access significantly. According to Anthropic's safety documentation (Use Claude Cowork Safely), Cowork may interact with local files and folders, installed apps, browser windows and sessions, scheduled tasks, plugins, MCPs, and across-app data flows. The "Use Claude's Computer in Cowork" help page (April 2026) explains that computer use involves screenshots — meaning anything visible on the screen at the time Claude takes a screenshot is potentially in scope. [verify — current as of writing]

This is not a warning against using Cowork. It is a description of what you are configuring when you do. A broader surface requires more deliberate boundary-setting.

---

## Sensitivity Categories

Before you decide what to share, you need a clear way to classify what you have. The table below gives you a working taxonomy.

| Data type | Examples | Default Claude posture |
|---|---|---|
| Public | Published papers, public webpages, open datasets | Generally acceptable; check source and accuracy |
| Internal | Draft memos, meeting notes, nonpublic plans | Use with policy review; avoid broad sharing |
| Confidential | Client files, contracts, unpublished strategy | Avoid unless workflow is approved and governed |
| Personal | Names, emails, student or employee records | Minimize, redact, or avoid |
| Regulated | Health, financial, education, legal data, minor-related records | Use only in approved systems with appropriate agreements |
| Credentials and secrets | Passwords, API keys, tokens | Never share |
| Consequential actions | Purchases, sent messages, deletions, filings | Human-only final approval |

Regulated data deserves special attention because the obligation is not discretionary. The HIPAA Privacy Rule (HHS) defines protected health information and the conditions under which it can be disclosed. [verify — current as of writing] A clinician, researcher, insurer, or healthcare administrator who puts PHI into a general AI workflow without a business associate agreement and appropriate organizational approval is not just making a privacy mistake — they may be creating a legal liability. The same logic applies to student records under FERPA, financial data under relevant financial regulations, and legal materials under attorney-client privilege norms.

This chapter does not give legal advice. The practical rule is: if you are not certain whether a category of data is regulated and what your obligations are, treat it as regulated and get organizational guidance before proceeding.

---

## The Permission Discipline

NIST's Privacy Framework (NIST, Privacy Framework) provides a general principle: identify and manage privacy risk while enabling useful systems. The practitioner translation for Claude workflows:

**Share only what the task needs.** The fact that a file is in your working folder does not mean Claude needs to read it. Name what Claude should use; exclude what it should not.

**Prefer copies to originals.** Work from a copy of a document, not the live version. If something goes wrong, the original is intact. If you need to redact before sharing, you redact the copy.

**Redact before uploading.** Remove names, identifiers, account numbers, and any other sensitive markers before placing a document in Claude's working scope. If the redacted version does not have enough context to do the work, that is a signal the workflow may not be appropriate.

**Use a dedicated working folder.** Create a folder — call it something like "Claude-working" — that contains only materials appropriate for this session. Do not grant access to your main Documents folder or your Desktop.

**Name forbidden files, actions, and apps.** When configuring Cowork permissions, explicitly name what Claude should not touch. Do not assume exclusion by silence.

**Avoid sensitive portals.** Anthropic's computer-use documentation (April 2026) notes that computer use involves direct interaction with visible apps and browser windows. [verify — current as of writing] Do not run computer-use sessions while logged into email, financial accounts, patient portals, or any other sensitive interface unless you have reviewed the implications carefully.

**Check settings before you start.** Memory, history, retention, and training settings may differ across sessions, accounts, and products. Review the current Anthropic Privacy Center and Help Center for your account type before starting any sensitive workflow. [verify — current as of writing]

---

## The "Should Claude See This?" Decision Tree

Before sharing any file, folder, or data with any Claude surface, walk through these questions:

1. **Is this data public?** If yes and you have source confirmation, proceed.
2. **If not public, do I have policy and need-to-know permission to use it in this workflow?** Check your organization's AI policy, your client or employer agreement, and any relevant regulation.
3. **Is this data personal, regulated, credential-like, or client-confidential?** If yes, treat it as restricted by default.
4. **Can I redact, aggregate, or replace it with synthetic examples?** If the workflow can proceed without the sensitive elements, remove them.
5. **Does this task require local files, connectors, browser, or computer use?** If yes, the access boundary expands and each element needs its own permission check.
6. **Is the action reversible?** If Claude will send a message, delete a file, make a purchase, or file something on your behalf, that action may not be undoable. Keep final approval for irreversible actions explicitly human.
7. **What settings, retention rules, and logs apply?** Confirm before starting.
8. **Who owns the final decision?** If the answer is unclear, the decision stays human.

---

## Memory, History, and Retention

Claude Cowork includes memory and chat search features that can build context across sessions (Anthropic Help Center, Use Claude's Chat Search and Memory). [verify — current as of writing] This is a convenience feature, but it is also a privacy-relevant one. Memory changes what Claude can retain from one session to the next — including information you may not have intended to carry forward.

The practical advice here is not to disable memory categorically. It is to understand that memory, conversation history, incognito chat, history retention, and enterprise data export policies are separate controls, each of which may affect what persists and who can access it. [verify — current as of writing] Review the current settings for your account before using memory in workflows that involve sensitive context.

For sensitive sessions, consider using incognito or history-off modes if available for your account and workflow. Check the Anthropic Help Center for current behavior on your plan before relying on any specific setting. [verify — current as of writing]

---

## Scenarios: Applying the Framework

**Student records.** A teacher wants Claude to help summarize student progress notes for a report. The safer workflow: anonymize or aggregate the data before using Claude. Use initials or pseudonyms if Claude needs to refer to individuals. Check whether your institution has an AI policy governing student data. If student-identifiable data is involved and the institution has not approved the tool, do not proceed.

**Interview transcripts.** A researcher with human-subjects transcripts wants coding help. The safer workflow: check your IRB protocol and consent form — did participants consent to their words being processed by external AI tools? Can you de-identify the transcripts adequately? If not, explore approved local tools or submit a protocol amendment.

**Client documents.** A consultant wants Cowork to assemble a client report from local files. The safer workflow: create a sanitized project folder. Remove contracts, personnel data, and proprietary financial figures. Work from redacted versions. Log what was shared.

**Code repository.** A developer wants to use Claude Code on a proprietary codebase. The safer workflow: check your employer's AI tool policy. Verify whether your Claude Code account type (personal, API, enterprise) matches what your employer permits. Do not assume personal-plan use of proprietary code is covered by enterprise agreements. [verify — current as of writing]

**Healthcare notes.** A clinician should not put protected health information into any general AI chat tool unless the organization has approved the specific product for that use, with appropriate agreements and security controls. This is a firm boundary, not a preference.

**Browser automation.** Claude in a browser session can encounter logged-in pages, session cookies, saved credentials, and injected instructions in web content. Restrict access to trusted sites. Avoid running browser sessions while signed into financial accounts, patient portals, or administrative systems.

---

## Personal Accounts and Work Data

A practical default that covers most ambiguous cases:

**Do not use personal Claude accounts for employer, client, student, patient, or regulated data unless explicitly approved by the relevant institution.**

Personal plans typically operate under consumer-oriented terms. Enterprise and team arrangements often include additional privacy controls, data processing agreements, and retention restrictions. The gap between those configurations can be significant, and it is your responsibility to know which one applies to your workflow. Anthropic's Claude Code data usage documentation illustrates that these configurations differ materially by account type. [verify — current as of writing] The same logic applies to Cowork and Claude chat.

When in doubt: check your institution's AI policy, ask your IT or legal team, or use a system your organization has already approved.

---

## Common Mistakes

**"If I can access the file, I can give it to Claude."** Your access rights and your authorization to share data with a third-party AI tool are different questions. You may be allowed to read a file and not allowed to share its contents externally.

**"Privacy settings are the same across Chat, Code, Cowork, API, Team, and Enterprise."** They are not. Retention, training data use, memory, and access controls differ. Check the current configuration for your account. [verify — current as of writing]

**"Incognito means nobody can access the content."** Incognito or no-history modes may limit what persists in your account. They do not necessarily change what is processed during the session or what the service provider retains under its platform agreements. [verify — current as of writing]

**"A local file workflow is automatically private."** A Cowork session that reads local files is processing those files through a connected service. Local storage of the originals does not make the workflow private if Claude reads them.

**"Sensitive data only means passwords or bank numbers."** Unpublished research ideas, peer-review manuscripts, employee performance notes, student accommodation records, proprietary source code, business strategy documents, and internal communications may all be sensitive even when they do not look like obvious secrets.

**"If Claude asks for permission, the action is safe."** The permission request tells you Claude is about to act. It does not tell you the action is appropriate, reversible, or within your data governance obligations. You decide whether to approve.

---

## The Permission Checklist

Before starting any Cowork or file-heavy Claude workflow:

- [ ] Dedicated working folder created
- [ ] Originals backed up and excluded
- [ ] Sensitive files removed from working folder
- [ ] Redactions applied and checked
- [ ] Allowed apps and sites explicitly named
- [ ] Forbidden apps and sites explicitly named
- [ ] Memory and retention settings reviewed for this account [verify — current as of writing]
- [ ] Connectors and plugins confirmed as necessary and appropriate
- [ ] Irreversible actions prohibited or flagged for manual approval
- [ ] Output inspected for inadvertent inclusion of sensitive data
- [ ] Organizational AI policy confirmed

This checklist is not complete — your specific workflow, institution, and data types will add requirements. Treat it as a starting template.

---

## Try This

**Exercise 1 — Classify Your Own Files (hands-on)**

Open your main working folder — the one you would most naturally point Claude toward. Classify each item using the sensitivity taxonomy: Public, Internal, Confidential, Personal, Regulated, Credentials, Consequential. Count how many files fall into each category. Now: how many of those would be appropriate to include in a Claude Cowork session under your current account configuration? What would you need to remove before creating a working folder?

**Exercise 2 — Decision Tree Practice**

Think of a real workflow you would like to use Claude for that involves files, documents, or actions. Walk through the "Should Claude see this?" decision tree for that workflow. At which step do you first hit a question you cannot confidently answer? What would you need to find out before proceeding?

---

## What Would Change My Mind

The advice here is deliberately conservative. It would loosen if:

- Anthropic and similar platforms developed stable, auditable, and clearly documented per-plan privacy guarantees that practitioners could rely on without re-checking frequently. The current state of the documentation requires ongoing verification because policies have changed and continue to change. [verify — current as of writing]
- Institutional AI policies became standard enough that a practitioner could reliably know their obligations from a common template rather than navigating institution-by-institution. That standardization would reduce the burden of checking each context.
- Technical controls for data minimization — automatic redaction, selective-field access, granular permission scoping — became standard in workflow agent interfaces. Those controls would make least-privilege access easier to implement without requiring careful manual folder preparation.

Until those changes arrive, start narrow.

---

## Still Puzzling

- How do organizational AI policies interact with individual Claude account configurations? If a university approves "Claude Enterprise" for faculty, does that approval extend to personal Pro accounts used on the same work? The answer varies by institution, and the guidance from most institutions is still catching up to practice.
- What happens to data processed during a Cowork session that is never explicitly retained — is processing itself a privacy event worth disclosing to participants or clients? Legal frameworks give different answers in different jurisdictions.
- How should practitioners handle the fact that privacy settings and product terms change faster than institutional policies can track them? This is an organizational governance problem, not just a user practice problem, and this chapter can only gesture at it.

---

## Bridge to Chapter 9

Chapter 8 taught you where the boundary is before you start. Chapter 9 builds on both the verification discipline from Chapter 6 and the privacy boundary from Chapter 8 to design a repeatable personal workflow — templates, decision rules, and habits that make the right choices automatic rather than effortful.

---

## Sources Used

- Anthropic Privacy Center. "How long do you store personal data?" Updated March 16, 2026. https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-personal-data [verify — current as of writing]
- Anthropic Privacy Center. "I would like to input sensitive data into my chats with Claude. Who can view my conversations?" Updated March 16, 2026. https://support.claude.com/en/articles/8325621-i-would-like-to-input-sensitive-data-into-my-chats-with-claude-who-can-view-my-conversations [verify — current as of writing]
- Anthropic. "Data usage." Claude Code Docs. https://docs.claude.com/en/docs/claude-code/data-usage [verify — current as of writing]
- Anthropic. "Use Claude Cowork safely." Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic. "Let Claude use your computer in Cowork." Claude Help Center. April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork [verify — current as of writing]
- Anthropic. "Use Claude's chat search and memory to build on previous context." Claude Help Center. https://support.claude.com/en/articles/11817273-using-claude-s-chat-search-and-memory-to-build-on-previous-context [verify — current as of writing]
- NIST. "Privacy Framework." https://www.nist.gov/privacy-framework
- NIST. "Artificial Intelligence Risk Management Framework: Generative Artificial Intelligence Profile." NIST AI 600-1. 2024. https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf
- Federal Trade Commission. "AI Companies: Uphold Your Privacy and Confidentiality Commitments." 2024. https://www.ftc.gov/policy/advocacy-research/tech-at-ftc/2024/01/ai-companies-uphold-your-privacy-confidentiality-commitments
- U.S. Department of Health and Human Services. "The HIPAA Privacy Rule." https://www.hhs.gov/hipaa/for-professionals/privacy/index.html
