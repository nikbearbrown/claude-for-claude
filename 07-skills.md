# Chapter 6 — Skills: Build Once, Use Every Semester


## TL;DR

- This chapter gives a working overview of Skills: Build Once, Use Every Semester, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What a Skill is, Workflow, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> A Skill is a CLAUDE.md file for a specific task — a lesson-plan generator, a rubric builder, a grading workflow. Build it once. Invoke it with `/skill-name` every semester after that.

---

## Learning outcomes

1. **(Understand)** Explain what a Skill is and how it differs from CLAUDE.md (always-on) and Hooks (enforcement).
2. **(Apply)** Build a lesson-plan-generator skill and a grading-workflow skill using the SKILL.md format.
3. **(Apply)** Invoke a skill with `/skill-name` and configure automatic vs. explicit invocation.

---

## Opening

The teacher had written the same grading-workflow specification for the third time.

Different assignment. Different week. Same workflow: read submissions, check for required sections, flag patterns, output per-student flags — never generate a final grade, never write directly to the student. The third time, the spec was 280 words long and took 18 minutes to compose. Claude executed cleanly. The drafts came out right.

The teacher closed the session. They knew they would be writing the same specification in two weeks, for the next assignment. They would write it again in November, in January, in March. Every grading cycle. Eighteen minutes each time, plus the cognitive cost of re-deciding the same things.

Then they remembered that Claude Code has Skills.

A Skill is a markdown file in `.claude/skills/<name>/SKILL.md` that captures a workflow Claude can re-execute on invocation. The workflow lives in the file once; you invoke it with `/skill-name` whenever you need it. The teacher wrote the grading workflow into `.claude/skills/grading-workflow/SKILL.md`, refined for an hour, committed to git. From that point forward: 90 seconds to invoke. The same workflow ran. The grading cycle's startup cost dropped from 18 minutes to 90 seconds.

This is the chapter. Skills are where the conducting discipline becomes *reusable across semesters*. Every workflow you would otherwise re-specify is a candidate. The chapter teaches the file format, the invocation, the boundary with CLAUDE.md (Chapter 2) and Hooks (Chapter 7), and the catalog of skills most teachers will want by the end of the first semester.

![Skills vs](images/07-skills-fig-01.png)
*Figure 7.1 — Skills vs*

---

## What a Skill is

A Skill is a `SKILL.md` file in `.claude/skills/<skill-name>/` at your project root (or in `~/.claude/skills/` for user-level skills shared across projects).

The file has YAML frontmatter and a body. The frontmatter declares the skill's name, description, and a few configuration flags. The body is the workflow Claude follows when the skill is invoked.

Minimal example, for a *grading-workflow* skill:

```markdown
---
name: grading-workflow
description: Process student submissions, flag patterns, produce per-student flag reports; never generate final grades or write directly to students.
disable-model-invocation: false
---

## Workflow

1. Read submissions from `submissions/<assignment>/` (one directory per student).
2. For each submission, check the presence of required sections from `rubric.md`.
3. Run pattern detection across all submissions: missing docstrings, common misconceptions, code-style outliers.
4. Output a per-student flag report (not graded feedback) to `grading/flags/<student>.md`.
5. Output a cohort pattern report to `grading/patterns.md`.

## Never

- Generate a final letter grade or numeric grade.
- Write feedback that addresses the student directly ("you should...").
- Modify files in `submissions/`.

## Verification

- After running, count: should produce one flag report per student plus one cohort pattern report.
- The cohort pattern report names at least three patterns, not just occurrence counts.
- No file in `grading/` contains a numeric score.
```

To run: type `/grading-workflow` in Claude Code. Claude reads the SKILL.md, executes the workflow, writes the artifacts. The session continues; the skill's content stays in scope until the session ends.

The file is checked into git. Next semester: same skill, same invocation, same workflow.

---

## Skills vs. CLAUDE.md: the boundary

Both are markdown files Claude reads. The difference is *when* Claude reads them.

**CLAUDE.md loads at session start.** Every prompt in the session operates with CLAUDE.md in context. Use CLAUDE.md for what *must* be true across all work in this project: the project's stack, code style deviations, environment quirks, never-rules.

**Skills load on invocation.** A Skill's content does not occupy context until you (or Claude, with auto-invocation enabled) call it. Use Skills for *workflows* — the multi-step procedures you do repeatedly: the grading workflow, the lesson-plan generator, the rubric builder.

The heuristic: *must this be true every session, regardless of what I am doing?* CLAUDE.md. *Is this a workflow I run sometimes, that has its own specific rules?* Skill.

A maintained project has both. The grading project's CLAUDE.md says *"submissions are read-only; grading output goes to `grading/`."* The grading project's `grading-workflow` skill says *"the workflow has five steps; here they are in order; never generate a final grade."* CLAUDE.md is the constitution; Skills are the named procedures.

Context cost is the practical reason for the distinction. CLAUDE.md content occupies context for every prompt in the session — useful for what every prompt needs, expensive for content that only matters during one specific workflow. Skills cost (small) context only when their description loads at session start; the full content costs only on invocation. A project with five workflows benefits from putting the workflow specifics in Skills rather than in a 500-line CLAUDE.md that bloats every prompt.

---

## The two invocation modes

Skills can be invoked two ways. The flag in the frontmatter controls which.

**`disable-model-invocation: false` (default).** Claude can decide to invoke the skill automatically when it judges the skill is relevant to the current prompt. Useful for skills that are uncontroversial when invoked — a lesson-plan generator, a rubric builder. The risk: Claude invokes the skill when you did not intend.

**`disable-model-invocation: true`.** Claude cannot auto-invoke the skill. The only way to run it is for you to type `/skill-name` explicitly. Use for skills with side effects you want explicit control over — a deploy workflow, a database migration, a grading-workflow that writes to many files. The cost: you must remember to invoke. The benefit: no surprise side effects.

For teacher work, the working heuristic: skills that produce *artifacts in directories you care about* (grading, deploy, build) → `disable-model-invocation: true`. Skills that produce *content in the current prompt's response* (lesson plan draft, rubric, exit ticket) → `disable-model-invocation: false`.

---

## The teacher skill catalog

The skills most teachers will want by the end of one semester:

| Skill | What it does | When | disable-model-invocation |
|---|---|---|---|
| `lesson-plan-generator` | Produce a draft lesson plan from learning objectives | Planning week | false |
| `rubric-builder` | Generate a draft rubric from an assignment description | Assignment design | false |
| `grading-workflow` | Flag patterns across submissions; produce per-student flag reports | Grading day | **true** |
| `differentiation-helper` | Suggest differentiation moves for a lesson plan | Lesson refinement | false |
| `feedback-generator` | Draft feedback for a single submission (first draft only) | Per-submission grading | **true** |
| `exit-ticket` | Generate an exit-ticket prompt from a lesson's objectives | End of lesson | false |

The three with `disable-model-invocation: true` are the ones with side effects on files — they should run only when you explicitly invoke them. The rest are content-generation that Claude can auto-invoke without surprise.

Six skills, built across the first semester, cover most teacher grading and planning workflows. Each one's first build takes 45–90 minutes. Each one pays back hours per semester after that.

---

## Writing your first skill: the lesson-plan generator

A walkthrough of building one skill from blank file to checked-in artifact.

**Step 1: name and locate.** Create `.claude/skills/lesson-plan-generator/SKILL.md` at your project root (or user-level path for cross-project use).

**Step 2: frontmatter.** Name, description, invocation flag.

```yaml
---
name: lesson-plan-generator
description: Produce a draft lesson plan from a set of learning objectives, target grade level, and class duration. Output as a structured markdown document.
disable-model-invocation: false
---
```

**Step 3: body.** The workflow Claude follows.

```markdown
## Inputs

Required, ask user if not provided:
- Learning objectives (1–4 specific objectives, ideally Bloom-tagged)
- Grade level (e.g., 9th-grade CS)
- Class duration (e.g., 50 minutes)
- Subject area (CS, Earth science, etc.)

## Workflow

1. Confirm the inputs (echo back to user; ask for confirmation before proceeding).
2. Compose the lesson plan with these sections:
   - Objectives (numbered; copy verbatim from input)
   - Opening hook (5 minutes; concrete, classroom-ready)
   - Direct instruction (10–15 minutes; sketch; not full script)
   - Practice (15–20 minutes; specific activities; differentiation noted)
   - Closure / exit ticket (5 minutes; concrete prompt)
   - Materials list
3. Output as markdown; save to `lesson-plans/<date>-<topic-slug>.md`.

## Constraints

- Use plain language; no jargon.
- Activities must be classroom-ready; if equipment is needed, name it.
- Differentiation must specify at least one ELL move and one move for advanced students.
- The exit ticket must be assessable in under 2 minutes per student.

## Never

- Generate content for a topic without an explicit objective input.
- Use generic activities ("students will discuss"); name the specific protocol.
- Omit the differentiation section.
```

**Step 4: commit.** `git add .claude/skills/lesson-plan-generator/SKILL.md && git commit -m "Skill: lesson-plan-generator"`. The skill is now part of the project's source.

**Step 5: invoke.** In Claude Code: `/lesson-plan-generator`. Claude reads the skill, asks for the inputs (per Step 1 of the workflow), executes the workflow, writes the lesson plan to `lesson-plans/`.

**Step 6: refine after first use.** The first invocation will surface something — a section the output is weak at, an input you forgot to ask for, a constraint you wish were tighter. Edit `SKILL.md`. Commit the change. The next invocation uses the refined version.

The first build of a skill takes 45–90 minutes. The refinement after the first use takes 15 minutes. The skill stabilizes after two or three uses. From that point it is a 90-second invocation that produces a 30-minute artifact.

---

## Skills vs. Hooks — preview

Hooks are Chapter 7's full topic. The preview here:

A Skill's constraints (the *Never* section above) are *advisory* — Claude reads them and tries to follow them. The compliance is high but not absolute. If a Skill says *"Never generate a final grade"* and you tell Claude to generate a final grade, Claude *may* comply with your instruction even though the Skill prohibits it.

A Hook is *deterministic*. A hook that blocks output containing a numeric grade runs regardless of what Claude decides. The skill says *"should not"*; the hook makes it *"cannot"*.

The right pattern: encode the workflow in the Skill, encode the inviolable rules in Hooks. For the grading-workflow skill above, the *Never* rules are reinforced by a Hook (Chapter 7) that blocks any file write to `grading/` containing a numeric grade pattern. The Skill explains the workflow; the Hook enforces the invariant.

---

## Hooks-in-skills (the 2026 affordance)

A recent Claude Code feature: hooks can be defined inside a skill's frontmatter, scoped to that skill's activation.

```yaml
---
name: grading-workflow
description: ...
disable-model-invocation: true
hooks:
  PostToolUse:
    - command: "scripts/check-no-grades.sh"
      paths: ["grading/**"]
---
```

When the skill is active, the hook runs after each tool use that writes to `grading/`. The hook's script (`scripts/check-no-grades.sh`) checks for numeric grade patterns and aborts if found. When the skill is not active, the hook does not run.

This pattern is the cleanest way to bind enforcement to a specific workflow without polluting the project's always-on Hooks. Chapter 7 walks through Hook authoring in detail; the preview here is that Skills and Hooks compose well in 2026.

---

## Teacher build: build your first two skills

Apply the chapter's discipline to your grading-tool prep from Chapter 5.

**Skill 1: `lesson-plan-generator`.** Use the walkthrough above. Build, refine after first use, commit.

**Skill 2: `grading-workflow`.** Use the worked example from earlier in this chapter as the starting point. Adapt to your assignment's specifics. Set `disable-model-invocation: true`. Build, refine after first use, commit.

After both are built, run each at least once. Note in CLAUDE.md any lesson learned during the first invocation. The Skills will continue to be refined across the semester; the first version is the start, not the end.

---

## Classroom activity: students build a skill for a repeated task

For your students' classroom:

Each student identifies a *task they do repeatedly* in a project they are working on — generating a test case structure, formatting a data analysis report, scaffolding a new feature. Repeated work in any direction.

They write the task as a SKILL.md:

- Frontmatter (name, description, invocation mode).
- Body with workflow steps, constraints, never-rules, verification.

They invoke the skill once on a real instance of the repeated task. They refine the skill after the first invocation. They commit.

Pair students. Each peer reviews the other's skill: is the workflow specific enough to be unambiguous? Are the never-rules necessary? Is the invocation mode appropriate to the side effects?

The pedagogical move: students learn that Skills are the form repeated discipline takes. The peer review surfaces the difference between a workflow specific enough to invoke and a workflow vague enough to need rewriting each time.

This activity maps to the student book's chapter on Skills. Your design here is the worked example.

---

## Common misconceptions

**"Skills are just longer prompts."** No. Skills are *stored* — invokable by name — *reusable* across sessions and across semesters.

**"Everything that's reusable should be a Skill."** No. The CLAUDE.md / Skill boundary matters. Every-session content is CLAUDE.md; workflow-specific content is a Skill.

**"Skills are for power users."** Skills are the most teacher-relevant Claude Code feature. The six-skill catalog above covers most teacher work.

**"The first Skill I write will be good."** Unlikely. The first version is the starting point. The first invocation surfaces what needs refinement. The skill stabilizes after two or three uses. Plan for the iteration.

**"Skills compete with CLAUDE.md."** They complement. CLAUDE.md is the project's constitution. Skills are the named procedures the project supports. Together they form the operating environment for your work.

---

## Exercises

1. **(Apply)** Build a `lesson-plan-generator` Skill. Test with three different sets of learning objectives. Document what it does well and where you still apply interpretive judgment after the draft.

2. **(Apply)** Build a `grading-workflow` Skill that includes the *Never generate a final grade* constraint. Set `disable-model-invocation: true`. Invoke. Verify the constraint held.

3. **(Analyze)** Review a Skill that is too long (over 300 lines). Identify three sections that should move to CLAUDE.md (because they apply across sessions) and three that should become Hooks (because they need deterministic enforcement).

---

## What would change my mind

The chapter's strong claim is that **Skills produce materially better outcomes on repeated workflows** than re-specifying them per session. If a controlled comparison found no measurable difference in workflow quality or speed between Skill-invoked and per-session-specified runs, the chapter's discipline becomes optional. The chapter would still recommend Skills for portability across semesters; the per-invocation case softens.

I expect the difference to be substantial because the cognitive overhead of re-deciding the workflow specifics each time is exactly what the discipline of writing the Skill removes. The grading workflow's third-week-in-a-row specification cost is the chapter's empirical anchor.

---

## Still puzzling

- **The Education Agent Skills Library.** A community effort (152 evidence-based teaching skills across 19 learning-science domains, with MCP tools). If still maintained at press, it is a strong adoption asset for teachers starting from zero. Verify before assigning.

- **Skills across schools.** A district could share a `grading-workflow` skill across a department. The governance model (who owns, how revisions propagate) is open. The chapter's working answer: individual teacher skills first; district-shared skills as a future-edition topic.

- **Hooks-in-skills' adoption rate.** The pattern is clean. Whether the practitioner community converges on it as default is open. The chapter recommends it for any skill with side effects.

---

## Links

- [Claude Code Skills documentation](https://code.claude.com/docs) — SKILL.md format, invocation, frontmatter flags.

---

##  AI Wayback Machine
 **Charles Babbage** (1791–1871) — British mathematician whose designs for the Analytical Engine (1837–1871) included the conceptual foundation for what would later be called the *subroutine*: a stored, named operation that could be invoked at any point in a program's execution without being re-specified each time.[^1] Babbage's argument, articulated in his autobiography *Passages from the Life of a Philosopher* (1864), was that the Engine's power came not from any individual operation but from the *reusability* of operations across many computations. A subroutine specified once and stored on a punched card could be invoked from any program; the work of specification was done once and amortized across every future invocation.

A Claude Code Skill is a Babbage subroutine in 2026 form. The teacher writes the grading workflow once. The workflow is stored as `SKILL.md`. The invocation is `/grading-workflow`. The cost of specification is amortized across every grading cycle for as long as the project lives. Babbage's insight — that the unit of programming work is the *named reusable procedure*, not the per-execution command — is the chapter's insight applied to teacher practice.

Babbage built the conceptual machine; Ada Lovelace, his collaborator, wrote the first program for it (the Bernoulli-number computation in Note G of her 1843 translation of Menabrea's article) and articulated the principle that the Analytical Engine could perform any operation whose specification could be encoded.[^2] Lovelace's deeper argument — that the value of the machine depended on the *programmer's craft* of designing the operations — is the deeper argument of this chapter. The Skill is only as good as the workflow specified in it. The conducting discipline produces good Skills. Bad workflows produce Skills that have to be re-specified after every invocation, which is no better than per-session prompting.

Babbage and Lovelace did not see their machine built. The conceptual framework outlived the physical constraint. Two centuries later, the Skill is the operational form of their argument. The teacher who maintains a Skill catalog across a school year is the practitioner Lovelace imagined.

---

## Bridge

Skills give you reusable workflows. Chapter 7 gives you the enforcement layer that makes the workflow's constraints non-negotiable — Hooks.

---

[^1]: Babbage, C. *Passages from the Life of a Philosopher*. Longman, Green, 1864. Modern reprint: Cambridge University Press (1994).
[^2]: Lovelace, A. *Sketch of the Analytical Engine Invented by Charles Babbage* (translation of Menabrea with extensive Notes). *Scientific Memoirs*, vol. 3, 1843. Note G contains the first published algorithm intended for execution by a machine.
