# Chapter 8 — Organizing and Renaming Files

## TL;DR

- File organization feels mundane. It is not. Moving, renaming, and deleting files changes your working environment — sometimes irreversibly.
- The safe sequence: inventory, propose convention, dry run, review, backup, approve, create manifest, verify.
- Cowork should propose file changes, not execute them silently. You approve each batch.
- Cowork should never delete files. Deletion is a human action.
- A naming convention applied automatically is still an interpretation. Check the low-confidence cases.

---

## The Scene

The research folder has accumulated three years of files. There are two hundred and forty-three documents. Some have meaningful names: `interview-transcript-participant-07-2024-03.docx`. Many do not: `final_FINAL_v3.docx`, `notes`, `scan0047.pdf`, `untitled (2).pdf`.

You ask Cowork to organize the folder: create subfolders by project and year, rename files to a consistent format, and flag obvious duplicates.

Forty minutes later, Cowork reports completion. Two hundred and thirty files have been moved and renamed. The folder looks clean. You open it and search for the signed consent forms you need for an IRB renewal. They are not where they were. They are not in the new structure either. Cowork renamed them — the signed consent forms were `scan0012.pdf` through `scan0019.pdf` — and placed them in a folder called `Uncategorized-Scans/` with new names like `scan-undated-001.pdf`. The original names and locations are gone.

The forms still exist. It takes two hours to find them, verify they are the right ones, and update your IRB materials with their new locations. Two hours on a task that was supposed to save time. And you were lucky: Cowork did not delete anything.

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to:

- Use Cowork to generate a file inventory and a proposed naming convention without executing any changes.
- Request a dry-run manifest and review it before anything moves.
- Identify which files need human judgment before they can be renamed or moved.
- Protect reversibility through backup, manifest, and post-action verification.
- Set a firm rule: Cowork proposes; you approve; you delete.

---

## File Operations Are State Changes

A conversation you have with Cowork can be revisited. A document it drafts can be revised. A file it moves or renames is in a new location. A file it deletes is gone, or at minimum requires active recovery effort. These are not equivalent actions.

The reason file organization feels safe is that the individual actions look small. Move this file to that folder. Rename this one to that format. But the cumulative effect of two hundred automated moves is a working environment that no longer matches your mental map of where things are. Links break. Citation references go stale. Shared collaborators lose access because the path they bookmarked no longer exists. Search tools that indexed the old names return nothing for the new ones.

Bainbridge's "Ironies of Automation" (1983) observed that automation does not eliminate human monitoring — it often increases the monitoring burden, because failures happen faster and at scale. Applied to file organization: Cowork can rename two hundred files faster than you can blink, which means a systematic error propagates through two hundred files before you can catch it.

The response to this is not to avoid file automation. It is to run it in stages that keep the human in control of each decision that matters.

---

## The Safe Sequence

### 1. Inventory first

Before any file moves, ask Cowork to produce a plain inventory: a list of every file in the folder with its current name, location, size, modification date, and any metadata it can read. No changes. Just a list.

This inventory is your before-state. It is also a diagnostic: you will see the problem files, the mysteriously named files, the probable duplicates, and the ones that look like they belong somewhere else. You need to see this before you write a naming convention, because the convention you write should be based on what is actually in the folder, not on what you assume is there.

### 2. Define the naming convention explicitly

A naming convention is a rule. Write it out: what elements go into the name, in what order, in what format. For example:

`YYYY-MM-DD_project-code_document-type_version.ext`

Specify how to handle files that do not fit the convention cleanly. What happens to a file with no date? What happens to a file whose project code is unclear? These are not edge cases — they are the files you most need to get right, because they are the ones most likely to have an ambiguous automated name applied.

### 3. Dry run — manifest before action

A dry run is a proposed table of changes, not the changes themselves. Ask Cowork to produce a manifest in this format:

| Current path | Proposed path | Action | Reason | Confidence | Human review? |
|---|---|---|---|---|---|
| `/research/scan0012.pdf` | `/research/consents/consent-signed-001.pdf` | Rename + Move | Appears to be signed consent form | Low | Yes |
| `/research/final_FINAL_v3.docx` | `/research/reports/2024-annual-report-v3.docx` | Rename + Move | Annual report based on content | Medium | Yes |

**Confidence and Human review? are the two columns that tell you where to spend your time.** Any row marked Low confidence or Yes on human review should be opened and checked before you approve it. These are not suggestions. They are Cowork telling you it guessed.

The manifest is not a contract — it is a proposal. You can modify it, reject individual rows, or reject the whole thing and provide a clearer convention.

### 4. Review the manifest

Read the manifest before you approve anything. You are looking for:

- Files that should not be moved (anything shared, linked, published, or cited elsewhere)
- Files with low-confidence name assignments
- Files that look like duplicates of something important
- Files that are missing from the proposed list (Cowork may have skipped files it could not process)
- Any proposed deletion (there should be none — deletions are human-only)

Shared folders and files with external links deserve special attention. Moving a file on your local machine may break a link in a shared document, a citation in another file, or a script that expects the file at a specific path. If any file in the folder is shared with collaborators, check whether moving or renaming it will break their access (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

### 5. Backup before action

Once you have approved the manifest, make a backup of the current folder before anything moves. This can be a compressed archive, a copy to a different directory, or a snapshot in your cloud backup system. The backup is not a formality — it is the mechanism that makes the whole operation reversible.

The cost of making a backup of two hundred files is a few seconds. The cost of reconstructing a working file structure from memory after a bad batch rename is hours. Least privilege applies to file operations too: work on the smallest scope needed, and always have a path back (Saltzer and Schroeder 1975).

### 6. Approve and execute in batches

Do not approve the entire manifest at once if the folder is large or the stakes are high. Approve a subset — the high-confidence, well-understood files — and run those first. Check the result. Then approve the next batch. Stop and investigate if anything looks wrong.

The Microsoft Research guidelines for human-AI interaction recommend making AI actions reversible and providing users with control and correction capabilities (Microsoft Research, CHI 2019). Batch execution supports this: each batch can be checked before the next one runs.

### 7. Create a before/after manifest and verify

After the operation completes, ask Cowork to produce a final manifest showing what actually changed: old path, new path, action taken, date. Save this manifest alongside the reorganized folder. It is your record of what happened.

Then spot-check. Find five to ten files you specifically care about — the ones you use regularly, the ones that are hard to replace, the ones that had unusual names — and verify that they are where the manifest says they are and that their contents are intact.

---

## A Worked Walkthrough

**The task:** Organize a folder of 120 literature PDFs. Goal: consistent author-year-title naming, organized by topic subfolder.

**The task brief:**

```
Task: Inventory and organize literature PDFs
Source: /research/literature-raw/ (120 PDF files — working copy)
Output: Proposed manifest only — no file changes until I approve

Step 1: Inventory
- List all 120 files with current name, size, and any metadata readable from filename

Step 2: Naming convention
- Proposed format: AuthorLastname-Year-ShortTitle.pdf (max 50 chars total)
- If author is unclear: UNKNOWN-Year-ShortTitle.pdf
- If year is unclear: AuthorLastname-XXXX-ShortTitle.pdf
- Do not remove original extension

Step 3: Proposed topic subfolders
- Suggest 4–6 topic folders based on title content
- Assign each file to one folder; flag files you cannot confidently assign

Step 4: Produce manifest
- Show full proposed rename and move for every file
- Include Confidence (High / Medium / Low) for each row
- Flag any file that needs human review before moving
- Include count of High/Medium/Low confidence assignments at the end

Do not move or rename any files. Produce the manifest for my review.
```

**What to check in the manifest:**

- Every Low confidence row — open the file, verify the name assignment
- Every file flagged for human review
- The UNKNOWN and XXXX placeholders — decide whether to fill them or leave them
- The topic folder assignments for any file whose topic you know well

**Before approving:** make a compressed copy of `literature-raw/` and store it outside the working folder.

**After approving:** verify ten specific files by looking them up in the final manifest and confirming they are in the right place.

---

## The Human Gate for File Operations

Three questions before any batch file operation:

**One: Is there a backup?** Not a plan to back up — an actual backup, already made, before the first file moves.

**Two: Have I read the manifest?** Not skimmed. Read. Every Low confidence row and every flagged row is a judgment call you need to make, not delegate.

**Three: Am I working on a copy?** If the source folder is the only copy of these files, you are working without a safety net. Make a copy first, always (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

---

## The Deletion Rule

Cowork should not delete files. State this explicitly in every file organization brief.

This is not a technical limitation — Cowork is capable of deleting files if you give it permission. It is a workflow discipline. Deletion is permanent or requires active recovery effort. The cost of keeping a probably-duplicate file is near zero. The cost of deleting an actually-important file is unpredictable.

Set the rule in writing in your task brief: "Do not delete any files. If you identify probable duplicates, list them in a separate section of the manifest. I will review and decide."

OWASP's guidance on LLM application risks flags excessive agency — allowing an AI system to take actions with high real-world impact without sufficient human oversight — as a key risk category (OWASP, Top 10 for LLM Applications 2025). File deletion is a simple, concrete example. The remedy is equally simple: keep the deletion decision with the human.

---

## Duplicate Detection

Cowork can identify files that appear to be duplicates based on name similarity, content similarity, or both. This is useful. It is also imprecise in specific ways you need to understand.

**Files that look like duplicates may not be.** A file named `report-v2.docx` and a file named `report-final.docx` in the same folder might be genuine duplicates. They might also be a working draft and a signed, approved final — functionally different even if textually similar. Version history, approval status, metadata, and annotations can differentiate files that look the same by content.

**Deletion should always follow human review.** The manifest for duplicate detection should produce a list of probable duplicate pairs with Cowork's reasoning. The human decides for each pair which to keep, which to archive, and which (if any) to delete. A rule of thumb for conservative practice: archive before deleting. Move the suspected duplicate to an `archive/` folder for thirty days. If you never need it, delete it then.

---

## Common Mistakes

**Approving the entire manifest without reading it.** The manifest is a proposed change to your working environment. Low-confidence rows are the places Cowork is most likely to be wrong, and those are exactly the rows most likely to be skimmed.

**Working on the original folder.** File organization should operate on a working copy, not the only copy of your files. This is not about distrust of Cowork — it is about having a path back if anything goes wrong.

**Letting Cowork delete.** The gain from automated deletion is minimal. The risk is permanent. Keep deletion with the human.

**Ignoring shared files.** If any file in the reorganized folder is shared with other people or referenced by external links, path changes can break access without any warning. Before moving shared files, check whether the move will affect anyone else.

**Treating naming conventions as objective.** A naming convention encodes assumptions: what the date refers to, what the project code means, what counts as one document versus two. Applying a convention automatically applies all its assumptions automatically. The low-confidence rows are where those assumptions break down.

**Assuming the operation is easy to undo.** If you do not have a manifest and a backup, a bad batch rename requires manual reconstruction. "Easy to undo" depends entirely on having made it reversible before you started.

---

## Try This

**Exercise 1 — Inventory as diagnosis.** Pick a folder you have been meaning to organize. Before writing any brief, ask Cowork for a plain inventory: every file, its name, date, and size, no proposed changes. Read the inventory. What surprises you? How many files do you not recognize? How many look like duplicates? How many have names that tell you nothing? Use this to write a naming convention before asking Cowork to propose any changes.

**Exercise 2 — Dry-run review.** Ask Cowork for a dry-run manifest for a folder reorganization. Do not approve anything yet. Go through the manifest and mark every row you would need to verify before approving. Count them. What fraction of the total are you confident about? What does that tell you about where to spend your review time?

**Exercise 3 — Backup discipline.** Before your next Cowork file task — any file task — make a backup first and record where it is. After the task is done, open the backup and verify that it contains what you intended to preserve. Do this three times. By the third time, it should feel automatic.

---

## What Would Change My Mind

This chapter recommends human-only deletion and mandatory dry runs for all bulk file operations. These constraints are appropriate for nontechnical professionals working with files that may be the only copies of important work. For professionals with reliable version control, automated backup systems, or cloud storage with full revision history, some of these constraints could be relaxed — specifically, the backup step may be redundant if the storage system already maintains full history. What would need to be verified: that the version history is complete, that individual file versions are recoverable without professional help, and that the recovery process has been tested. If all three are true [verify — current as of writing], a lighter verification workflow is defensible.

---

## Still Puzzling

File organization is one of the places where Cowork's capabilities and the average nontechnical user's mental model of what is happening are most misaligned. A user who thinks of Cowork as "helping me clean up my folder" may not understand that it is making classification decisions — and those decisions encode assumptions about what the files are and what they are for. The deeper question is whether an automated naming convention can be made legible enough for a nontechnical user to understand and correct the assumptions it encodes. The manifest approach in this chapter is one answer. It is probably not the only one.

---

## Bridge to Chapter 9

You have extracted data, assembled reports, and organized your files. Now the work turns outward: producing knowledge artifacts that other people use.

Chapter 9 covers research packets and meeting notes — the connective tissue of knowledge work. Scattered notes, action items, follow-up questions, and reference materials need to become documents other people can use. The verification challenge shifts from checking numbers and file locations to checking whether the summary accurately represents what was decided, who committed to what, and what remains open.

---

## AI Wayback Machine

**Lillian Gilbreth** was one of the first industrial engineers to apply systematic analysis to how people organize their physical workspaces — not to eliminate human judgment, but to remove the unnecessary effort that obscured it. Her insight was that a well-organized workspace makes the important decisions visible and the routine ones automatic. A disorganized workspace does the opposite: you spend energy on where things are instead of what they mean. A dry-run manifest before a file reorganization is a Gilbreth move: surface the decisions that matter, make the routine ones visible, and keep the human in charge of the ones that count.

**Run this:**

```
Who was Lillian Gilbreth, and what was her contribution to industrial engineering and workplace design? How does her concept of workspace organization apply to digital file management? Keep it to three paragraphs.
```

→ Search **"Lillian Gilbreth"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask about Gilbreth's work on making kitchens and offices more efficient for people with disabilities — and how her approach prioritized human capability rather than just efficiency.
- Ask how the Gilbreth motion-study method (analyzing every physical movement in a task) might apply to analyzing every step in a digital knowledge workflow.

What changes? What gets better? What gets worse?

---

## Sources Used

- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic, "Let Claude use your computer in Cowork," Claude Help Center, April 24, 2026. https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork [verify — current as of writing]
- Saltzer and Schroeder, "The Protection of Information in Computer Systems," 1975. https://web.mit.edu/Saltzer/www/publications/protection/
- Microsoft Research, "Guidelines for Human-AI Interaction," CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
- Bainbridge, "Ironies of Automation," 1983. https://doi.org/10.1016/0005-1098(83)90046-8
- NIST AI RMF 1.0, 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
- OWASP, "Top 10 for LLM Applications 2025." https://owasp.org/www-project-top-10-for-large-language-model-applications/
- VPI-Bench, "Visual Prompt Injection Attacks for Computer-Use Agents," arXiv, 2025/2026. https://arxiv.org/abs/2506.02456
- Anthropic, "Use plugins in Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13837440-use-plugins-in-cowork [verify — current as of writing]
