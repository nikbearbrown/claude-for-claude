# Chapter 11 — Prompts as Handoffs to Claude Code and Cowork

**Capability built:** Convert conversational prompts into action-ready specifications for agentic tools.

---

## Opening Scene

A communications manager wants to reorganize the folder of draft reports her team has been building for six months. Each report has a slightly different filename format, some are drafts, some are final, and the folder also contains a few files that were mistakenly placed there. She opens Claude Code and types: "Clean up this folder." Claude Code begins moving files. It renames three reports, deletes two that looked like duplicates, and moves four others to a subfolder it creates and names "archive." None of this is what she wanted. Some of it is not recoverable.

The problem is not that Claude Code is unreliable. The problem is that "clean up this folder" is not a work order. It is a wish. When a wish is handed to an agent that can actually execute file operations — move, rename, delete, create — the distance between what you meant and what you get becomes consequential.

This chapter is about the translation from conversational request to agentic specification. It is the most important chapter in this book for readers who use Claude Code or Claude Cowork, because the stakes of a misspecified handoff are higher than the stakes of a misspecified chat prompt. A chat prompt produces a draft you can discard. An agentic handoff can modify files, run code, and make changes that require real effort to reverse.

---

## What This Chapter Lets You Do

After this chapter you will be able to:

- Identify the components that every agentic handoff prompt must include
- Translate a vague conversational request into a bounded task brief
- Specify approval gates that pause execution before irreversible actions
- Write verification instructions that tell the agent how to confirm it did the right thing
- Design handoffs for readers who are not coders — folder workflows, document cleanup, draft generation — without requiring programming knowledge

The chapter focuses on Claude Code and Claude Cowork as the current agentic surfaces most accessible to working professionals. [verify — current as of writing] The principles are transferable to other agentic tools. The goal is not to make the reader a systems architect — it is to make the reader a clear enough specifier that the agent does the right thing and they can confirm that it did.

---

## Core Concepts

**Agentic tools act, not just answer.** Claude in a standard conversation produces text. Claude Code and Claude Cowork can execute: write and modify files, run terminal commands, interact with browsers, manage folder structures, and trigger downstream actions (Anthropic Claude Code overview). This is the fundamental difference that changes prompt design. A wrong answer in a chat conversation costs you time to detect and correct. A wrong action in an agentic workflow can delete a file you needed, commit code you did not review, or restructure a folder in a way that breaks a process downstream.

**The work order becomes a change request.** In software development, a change request specifies what to change, what not to change, what the expected outcome is, and how to verify the change succeeded. Agentic prompts need this structure. The Model Context Protocol (Anthropic MCP documentation) provides the technical foundation for agents to access and act on connected resources. That foundation only behaves as intended when the human has specified what the agent is authorized to do.

**Scope, authorization, and boundary.** Every agentic handoff must answer three questions: What is the workspace? What is the agent authorized to do? What must it not do? These are not formalities. Without a defined scope, an agent optimizing for "clean up this folder" will define "clean" however its training suggests — which may not match what you meant. Version control and change review best practices are built around the same logic: changes are specified, reviewed, and reversible by design.

**Approval gates.** An approval gate is a pause point built into the handoff that requires human confirmation before proceeding. "Before renaming any file, list the proposed renames and wait for my approval" is an approval gate. Human-in-the-loop AI literature consistently recommends approval gates for irreversible or high-impact actions (human-in-the-loop AI literature; NIST AI RMF). The number and placement of gates should be calibrated to the stakes: a draft generation handoff may need no gate; a file deletion handoff needs one before every deletion.

**Verification instructions.** Verification instructions tell the agent how to confirm the task was completed correctly. "After renaming the files, list every file in the folder with its new name and the rule that determined the rename" is a verification instruction. It does not guarantee correctness — you still have to read the list — but it forces the agent to produce an artifact you can inspect rather than returning only "done."

---

## Before / After Prompt Walkthrough

### The Before Prompt (conversational request)

> Clean up the reports folder. The filenames are inconsistent and there are some duplicates.

This request names the goal but specifies nothing that makes execution safe: no folder path, no definition of "inconsistent," no rule for identifying duplicates, no list of actions authorized, no actions prohibited, no approval gate before any irreversible step, and no verification requirement.

### The After Prompt (an agentic handoff)

> **Task:** Standardize the filenames in the reports folder. Do not delete any files. Do not move any files out of the folder.
>
> **Workspace:** The folder is `/Users/[name]/Documents/reports/`. Work only inside this folder.
>
> **Naming rule:** Rename files to this format: `YYYY-MM-DD_[report-title]_[status].docx` where status is either "draft" or "final." Use the date in the existing filename if present; if no date is in the filename, use the file's last-modified date.
>
> **Authorization:**
> - Rename files: Yes
> - Move files within the folder: No
> - Delete files: No
> - Create new folders: No
>
> **Before taking any action:** List every file in the folder, the current name, and the proposed new name. Wait for my approval before renaming anything.
>
> **Verification:** After I approve and you complete the renames, list every file with its new name and confirm no files were moved or deleted.
>
> **What to do if uncertain:** If a file does not match the naming rule clearly, flag it and ask rather than guessing.

What changed:

1. Scope is explicit — folder path, single folder only.
2. Authorization is explicit — rename yes, move no, delete no, new folders no.
3. The naming rule is specified — not "make consistent" but a defined format with a decision rule for missing dates.
4. An approval gate is built in before any action.
5. Verification is required after completion.
6. Uncertainty handling is specified — flag and ask, not guess.

The first prompt produces an action. The second prompt produces a plan you can review, a controlled action, and a verifiable record.

---

## The Human Gate: What You Must Verify

Agentic handoffs require active supervision at the gate and at the output. Passive review is not enough.

**Read the proposed action list before approving.** Approval gates only protect you if you actually read what is proposed. Skimming and approving is the same as not having a gate. Before approving a proposed list of renames, moves, or edits, read each one.

**Check the verification output against your expectation.** If the agent reports "all files renamed successfully," verify by opening the folder. If the agent lists 14 renamed files and you expected 17, find the three that are missing before closing the session.

**Check for unintended actions.** Agentic tools may take auxiliary actions not explicitly covered by the handoff. Check that only authorized actions were taken. If you see a new folder that was not authorized, investigate before proceeding.

**Maintain a backup before any handoff involving irreversible operations.** For any handoff that involves deletion, overwriting, or structural reorganization, create a backup before running the handoff. This is standard version control practice. The backup makes the approval gate genuinely protective rather than a gesture.

**Understand the limits of what you can verify.** Some agentic outputs are easy to inspect — a list of renamed files. Others are harder — code changes across many files. For complex agentic tasks, define the verification requirement in the handoff prompt and plan time to review it properly.

---

## Common Mistakes

**Writing a goal instead of a specification.** "Improve the organization of this folder" is a goal. A specification names the folder, defines the organization rule, lists authorized actions, and describes what improved looks like. Goals produce interpretation; specifications produce verifiable execution.

**Omitting the boundary.** Without a workspace boundary, an agent optimizing for a task may act outside the intended scope. Agentic AI risk literature identifies scope creep — agents taking actions in adjacent systems or files — as a recurring failure mode. Always name the workspace explicitly.

**Forgetting the prohibition list.** It is not enough to list what the agent should do. List explicitly what it must not do, especially for irreversible actions. "Do not delete any files" and "Do not modify files outside this folder" are not redundant — they are required.

**Setting approval gates too late.** An approval gate placed after a batch of actions is too late to prevent the actions. Gates must be placed before irreversible steps, not after them.

**Assuming "done" means "correct."** Agents report completion. Completion is not correctness. The verification step is not optional, and it is not the agent's responsibility — it is yours.

**Using agentic tools for tasks that are still underspecified.** If you do not yet know exactly what you want, use a conversation first. Chat out the specification, identify the edge cases, decide on the rules — then translate to a handoff prompt. Handing an underspecified task to an agent that will act on it is a way of making the underspecification consequential.

---

## From Chat to Handoff: The Translation Pattern

The translation from a conversational request to an agentic handoff follows a consistent pattern. Here it is as a template:

```
Task: [One-sentence description of what to accomplish]

Workspace: [Exact path or location; explicit scope boundary]

Input: [What files, data, or content the agent will work with]

Authorized actions: [List each action the agent may take]

Prohibited actions: [List each action the agent must not take]

Decision rule: [If there are choices to make — naming, classification, prioritization — state the rule]

Approval gate: [What to show you before acting, and when to wait]

Verification: [What report or artifact to produce after completion]

Uncertainty handling: [What to do when a case does not fit the rule — flag, skip, or ask]
```

Not every handoff requires every element. A draft generation handoff with no file operations may not need approval gates or a prohibition list. A file reorganization handoff with deletion risk needs all of them. Scale the specification to the stakes.

---

## Try This

**Exercise 1 — Translate a request.** Take a task you have considered doing with Claude Code or Cowork — or any agentic tool. Write the version you would have typed without this chapter (the conversational request). Then translate it using the template above. Compare the two. Identify which risks the translation addresses that the original request left open.

**Exercise 2 — Write an approval gate.** For a folder or document set you work with, write a handoff prompt that includes an explicit approval gate — a step where the agent must stop, list what it proposes to do, and wait for your confirmation. Run it [verify — current as of writing on the current interface for Claude Code/Cowork] and note whether the gate was respected and whether the list was clear enough to make a real approval decision.

**Exercise 3 — Design a non-coder handoff.** Write a handoff prompt for a task a working professional without coding knowledge might want to do: organize a folder, rename a set of documents, extract a list of items from a folder of PDFs, or convert a set of files to a new format. Focus on the boundary, authorization, gate, and verification components. Do not assume coding knowledge is required — the specification discipline applies to any agentic task.

---

## What Would Change My Mind

This chapter treats agentic handoffs as requiring more specification than chat prompts, with approval gates as a near-universal recommendation for irreversible actions. That position rests on the current state of agentic AI reliability and the current maturity of approval-gate interfaces.

If agentic tools developed reliable intent-checking — if Claude Code could, with high accuracy, infer the full specification from a short request and surface it for confirmation before acting — the front-loaded specification requirement would loosen. The human judgment would move from specification to confirmation of the inferred specification, which is easier. [verify — current as of writing]

The NIST AI RMF recommends human-in-the-loop controls at high-stakes decision points. That recommendation is calibrated to current AI reliability, not permanent AI limitations. As reliability evidence develops, the calibration should update.

---

## Still Puzzling

The chapter focuses on file and document tasks — the most common agentic use cases for the book's audience of working professionals. There is a separate and harder question about multi-step agentic workflows in which Claude Code operates autonomously across many steps before a review point. The specification discipline is the same, but the granularity of gate design becomes more complex. A future revision should address this for readers whose work involves longer autonomous task chains.

There is also an open question about non-coders and Claude Code. The current Claude Code interface assumes some comfort with terminal commands and file paths. [verify — current as of writing] Working professionals who are not developers may find the interface itself a barrier before the specification challenge even arises. A companion resource for non-coders using Claude Code safely — focused on the prompting discipline rather than the interface — is a gap this chapter points toward but does not fill.

---

## Bridge

This chapter completes the applied arc — writing, research, analysis, learning, and agentic handoffs. What runs through all of them is the same idea the book opened with: a prompt is a work order, and the quality of the work order determines whether the output is supervisable, correctable, and worth acting on. The final two chapters turn that discipline into something durable: a reusable prompt library and a capstone workflow the reader designs, tests, and keeps.

---

## Sources Used

- Anthropic, Claude Code overview. https://docs.anthropic.com/en/docs/claude-code/overview
- Anthropic Claude documentation. https://docs.anthropic.com/
- Anthropic prompt engineering documentation. https://docs.anthropic.com/
- Anthropic Model Context Protocol documentation
- Human-in-the-loop AI literature
- Software requirements and acceptance criteria practice
- NIST AI Risk Management Framework
- Agentic AI risk and evaluation literature
- Documentation and workflow design literature
- Version control and change review best practices
