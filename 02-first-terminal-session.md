# Chapter 1 — Your First Terminal Session: Claude Code Is Not ChatGPT


## TL;DR

- This chapter gives a working overview of Your First Terminal Session: Claude Code Is Not ChatGPT, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What the agentic loop is, Installation, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> Claude Code runs in your terminal, reads your files, runs commands, and works autonomously across multiple steps. That autonomy is the feature — and the reason you need a discipline before you start.

---

## Learning outcomes

1. **(Understand)** Explain the agentic loop and how it differs from a chatbot conversation.
2. **(Apply)** Install Claude Code and run a first session in a project directory.
3. **(Understand)** Explain the context window constraint and why it is the primary resource to manage.

---

## Opening

The teacher opened their terminal for the first time with Claude Code running. The cursor blinked. They typed `claude` and pressed enter.

Claude opened the project folder. Read the existing files. Identified the framework (a basic HTML/CSS site, no build tool, three pages in `pages/`, a `styles.css` at the root). Proposed a structure that would let them add new pages without changing the existing ones. Asked which page to start with.

The teacher had told it nothing. It had read everything.

The teacher's instinct, from a year of using Claude.ai, was to type an answer to the question and let the build proceed. The discipline this chapter teaches is to do something else first: ask five questions before making any change.

Claude Code is not ChatGPT. The first session is for calibration — figuring out what Claude sees about your project, what it gets right, what it assumes that you need to correct. The temptation is to skip the calibration because Claude looks ready to build. The cost of skipping is a file system that no longer matches what you thought you were building, a dependency installed you did not know about, a config change that breaks a different project. The calibration is the protection.

This chapter is the first session. By the end you will have Claude Code installed, your first project's CLAUDE.md generated, plan mode demonstrated, and the discipline of asking-before-acting installed as a default. The build that follows in Chapter 3 assumes all of this.

![The agentic loop](images/02-first-terminal-session-fig-01.png)
*Figure 2.1 — The agentic loop*

---

## What the agentic loop is

Claude.ai is a turn-based conversation. You type. The model produces text. You read. You decide what to do.

Claude Code is an *agentic loop*. The model gathers context — reads files, runs read-only commands, examines the project state. It takes action — edits files, runs commands, calls APIs. It verifies results — checks whether the action did what was intended, often by running another read-only command. Then it loops: gather, act, verify, gather, act, verify. The whole loop can run for many iterations before reporting back to you.

This is what makes Claude Code useful. It is also what makes the discipline necessary. A model that produces text can produce wrong text; you read it, you reject it, no harm done. An agentic system that runs the loop can produce wrong actions — modified files, executed commands, changed state — before you have a chance to read the explanation. The model believes the action was correct; the loop continues; the consequences accumulate.

You have three primary interruption points.

**Esc** stops Claude mid-action. The current operation is interrupted. The loop pauses. You decide what to do next.

**Plan mode** (Shift+Tab twice) puts Claude in read-only mode. It can read files, propose plans, explain what it would do — but it cannot edit, run, or change anything until you switch out of plan mode. Use plan mode when you want to see the plan before approval; use it especially for any operation whose failure would be expensive.

**Handoff conditions** (Chapter 4) are the explicit gates between build steps. The loop's autonomy is bounded by conditions you write that must be true before the next step begins. Without them, the loop runs as far as Claude's context allows. With them, the loop pauses at every gate you specify.

The agentic loop is the feature. The interruption points are the discipline that makes the feature safe.

---

## Installation

The exact commands. Test them on your machine before reading further.

**On macOS / Linux:**

```bash
npm install -g @anthropic-ai/claude-code
```

This installs Claude Code globally. If you do not have Node.js, install it first (`brew install node` on macOS; the Node.js installer for Linux).

**On Windows:**

The same npm install command in PowerShell (or Windows Terminal). If you do not have Node.js, install from nodejs.org.

**To run:**

```bash
cd ~/your-project-folder
claude
```

You will be prompted to authenticate the first time. Follow the prompts (the docs at code.claude.com have the current authentication flow; it changes occasionally as the security model evolves).

**If you hit a school IT wall:** Your district may block npm installs or terminal commands on managed laptops. Two paths. First: ask IT — many districts have an approved process for development tools that just needs a request. Second: use a personal device for the book's builds and deploy the artifacts to school-managed devices via the school's standard deployment process (GitHub Pages, district CMS, etc.). The framework holds regardless of which path you take to your first session.

---

## /init: Claude's first read of your project

In a project directory, run:

```
/init
```

Claude reads the project. It identifies the framework, the structure, the existing conventions, the likely tooling. It generates a starter `CLAUDE.md` at the project root.

Open the generated file. Read it.

You will notice two things. First, Claude got most of the structure right. The file lists the framework, the directory layout, the build commands (if there are any). Second, Claude noticed things you had not told it — a naming convention you had not realized was a convention, an inconsistency in two of the files, a missing tooling piece. The `CLAUDE.md` is Claude's *first reading* of your project, written down.

The `CLAUDE.md` is editable. You will edit it across the next chapter and across every subsequent build. For now: read it. Notice what Claude saw. Notice what is missing. Do not yet add anything.

The `/init` is the starting point, not the finish. Chapter 2 makes it the constitution.

---

## Questions before code: five questions, no changes

The first session's discipline:

Before you ask Claude to change anything, ask five questions. Each question is a *probe* — Claude answers, you read, you calibrate your sense of what Claude can and cannot see, what it would do without further specification, where you would have to correct it.

Five questions for the class-website project from this chapter's opening:

1. *"What is the file structure of this project? List the directories and files at the top two levels, and tell me what you think each one is for."*
2. *"What styling approach does this project use? What CSS conventions do you see, and what would you assume if I asked you to add styles for a new page?"*
3. *"If I asked you to add a 'Contact' page to this site, what files would you create or modify? Do not make any changes yet."*
4. *"Are there any patterns in this project that look inconsistent — places where similar things are done differently in different files?"*
5. *"What is the one thing about this project that, if I do not tell you about it explicitly, you would probably get wrong on your first build attempt?"*

Read the answers. Some will be right. Some will be wrong in instructive ways — Claude will assume you use one CSS approach when you use another, or will propose a directory structure that contradicts the project's convention. These wrong answers are the calibration. You now know the places where you will need to be explicit in your specifications.

The five questions take 5–10 minutes. They save hours later. The session you start *after* the five questions is a session where Claude has the right context and you have the right expectations.

---

## Plan mode: the demonstration

Enter plan mode. On the keyboard: Shift+Tab twice. The CLI shows you are in plan mode (the prompt indicator changes).

Now ask Claude to *propose* a change without making it:

```
I want to add a Contact page to this site. Propose the file structure
and the content. Do not create any files yet.
```

Claude returns a plan. Read it. Notice what Claude assumed: where the page lives, what it links to, what styling it inherits, what content sections it has. Some assumptions are right; some are wrong; one or two will surprise you.

You have two options.

**Approve and switch out of plan mode** — Claude executes the plan as proposed.

**Edit the plan first.** Press Ctrl+G. The plan opens in your text editor. Edit it directly — remove a step, add a constraint, change a path. Save and return. The plan is now what *you* approved, not what Claude proposed. Switch out of plan mode; Claude executes the edited plan.

For the chapter's first session: edit the plan. Change at least one thing. Notice that the act of editing forces you to think about what you actually want, not just what Claude proposed. This is the conducting discipline in its smallest form.

Then switch out of plan mode. Let Claude execute the edited plan. Watch the agentic loop run — gather, act, verify. When it stops, check the result.

---

## The context window: your primary resource

The single most important constraint in Claude Code is the context window. Everything Claude knows in a session lives in the context window. As the session continues, the context fills with file reads, command outputs, the conversation history, and Claude's intermediate reasoning. When the window fills, performance degrades — Claude forgets earlier parts of the session, repeats work, makes errors it would not have made earlier.

Three commands manage the window.

**`/context`** shows what is currently using context space — how much is conversation history, how much is files read, how much is CLAUDE.md, how much is free. Run it when the session feels slow.

**`/clear`** wipes the context entirely. Use between unrelated tasks. Cheap and effective. The cost is that Claude will need the relevant CLAUDE.md sections re-pasted at the start of the next prompt — which is fine, because the CLAUDE.md (Chapter 2) is designed to be re-pasted.

**`/compact focus on X`** summarizes the conversation while preserving what matters (X). Use when you have built something complex and want to continue in the same session without losing the gist. The summarization is approximate; do not use compact when you need precise prior detail.

The discipline: treat the context window like a kitchen counter. Clear it when you are done with one dish before starting the next. Compact it when you need to keep working with a smaller workspace. The window is finite; the discipline is about staying inside it.

There is also `/rewind` — Esc, Esc — which restores Claude to a previous checkpoint, undoing both the conversation *and* the code changes made since that checkpoint. This is the discipline of Chapter 4 (handoff conditions): when a step fails, you do not "fix forward" — you rewind to the checkpoint before the failure, write a better specification, and re-run from clean state. The rewind is the gate's enforcement mechanism.

---

## Teacher build: the first session

Apply this chapter to your class-website project.

1. **Install Claude Code.** Run the npm install. Authenticate. Verify by running `claude --version`.

2. **Create the class-website folder.** `mkdir ~/teaching/class-website && cd ~/teaching/class-website`. If you already have a class website, `cd` into its folder instead.

3. **Run `claude` to start the session.** Then `/init`. Read the generated CLAUDE.md. Note three things Claude noticed that you had not told it.

4. **Ask the five questions above** (adapted to your project's specifics). Do not make any changes. Read the answers. Identify the one place where Claude's assumption is most clearly wrong for your project.

5. **Enter plan mode.** Propose one small change to the project — adding a single page, or changing the styling approach for one section. Read the plan. Edit it with Ctrl+G. Approve.

6. **Switch out of plan mode.** Let Claude execute. Check the result.

7. **`/context` to see what filled.** Note the proportions. Then `/clear` and verify the context resets.

The first session takes 30–45 minutes including reading. The artifacts are the `/init` CLAUDE.md, your edited plan, and the executed change. The discipline is the five questions and the plan-mode edit.

---

## Classroom activity: students run their first session

For your students' classroom:

Provide a starter project — a small repo with three or four files in a structure that matches a real project shape. The repo's purpose can be anything (a small website, a CSV processing script, a simple data visualization); the point is that students run Claude Code on something with real structure, not on a one-file demo.

Their constraints:

- Run `/init`. Read the generated CLAUDE.md. Note one thing Claude saw that they would not have written down themselves.
- Ask three questions about the codebase. Document Claude's answers.
- Make no changes in this first session. Submit a one-paragraph reflection: what surprised them about Claude's reading of the project?

The pedagogical move: students experience the agentic loop's capability without exercising its destructive power. The discipline of questions-before-code is installed before they have the option to skip it. The reflection paragraph is the assessable artifact; the surprise the students report is the diagnostic for whether the calibration worked.

This activity maps to the student book's Chapter 1. Your design of this activity is the worked example for that chapter when you teach it.

---

## Common misconceptions

**"Claude Code is just a better Claude.ai."** No. Agentic system, file access, command execution, autonomous multi-step loop. The autonomy is the feature; the discipline is what makes the feature safe.

**"I'll skip questions-before-code; I know what I want."** Sometimes true. Often not. The five-question session takes 10 minutes; the build it informs takes the rest of the afternoon. The economics favor the questions.

**"Installation problems mean this book isn't for me."** Friction, not exclusion. The book includes the troubleshooting paths; your district IT may need a request; a personal device works for the book's builds. The framework outlives the installation path.

**"Plan mode is overhead."** For routine work, sometimes. For first sessions, for destructive operations, for anything that touches a file you care about — plan mode is the cheapest insurance you can buy. The cost is 30 seconds. The benefit is the avoided rollback.

**"The context window is a developer concern; I'll just keep typing."** It is the single most important resource in your session. The session that fills the window without management produces error-prone output that is hard to fix because the errors compound on themselves. Manage the window from the first session.

---

## Exercises

1. **(Apply)** Install Claude Code. Run `/init` in a project folder. Read the generated CLAUDE.md and write three sentences naming specific things Claude noticed that you would not have written down.

2. **(Analyze)** Run Claude in plan mode. Ask it to propose adding a new page to your class website. Read the plan. Do not approve. List three assumptions Claude made that you would need to correct before approval.

3. **(Evaluate)** What is the single most important resource to manage in a Claude Code session? Write a one-paragraph answer using the chapter's vocabulary. Compare with a peer's answer.

---

## What would change my mind

The chapter's strong operational claim is that **questions-before-code in the first session materially reduces error rates in subsequent build sessions**. If a controlled comparison found no measurable difference between teachers who ran the five-question calibration and teachers who started with their first build prompt, the discipline becomes optional. The chapter would still recommend it for engagement; the urgency softens.

I expect the difference to be substantial because the calibration session surfaces exactly the assumptions that, left unsurfaced, produce the dangerous middle on the first build prompt.

---

## Still puzzling

- **The right scope for plan mode.** The chapter recommends plan mode for first sessions and destructive operations. Some practitioners use it for everything; some skip it for routine work. The working heuristic: use plan mode whenever the operation's failure would cost more than 30 seconds to recover from. Most teachers' classroom work meets that bar.

- **The `/init` quality variance.** Claude's first read of a project is usually good and sometimes thin. A `CLAUDE.md` that came back with three lines is usually the sign that the project's structure was not clear enough for Claude to infer conventions. The fix is to edit the file (Chapter 2) — Claude's first read becomes your second pass.

- **Cross-machine session management.** Most teachers work on multiple devices (home laptop, school desktop). Claude Code's session state lives in `~/.claude/`; the CLAUDE.md and project files live in your project directory and travel with the repo. Sync the repo with git; the session resumption pattern is covered in Chapter 9.

---

## Links

- [code.claude.com](https://code.claude.com) — official documentation; installation, commands, troubleshooting.
- [The Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md) — track what changes between editions.

---

##  AI Wayback Machine
 **Alan Kay** (born 1940) — American computer scientist whose 1972 Xerox PARC paper *"A Personal Computer for Children of All Ages"* introduced the concept of the **Dynabook** — a personal computer designed not as a faster typewriter but as a *medium for exploration*, a tool through which the user could *think about thinking*.[^1] Kay's argument, refined across forty years of work at Xerox PARC, Apple, and HP, was that the computer's deepest contribution would not be efficiency at predefined tasks but the *expansion of the human ability to formulate questions worth asking*.

The questions-before-code rule in this chapter is Kay applied to terminal AI. The first session is not the most efficient path to executing a build; it is the path that develops the teacher's *capacity to formulate the specifications* that the build requires. Claude Code is a Dynabook in Kay's sense — a medium through which the teacher learns what the project actually is by asking it questions, not by typing instructions to it. The five-question calibration is the medium being used as Kay intended it.

Kay's most quoted line — *"The best way to predict the future is to invent it"* — describes the agentic loop in a way Kay himself could not have foreseen. The teacher who runs `/init`, asks five questions, edits a plan in plan mode, and executes a small change has invented a small piece of their own classroom future. The next chapter (`CLAUDE.md`) is the artifact that captures the inventions and makes them durable across sessions.

---

## Bridge

The terminal session is no longer foreign. Chapter 2 gives you the file that makes every subsequent session smarter — `CLAUDE.md`, your project's persistent context.

---

[^1]: Kay, A. *A Personal Computer for Children of All Ages*. Xerox PARC Technical Report, August 1972. The paper's vision was elaborated in Kay, A. and Goldberg, A. *Personal Dynamic Media*. IEEE Computer 10, no. 3 (1977): 31–41.
