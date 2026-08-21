# Chapter 2 — CLAUDE.md: Your Coding Constitution


## TL;DR

- This chapter gives a working overview of CLAUDE.md: Your Coding Constitution, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What CLAUDE.md is, What goes in: the five elements, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> CLAUDE.md is the file Claude reads at the start of every session. It is the difference between a Claude that knows your project and a Claude that guesses.

---

## Learning outcomes

1. **(Understand)** Explain what CLAUDE.md is, where it lives, and when Claude reads it.
2. **(Apply)** Write a CLAUDE.md for your class-website project using the five-element format.
3. **(Analyze)** Distinguish CLAUDE.md content (always-on rules) from Skills (on-demand) and Hooks (deterministic enforcement).

---

## Opening

The teacher started their second Claude Code session for the class-website project. They had the `/init`-generated CLAUDE.md from Chapter 1 sitting in the project root. They had not edited it yet.

They asked Claude to add a Contact page to the site.

Claude opened the existing pages, read the structure, proposed a Contact page with a form and JavaScript validation. The form posted to `/api/contact` — an endpoint the teacher's static-HTML school server did not have, and would never have, because the school's mod_rewrite was old and the deployment was a flat rsync. Claude could not have known this. The starter CLAUDE.md had not said.

The teacher pressed Esc, opened CLAUDE.md, and added one line:

> *School server: static-HTML only. No backend, no `/api/*` endpoints, no form processing. Contact forms must be `mailto:` links.*

They restarted the session. They asked the same question. Claude proposed a Contact page with a `mailto:` link and a short note about expected response time. No backend. No assumption that did not match the deployment.

One line in CLAUDE.md. The whole subsequent session was different.

This is the chapter. CLAUDE.md is the file you write so that Claude does not need to be told the same thing every session. It is automatic — loaded at session start, in scope for every prompt, costing nothing per invocation once written. The difference between a teacher who uses Claude Code well and a teacher who fights it is, in practice, the difference between a maintained CLAUDE.md and an abandoned one.

![CLAUDE](images/03-claude-md-fig-01.png)
*Figure 3.1 — CLAUDE*

---

## What CLAUDE.md is

A markdown file. Lives in your project root (or in `.claude/CLAUDE.md` for project-scoped Claude conventions). Claude Code reads it automatically at session start. Every prompt in that session operates with the file's contents in Claude's context.

There is a hierarchy. Claude reads, in order:

1. Project-root `CLAUDE.md` and `.claude/CLAUDE.md` — project-specific instructions.
2. User-level `~/.claude/CLAUDE.md` — your personal preferences across all projects.
3. Managed policy locations (for districts and organizations that enforce policies) — overrides that apply across all users on the device.

The order matters because closer-to-the-project entries take precedence. Your project CLAUDE.md says "this site uses vanilla CSS, no Tailwind." Your user CLAUDE.md says "I prefer Tailwind by default." The project wins, because the project is closer.

You do not need to think about all three tiers from day one. Most teachers, most of the time, work with project-root CLAUDE.md only. The user-level and managed locations matter when you have multiple projects (you will, after one semester) or when your district wants enforcement (rare; useful when it happens).

---

## What goes in: the five elements

The CLAUDE.md format is not a rigid template; it is a set of categories that reliably matter. Include in your CLAUDE.md the content that fits these categories. Exclude content that does not.

**1. Bash commands Claude cannot guess.** Your deployment command. Your test runner. The non-standard linter you use. The local development server's start command. If Claude would have to read your `package.json` or your README to find the command, it goes in CLAUDE.md. Three to seven entries is typical.

**2. Code style deviations from the norm.** Standard conventions (camelCase variables, two-space indent, single quotes for strings) do *not* belong here — Claude knows the standards. What belongs: the places where your project deviates. *"We use semantic HTML5 elements only; no nested divs for layout."* *"Vanilla CSS only — no Tailwind, no preprocessor, no PostCSS plugins."* *"JavaScript: ES modules, no bundler, no framework."* The deviations are short and specific.

**3. Test runners and verification commands.** What command verifies a step is done? *"After any HTML change, run `npm run lint-html` (checks links, accessibility)."* *"After any CSS change, manual visual check on mobile breakpoint at 375px."* If verification is automated, Claude needs the command. If it is manual, Claude needs to know what to remind you to check.

**4. Architectural decisions.** The structural choices that govern the project. *"Each page is a self-contained file. Shared components are template literals injected at build, not iframes."* *"No JS framework. The site must work with JavaScript disabled."* These are the decisions Claude would otherwise re-decide on every prompt.

**5. Environment quirks.** The specific things about your environment that no general-purpose model could know. The school server's old mod_rewrite from the opening. *"All paths end in `.html` explicitly. Never use trailing slashes for canonical URLs."* *"The school network blocks `*.cloudfront.net` — fonts must be self-hosted."* *"Personal Chrome profile lives at `~/Library/Application Support/Google/Chrome/Default/`; never modify."* The quirks are the protection against the dangerous middle.

A CLAUDE.md with five to fifteen entries across these categories is typical. The 200-line guideline is the soft cap — beyond that, Claude starts to ignore the actual instructions because the file is too noisy.

---

## What stays out

The other side of the same heuristic.

**Anything Claude can figure out from the code.** The file structure that is visible from `ls`. The framework that is obvious from `package.json`. The naming convention that is consistent across the existing files. Including these in CLAUDE.md wastes space and trains your future self to add to a file that nobody reads.

**Standard conventions.** Claude knows that React components are PascalCase, that Python uses snake_case, that Markdown headings start with `#`. Including these is noise.

**Anything that changes frequently.** A list of "current sprint goals" or "this week's priorities" does not belong in CLAUDE.md. The file is for things that hold across sessions, not for the volatile state of the day. Volatile state goes in a `TODO.md` or in your project management tool, not in the file Claude reads at session start.

**Secrets and credentials.** Never in CLAUDE.md. The file gets committed to git; if you need credentials, they live in `.env` (git-ignored) and CLAUDE.md tells Claude *that* there is a `.env` and what variables it should contain — not the values.

**Personal notes unrelated to the project.** Your reminder to call the dentist. Your weekend plans. Your speculative notes about a future project. These belong in a different file. CLAUDE.md is for Claude.

| Item | Meaning |
| --- | --- |
| CLAUDE.md include | exclude |
| code style deviations | A concrete checkpoint for applying the chapter concept. |
| test runners | A concrete checkpoint for applying the chapter concept. |
| architectural decisions | A concrete checkpoint for applying the chapter concept. |
| environment quirks. Exclude: what Claude can figure out from code | A concrete checkpoint for applying the chapter concept. |
| standard conventions | A concrete checkpoint for applying the chapter concept. |
| constantly changing state | A concrete checkpoint for applying the chapter concept. |
| secrets and credentials | A concrete checkpoint for applying the chapter concept. |
| personal notes. No color. | A concrete checkpoint for applying the chapter concept. |

---

## Refining the /init starter

Most CLAUDE.md files start with `/init`. The discipline is what you do next.

Open the generated file. Read it section by section. For each entry:

- **Is it still true?** /init reads the codebase at one moment; the codebase changes; an entry that was right last week may be wrong this week.
- **Is it specific enough?** /init produces generalities. *"Uses CSS"* is too vague; *"Vanilla CSS, no Tailwind, no preprocessor, no PostCSS plugins"* is specific. Tighten.
- **Is it on the right side of the include/exclude line?** /init sometimes includes content Claude can already figure out from the code. Cut.
- **Is anything missing?** The environment quirks from your project, the architectural decisions you have made, the lessons learned from the first session — these are not in /init's output by default. You add them.

The refinement takes 15–30 minutes for the first pass. The file becomes meaningfully *yours* — the specific knowledge that Claude needs in order to work on your project well.

Commit the file to git. CLAUDE.md is part of the project's source. The next collaborator (a co-teacher, a future-you) reads it to understand the project's conventions. The git history of CLAUDE.md becomes the project's accumulated discipline.

---

## A worked example: CLAUDE.md for a class website

For the class-website project, after refining the /init starter:

```markdown
# CLAUDE.md — CS 101 Class Website

## Stack and build

Static HTML/CSS/JS. No build step. Files live in `src/`.
Deploy with `npm run deploy` (rsync to school server).
Local dev: `npm run dev` (serves `src/` on http://localhost:8080).

## Code style

- Vanilla CSS only. No Tailwind, no preprocessor, no PostCSS plugins.
- Plain JavaScript with ES modules. No bundler. No framework.
- Semantic HTML5 only. No nested divs for layout — use main, section, article, nav.
- Two-space indent throughout. Single quotes for JS strings; double for HTML attrs.

## Test/verification

- After any HTML change: run `npm run lint-html` (htmlhint; checks links, accessibility).
- After any CSS change: manual visual check on mobile breakpoint at 375px width.
- After any JS change: run `npm test` (Node-based unit tests for any helpers).

## Architectural decisions

- Each page is a self-contained HTML file. Shared components are template
  literals in `src/shared/`, injected at build (sed-replaced via the deploy script).
- The site must work with JavaScript disabled — JS adds enhancements only.
- All assets self-hosted under `src/assets/`. No CDN dependencies.

## Environment quirks — school server

- School server runs outdated mod_rewrite. All paths end in `.html` explicitly.
  Never use trailing slashes for canonical URLs.
- School network blocks `*.cloudfront.net` and `googleapis.com/fonts/*`.
  Fonts must be self-hosted.
- Contact forms: `mailto:` only. No backend; no `/api/*` endpoints.

## Never rules

- Never modify files under `~/teaching/grading/` (separate project; FERPA).
- Never delete files outside `src/` and `dist/`.
- Never push to `main` without `npm run lint-html` passing.

## Lessons learned

- 2026-02-04: Claude proposed a `/api/contact` endpoint; the school server has
  no backend. Added the static-HTML constraint above.
- 2026-02-11: Claude used Tailwind classes in a generated component despite
  the code-style entry. Restating the constraint in the prompt fixed it;
  for stronger enforcement see Skills (Ch 6) and Hooks (Ch 7).
```

Roughly 50 lines. Specific enough to govern Claude's behavior; short enough to read in 30 seconds at session start. The Lessons Learned section grows by one entry every week or two; the rest of the file holds across the project's life.

---

## CLAUDE.md vs. Skills vs. Hooks

A first preview of the three-way distinction. Full treatment in Chapters 6 (Skills) and 7 (Hooks).

**CLAUDE.md — advisory, always-on.** Loaded at session start. Claude reads it and tries to follow it. The compliance is *probabilistic* — Claude may sometimes generate code that violates a CLAUDE.md rule, especially under prompt pressure. The Tailwind example in Lessons Learned above is the recurring pattern.

**Skills — on-demand, invoked.** A SKILL.md file in `.claude/skills/`. Not loaded at session start. Activated when you (or Claude, with auto-invocation enabled) call the skill by name. Used for *workflows* that you do repeatedly: a grading workflow, a lesson-plan generator, a rubric builder. The skill's content lives in your context only when the skill is active, which keeps CLAUDE.md lean.

**Hooks — deterministic, enforced.** A script that runs at specific lifecycle events (PreToolUse, PostToolUse, SessionStart, SessionEnd). The hook executes regardless of what Claude decides. If you want a rule that *must* hold every time — "never generate a numeric grade," "always run linter after a file edit" — the rule is a hook, not a CLAUDE.md entry. The hook turns "should not" into "cannot."

The heuristic for which to use:

- *Must Claude know this every session, but can violate it occasionally without catastrophe?* → CLAUDE.md.
- *Is this a workflow Claude runs sometimes, and I want the workflow's specifics out of the always-on context?* → Skill.
- *Must this rule hold every time without exception?* → Hook.

A maintained Claude Code project usually has all three. CLAUDE.md establishes the project's constitution. Skills encode the repeated workflows. Hooks enforce the inviolable rules. Chapter 6 and Chapter 7 walk through the second and third in detail.

---

## /memory: the tour

Claude Code includes a `/memory` command that lists all CLAUDE.md files currently loaded in your session. Useful when you have multiple files (project + user + managed) and want to see exactly what is in scope. Run it once in your first session to understand the hierarchy.

`/memory` also lets you edit the files in place. You can update an entry, save, and the change is reflected in the next prompt without restarting the session. This is the fastest way to add a Lessons Learned entry without breaking your flow — type `/memory`, add the line, save, return to the build.

There is also an *auto memory* feature that Claude can be configured to use — Claude accumulates short notes across sessions automatically. This is useful for some workflows; it competes with the discipline of writing CLAUDE.md entries deliberately. The chapter's recommendation: keep the auto memory feature off until you have habituated CLAUDE.md maintenance. Once the manual discipline is reflex, the auto memory becomes a useful addition rather than a substitute.

---

## Teacher build: write your class-website CLAUDE.md

Apply the chapter's discipline to your project.

1. **Open `/init`'s output.** Read top to bottom.
2. **Refine each entry.** Cut what Claude can figure out from code. Tighten the generalities. Move volatile content out.
3. **Add the missing categories.** Architectural decisions you have made; environment quirks for your school; the never-rules from your own experience.
4. **Add a Lessons Learned section.** Initially empty or with one entry. It will grow.
5. **Verify under 200 lines.** If longer, audit for what should move to a Skill (Chapter 6).
6. **Commit to git.** `git add CLAUDE.md && git commit -m "Project CLAUDE.md: initial refinement from /init"`. The file is now part of the project's source.
7. **Run a test session.** Ask Claude to add a small feature to the site. Observe whether Claude's output reflects the CLAUDE.md entries. Where it does not, refine.

The full pass takes 45–60 minutes the first time. Subsequent refinements are 5–15 minutes per Lessons Learned entry. The file pays back across every session.

---

## Classroom activity: students write their own CLAUDE.md

For your students' classroom:

Each student opens a provided starter project (a small website, a script, a data tool — anything with real structure). They run `/init` and read the generated file. Then they:

- Refine it: cut three entries Claude could figure out from the code; tighten two vague entries with specifics.
- Add three entries Claude cannot infer: an architectural decision, an environment quirk, a never-rule.
- Run a test session: ask Claude to add a feature. Observe whether the output respects the refined CLAUDE.md.
- Submit: the refined CLAUDE.md, plus a one-paragraph reflection on which entries Claude respected and which it ignored.

The pedagogical move: students experience the include/exclude discipline directly. After one round, they see that CLAUDE.md is for *Claude*, not for documentation, and that the most valuable entries are the ones Claude could not have inferred. The peer-review step is where the disciplines compound — different students caught different categories of inference.

This activity maps to the student book's Chapter 6 (CLAUDE.md). Your design here is the worked example for that chapter.

---

## Common misconceptions

**"CLAUDE.md is for documentation."** It is for *Claude*. Written by humans, read by Claude. Documentation for humans goes in README.md.

**"More content is better."** No. The 200-line guideline exists because Claude's instruction-following degrades on long context files. A maintained 50-line CLAUDE.md beats an abandoned 500-line one.

**"I'll write CLAUDE.md when the project is bigger."** Now. Even a three-page class website benefits — the environment quirks (school server, no CDNs, mailto-only) compound across every prompt.

**"/init is enough."** /init is the starter. Refinement is the discipline. The /init output captures the codebase's surface; the refinement encodes your decisions and your environment.

**"My CLAUDE.md is too personal to commit."** Project conventions belong in the project repo. Personal preferences (your default Tailwind preference; your editor settings) belong in `~/.claude/CLAUDE.md` — your user file, not the project's. The hierarchy lets you have both.

---

## Exercises

1. **(Apply)** Write a CLAUDE.md for your class-website project. Start with `/init`. Refine to fewer than 200 lines. Commit to git.

2. **(Analyze)** Run Claude on a small task with your CLAUDE.md in place, then again with the file moved out of scope. Document three specific behavioral differences.

3. **(Evaluate)** After one week of using the CLAUDE.md: identify one entry Claude is ignoring. Why is it being ignored — is it too vague, contradicted by another entry, or is the right form a Skill (Ch 6) or a Hook (Ch 7) instead?

---

## What would change my mind

The chapter's strong claim is that **a maintained CLAUDE.md materially improves Claude Code's behavior on subsequent prompts** compared to working without one. If a controlled comparison found no measurable difference in error rates between sessions with and without CLAUDE.md, the file becomes optional. The chapter would still recommend it for cross-session memory (Chapter 9); the per-session value softens.

I expect the difference to be substantial because the chapter-opening failure mode (Claude proposing a backend endpoint for a static site) is exactly the failure CLAUDE.md was designed to prevent, and it is the modal failure mode of session-by-session work without persistent context.

---

## Still puzzling

- **The CLAUDE.md / Skill boundary.** Some workflows fit either container. The working heuristic: if it must be present in every session, CLAUDE.md; if it is only needed for a specific workflow, Skill. The line drifts with project maturity.

- **Auto memory's role.** Useful for some teachers; competes with the manual discipline. The chapter's recommendation is to start with manual; revisit auto after the habit is in place.

- **District-managed CLAUDE.md.** Some districts may want device-wide entries (allowed domains, prohibited operations). The managed-policy CLAUDE.md location is the mechanism; full deployment guidance is out of scope here. If your district is considering this, the canonical reference is at code.claude.com.

---

## Links

- [Claude Code CLAUDE.md documentation](https://code.claude.com/docs) — hierarchical discovery, /init, /memory.
- [The /memory command reference](https://code.claude.com/docs) — browse and edit loaded files.

---

##  AI Wayback Machine
 **Donald Knuth** (born 1938) — American computer scientist whose 1984 *Computer Journal* paper *"Literate Programming"* introduced the radical idea that **a program is a document written for a human reader, with the executable code as a byproduct of the explanation**.[^1] Knuth's argument was that the conventional separation between code (for the machine) and documentation (for the human, written afterward, often not at all) was backwards. The right form was integrated: the program's explanation and its executable form, woven together, with the explanation primary.

CLAUDE.md is literate programming inverted for the AI era. Where Knuth's literate programs explained the code to the human reader, the CLAUDE.md explains the project's conventions to the AI reader — and to the human reader who comes next. The two readers receive the same text. The conventions that matter to Claude (the never-rules, the environment quirks, the architectural decisions) are exactly the conventions that matter to the next teacher who picks up the project. The artifact serves both audiences without translation.

Knuth's deeper insight was about *what counts as the program*. In the conventional view, the program is the executable; the documentation is meta. In the literate view, the program is the *understanding* of what is being built; the executable is an artifact derived from the understanding. The CLAUDE.md works the same way. The project's discipline lives in CLAUDE.md; the actual files in `src/` are derived from the discipline. Two collaborators (you and Claude, or you and another teacher) can converge on consistent outputs because the discipline is the same shared text. Forty years after Knuth, the principle scales without modification.

---

## Bridge

The constitution is written. Chapter 3 teaches you to write the prompts that operate within it — specifications, not requests.

---

[^1]: Knuth, D. E. "Literate Programming." *The Computer Journal* 27, no. 2 (1984): 97–111. The paper is open-access via the *Computer Journal* archive and Knuth's Stanford page.
