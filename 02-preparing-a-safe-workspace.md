# Preparing a Safe Workspace

## Capability Built

By the end of this chapter you will be able to create a bounded folder and access plan — the practical setup that tells Cowork exactly what it may see, what it may create, and what it must not touch.

---

## The Documents Folder Mistake

A project manager at a nonprofit needs a briefing document. She has been working on this program for two years. Her laptop's Documents folder has everything: program notes, a shared spreadsheet from the finance team, old grant applications, client case notes, staff performance reviews, a folder of tax-adjacent financial disclosures, personal medical appointments, and several years of meeting notes that include private conversations about personnel.

She opens Cowork, grants access to her Documents folder — it is where all the program files are — and asks for a briefing document.

Cowork can now see all of it.

This is the mistake this chapter prevents.

The problem is not that Cowork will immediately misuse the sensitive files. The problem is that she granted access to material that has no business being in a Cowork workspace: personnel notes, client case data, personal medical records, financial disclosures. Some of that material may be regulated. Some of it may be confidential by organizational policy. Some of it, under data minimization principles (NIST Privacy Framework), should never be shared with any tool that is not specifically authorized to process it.

The fix is not cleverness with prompts. The fix is creating a bounded workspace before the session begins.

---

## What a Safe Workspace Is

A safe workspace is a limited, intentional folder environment where Cowork can operate without seeing or changing unrelated material. It is both a file-management practice and a privacy-control practice.

The principle behind it is called **least privilege**, one of the most durable concepts in information security (Saltzer and Schroeder, 1975): a system should have access only to the information and resources required for the task. No more. Not the whole drive because that is where the task files happen to live. Only what the task needs.

A safe Cowork workspace does five things:

1. Specifies what Cowork may read (input files)
2. Specifies where Cowork may write (output folder)
3. Separates the task files from everything else on your machine
4. Preserves originals so the task can be reversed
5. Documents what the task is and what Cowork is allowed to do

None of this requires technical skill. It requires the habit of preparation before delegation — the same habit you would apply before handing files to any assistant.

---

## Preparing the Workspace: Step by Step

### Step 1: Create a task folder

Create a new folder with a name that describes the task and the date. Something like `program-report-june-2026-cowork`. Put it somewhere you can find it — a working-projects drive, a desktop staging area, or a dedicated AI-work folder.

This folder is the boundary. Cowork works inside it. Your original files stay outside it.

### Step 2: Identify what the task actually needs

Before you copy anything, list the source files the task genuinely requires. For a program briefing document, you might need: three quarterly reports, meeting notes from the program team (excluding personnel conversations), and a budget summary from finance with names and salaries removed.

That list is also a filter. Everything not on the list stays out of the workspace.

### Step 3: Copy input files — do not move them

Copy the relevant files into an `inputs/` subfolder inside your task folder. Do not move originals. Moving originals means there is no fallback if something goes wrong.

If the task involves files you cannot copy freely — confidential client records, regulated data, files under organizational data governance — stop here and ask whether this task should be in Cowork at all. Chapter 10 covers the boundary conditions.

### Step 4: Review what you are copying

Before the copy goes into the workspace, read through it. Remove or redact:
- Names, phone numbers, addresses, or identifying details that are not necessary for the task
- Private comments, editorial notes, or side conversations embedded in documents
- Credentials, passwords, or access tokens in any format
- Anything you are not authorized to share with an AI tool under your organization's policies

A note on redaction: redaction can fail. Covered text in some PDF tools is not actually removed from the underlying file (Anthropic Privacy Center, sensitive data guidance). If you redact, inspect the result before it enters the workspace (NIST Generative AI Profile, 2024).

### Step 5: Create an outputs folder

Create an `outputs/` subfolder. Name it clearly. When you write the task brief (Chapter 4), you will tell Cowork to write its results here — not to your desktop, not to the original folder, not anywhere it chooses.

### Step 6: Add a README or task note

Create a short text file in the workspace root — call it `README-task.md` or just `TASK.txt`. Write down:
- What this workspace is for
- What files are in inputs
- What Cowork is allowed to create in outputs
- What Cowork should not touch or change
- What you will verify when the task is done

This note serves two purposes: it helps you think clearly about the task before you start, and it can be included in your task brief so Cowork understands the boundaries from the first message.

---

## Recommended Folder Structure

```
program-report-june-2026-cowork/
  README-task.md
  inputs/
    q1-program-report.pdf
    q2-program-report.pdf
    march-team-meeting-notes.docx
    budget-summary-no-names.xlsx
  outputs/
  archive-originals-not-for-cowork/
```

The `archive-originals-not-for-cowork/` folder in this diagram is not granted to Cowork. It represents the originals you kept elsewhere on your machine. Its label is a reminder, not a grant. Cowork does not need to see this folder and should not be given access to it.

---

## What Cowork Can and Cannot Access

Cowork's access to your computer is not automatic. You grant it by connecting local folders or enabling connectors [verify — current as of writing]. The act of granting access is the moment where the safe-workspace preparation matters most.

When you grant access to a folder, Cowork can see the contents of that folder. If you grant a broad folder — Documents, Downloads, Desktop — Cowork can see everything in it. Anthropic's safety documentation is explicit that the scope of what Cowork can access is a user decision, not a default, and that users are responsible for the scope they grant (Anthropic, "Use Claude Cowork safely").

Anthropic's privacy guidance also describes how data is handled in Claude conversations and what retention applies to different account and plan types (Anthropic Privacy Center, 2026). The details depend on your account type, organizational plan, and Anthropic's current policies. Before you share any files — particularly sensitive organizational or personal information — read the current privacy documentation for your account type [verify — current as of writing].

The FTC has noted that AI companies should uphold the privacy and confidentiality commitments they make to users (Federal Trade Commission, 2024). That means the commitments matter — but you need to know what they are before you depend on them.

---

## Computer Use and the Visible Desktop

When Cowork uses computer-use capabilities to interact with applications, websites, or your visible desktop, the workspace extends beyond folders [verify — current as of writing] (Anthropic, "Let Claude use your computer in Cowork"). Any logged-in application, open browser tab, visible document, or active session may be in the practical workspace even if you did not explicitly grant it as a file.

If you are using computer-use features:
- Close tabs and applications not relevant to the task before starting
- Log out of sensitive portals you do not need open
- Use a clean browser profile or context where possible
- Treat "visible on screen" as "accessible to Cowork" in your planning

This is not a reason to avoid computer use. It is a reason to prepare your screen the same way you prepare your folder.

---

## The Backup Requirement

Any task that involves renaming, reformatting, cleaning, or transforming files requires a backup of the originals before the task begins. This is not optional.

Cowork may be asked to reorganize a folder, rename files according to a convention, clean up a spreadsheet, or reformat a document. If something goes wrong — a batch rename applies to the wrong files, a cleanup step removes a column — you need to be able to restore the original state.

The backup does not need to be elaborate. A copy of the input files in a location that Cowork cannot reach is sufficient. The `archive-originals-not-for-cowork/` pattern above, stored outside the Cowork workspace, is the minimum.

For file-renaming and bulk-operation tasks specifically, Chapter 8 adds a dry-run discipline — asking Cowork to show you the proposed changes before it makes them. The backup is insurance in case you approved a step you should not have.

---

## What Belongs Outside the Workspace

Some files should never enter a Cowork workspace without formal organizational authorization:

**Regulated data:** Health records (HIPAA applies in the US), student educational records (FERPA), financial statements under regulatory filing requirements. These categories have specific legal protection and handling requirements. Using a general AI tool to process them without approved governance is not a workaround — it is a potential compliance violation (HHS HIPAA Privacy Rule).

**Client-confidential material:** Legal agreements, client case files, confidential project details — material your organization or professional role requires you to protect.

**Identifiable personal data in bulk:** A list of individual employees with salaries and performance ratings, a dataset of community members with health conditions, a log of student accommodations. Even if individual fields seem benign, the combination can be identifying and sensitive.

**Your own private personal files:** Personal medical information, private financial records, family documents, personal correspondence. Just because they are on the same machine as your work files does not make them work files.

The test: If you would not hand this file to a temporary outside contractor without a signed confidentiality agreement, it should not be in a Cowork workspace without at minimum understanding your tool's current data handling policies.

---

## The Human Gate in This Chapter

Workspace preparation is entirely human work. Cowork cannot decide what it should be allowed to see. That judgment — this file belongs in, that file stays out, these details need to be removed first — requires you to understand the task, the data, and the risk.

The data minimization principle from NIST's Privacy Framework is a practical guide: provide only the information necessary for the task, not everything that might be relevant. In practice that means listing required source files rather than dropping a whole project folder into the workspace.

This preparation is not bureaucracy. It is the difference between a session that produces a trustworthy artifact and a session that mixes good program data with information that had no business being there.

---

## Common Mistakes

**"If the file is on my computer, it is okay to share."** The location of a file does not determine the appropriateness of sharing it. Regulated and confidential data is still regulated and confidential on your personal machine.

**"A folder boundary is only for neatness."** A folder boundary is an intellectual and a privacy boundary. Cowork operating inside a clean, bounded folder produces better outputs — fewer irrelevant files, clearer task scope — and fewer risks.

**"Copies are unnecessary if Cowork is careful."** Cowork makes mistakes. Even careful tools make mistakes. Backups exist precisely for the moments when caution was not sufficient.

**"Sensitive data means only passwords."** Meeting notes with private personnel comments, internal strategy documents, draft contracts, unpublished research, and personal communications can all be sensitive even without a password in sight.

**"Deleting sensitive files after the output fixes the problem."** After a session, files that were in the workspace may have been processed. The concern is not just what is in the folder now — it is what was shared during the session. Remove sensitive files before the session, not after.

**"Computer use sees only the app I care about."** Computer use can see the visible desktop. Plan accordingly.

---

## Try This

**Exercise 1 — The workspace audit.** Pick one task you identified as Cowork-shaped in Chapter 1. Before creating any files, list the source materials the task genuinely needs. Then look at where those files currently live. What else is in the same folder or drive? What would Cowork see if you granted access to that location? Note any files that should not be in the workspace. Then create a clean task folder with inputs, outputs, and a README.

**Exercise 2 — The sensitivity scan.** Take the files you planned to copy into the workspace. Read each one before copying. Identify any information that should be removed or is not needed for the task. Practice making a redacted copy for one of those files — and then verify the redaction actually removed the information, not just covered it visually.

---

## What Would Change My Mind

The least-privilege principle (Saltzer and Schroeder, 1975) is foundational security practice and has not been seriously contested in fifty years. Data minimization as a privacy principle is stable across frameworks (NIST, GDPR, CCPA). The practical workspace preparation steps in this chapter are grounded in those stable principles.

What could change: specific product behaviors around folder granting, data retention, and privacy commitments. Anthropic updates its policies, and the specific details of how Cowork handles your data depend on your account type and the current policy documentation. Every product-specific claim in this chapter should be verified against current documentation before high-stakes use [verify — current as of writing].

The interface for granting folder access is particularly high-aging-risk. The steps may look different in your version of Cowork than in any screenshot or step-by-step instruction written at any point. Rely on principles, verify mechanics.

---

## Still Puzzling

- What should the standard be for organizational teams that share a Cowork workspace? Who is responsible for workspace preparation when multiple people contribute files?
- How should redaction of PDFs be reliably verified by non-technical users?
- As Cowork gains more sophisticated permissions models, will least-privilege become something the tool can enforce rather than something the user must construct manually?

---

## Bridge to Chapter 3

You now have a bounded workspace. The next chapter explains how Cowork actually connects to files and external sources: local files, connectors, cloud sources, and browser assistance. Understanding what access means mechanically — not just conceptually — lets you make confident decisions about what to enable and what to leave off for each task.

---

## Sources Used

- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic Privacy Center, "How long do you store personal data?" updated March 16, 2026. https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-personal-data [verify — current as of writing]
- Anthropic Privacy Center, "I would like to input sensitive data into my chats with Claude. Who can view my conversations?" updated March 16, 2026. https://support.claude.com/en/articles/8325621-i-would-like-to-input-sensitive-data-into-my-chats-with-claude-who-can-view-my-conversations [verify — current as of writing]
- Anthropic, "Let Claude use your computer in Cowork," Claude Help Center, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork [verify — current as of writing]
- Federal Trade Commission, "AI Companies: Uphold Your Privacy and Confidentiality Commitments," 2024. https://www.ftc.gov/policy/advocacy-research/tech-at-ftc/2024/01/ai-companies-uphold-your-privacy-confidentiality-commitments
- HHS, "The HIPAA Privacy Rule." https://www.hhs.gov/hipaa/for-professionals/privacy/index.html
- NIST, "Privacy Framework." https://www.nist.gov/privacy-framework
- NIST, "Artificial Intelligence Risk Management Framework: Generative Artificial Intelligence Profile," 2024. https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf
- Saltzer, J.H. and Schroeder, M.D., "The Protection of Information in Computer Systems," 1975. https://web.mit.edu/Saltzer/www/publications/protection/
