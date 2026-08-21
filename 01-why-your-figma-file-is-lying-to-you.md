# Chapter 1 — Why Your Figma File Is Lying to You

*The designer changes a color. Three weeks later production still has the old blue. This chapter is about why.*

---

Here is a failure that happened, in one form or another, on almost every design team that works at scale. A designer runs a WCAG contrast audit and discovers that the primary button blue — `#1A73E8` — fails against white backgrounds at the body text size. The fix is straightforward: darken the blue slightly to `#1558D6`. The designer opens Figma, selects the primitive color variable, changes the value, and watches every component that references that variable update in real time. Primary buttons, link text, focus rings — all correct. The design review passes. The tokens JSON file in the design system repository is updated and merged. Everything looks right.

Three weeks later, a visual QA pass before a product launch catches it. The iOS app is still `#1A73E8`. The Android app has `#1558D6`. The web app has `#1558D6` in most places but the old value in the marketing components, which pull from a different token file that nobody updated.

Three environments. Three weeks of silent divergence. A developer fixing it at 9 PM the night before the launch.

The postmortem question was: "Why didn't anyone catch this?" The honest answer is that nobody had a reason to look. The Figma file was correct. The implicit assumption was that a correct Figma file produces correct production code — that the two are linked somehow, that a change in one propagates to the other. That assumption was wrong. And it was wrong in a way that is structural, not accidental. Not a communication failure. Not a process failure. Structural.

Understanding the structure is what this chapter is for.

---

## What the File Actually Is

Before you can reason about why Figma and production diverge, you need a working model of what Figma actually stores. Most people think of a Figma file as a picture — a sophisticated picture with metadata attached, but fundamentally a visual document. This model is wrong, and the wrongness matters.

A Figma file is a document graph. It is a tree of nodes, each with a type, a set of typed properties, and references either to child nodes or to shared resources — style definitions, variable collections, component definitions. The canvas you see when you open Figma is a rendering of that graph. Figma's rendering engine traverses the node tree, resolves the references, and produces pixels on screen. But the pixels are not the data. The graph is the data.

![Figma document graph on the left showing DOCUMENT → CANVAS → COMPONENT → boundVariables → VariableID nodes, contrasted with a rendered canvas panel on the right showing the same button as pixels](../images/01-why-your-figma-file-is-lying-to-you-fig-01.png)
*Figure 1.1 — The file is a graph; the canvas is a rendering of the graph.*

Here is a simplified fragment of what a primary button component looks like in that graph:

```json
{
  "id": "1:23",
  "name": "Button / Primary / Default",
  "type": "COMPONENT",
  "fills": [
    {
      "type": "SOLID",
      "color": { "r": 0.082, "g": 0.337, "b": 0.855, "a": 1 },
      "boundVariables": {
        "color": {
          "type": "VARIABLE_ALIAS",
          "id": "VariableID:12:45"
        }
      }
    }
  ]
}
```

Notice what is happening in the `fills` field. There is a raw color value — the RGB triple — but there is also a `boundVariables` entry pointing to a variable by ID. That variable, `VariableID:12:45`, is defined elsewhere in the file. It has a name, a collection it belongs to, and a value for each mode in that collection. The rendered button uses the resolved value: whatever `VariableID:12:45` currently equals in the active mode.

When you change a variable's value in Figma, you are modifying a node in this graph. Figma re-renders every component that has a `boundVariables` reference pointing at that variable ID. On screen, everything updates instantly. Visually, it looks like the change propagated everywhere.

It did not propagate anywhere outside Figma.

---

## The Structure of the Problem

Here is the core of it, stated as plainly as I can manage.

When you change a variable in Figma, you change a value in Figma's servers — in the document graph that Figma stores and renders. Production is a different system. It does not read from Figma's servers. It reads from a codebase: a collection of CSS files, Swift constants, Kotlin resource files, JavaScript modules, whatever the platform requires. Those files contain values that were correct at the moment a human copied them from Figma. Since that moment, the Figma file has continued to change. The files have not.

The technical name for this situation is a **snapshot**. Every time a designer exports a token file, copies a hex value from Dev Mode, downloads an SVG, or even screenshots a component and attaches it to a Jira ticket, they are producing a snapshot: a copy of the current state, severed from its source at the moment of creation. The copy is accurate when made. The source continues to change. The copy does not.

![Timeline showing the Figma state line rising continuously over time while the production state line remains frozen at the manual export snapshot point, with the gap between them labeled "drift"](../images/01-why-your-figma-file-is-lying-to-you-fig-02.png)
*Figure 1.2 — Every manual crossing produces a snapshot. Snapshots drift.*

This is not a communication problem. Two people talking constantly about a design change does not prevent divergence — it just means both people know the intention. The implementation still has to cross from Figma to production through a manual step, and that step is, by definition, a one-time operation. It does not repeat when the design changes again next week.

This is also not a tooling problem, at least not in the way people usually mean it. The era before the Figma API was the era of inspect tools: Zeplin, InVision Inspect, Figma's own Inspect panel. Inspect tools made the snapshot legible. A developer could click on a component and read its exact font size, color, and padding without guessing from a screenshot. This was a genuine improvement. But it was an improvement to the experience of reading from a snapshot. It did not change the fundamental structure: the snapshot existed as of the last export, and it was not updated when the design changed.

What Zeplin gave teams was a more comfortable way to ignore the synchronization problem. The solution was always going to require eliminating the manual step, not improving it.

---

## Three Ways the System Breaks

The synchronization problem fails in three recognizable patterns. They have different symptoms and different urgencies, but the same root cause.

**Silent drift** is the failure from the opening story. Production and Figma diverge without anyone noticing. The divergence is usually small at first — a shade off, a spacing value that rounds to the nearest pixel differently on different platforms — but it compounds. After six months of active design iteration, production may have dozens of values that no longer match the design. No test fails. The design looks right in Figma. Production looks approximately right in the browser. The divergence surfaces at a brand audit, an accessibility compliance check, or an embarrassing visual comparison in a client meeting.

The diagnostic question for silent drift is simple: when was the last time your team compared production token values against the Figma file programmatically — not by eye, programmatically? If the answer is never, you have silent drift. The only open question is how much.

| Symptom | Detectability | Typical discovery trigger | Urgency |
|---|---|---|---|
| Production color value off by a shade | Very low — passes visual review | Brand audit, accessibility compliance check | Medium — visible gap between design and code |
| Button padding wrong by 2px | Low — within design rounding tolerance | Designer QA pass, pixel-perfect review | Low — non-breaking unless accessibility-relevant |
| Icon asset outdated (pre-redesign version) | Medium — obvious once spotted | Customer screenshot, marketing review | High — customer-facing brand inconsistency |
| Token name referenced in code no longer exists | High — build or runtime error | CI failure, missing variable warning | Critical — blocks build or breaks UI |
| Font weight changed in Figma, old weight in prod | Low to medium | Accessibility audit, type review | Medium — may fail WCAG at small sizes |
| Semantic token pointing to wrong primitive | Very low — visually close | Programmatic token diff, design system audit | High — silent semantic error, hard to trace |

**Version chaos** is the `tokens_final_v3_really_final.json` situation. This failure is visible in the file structure: token files with version suffixes, multiple "source of truth" Figma files, duplicate component definitions across projects, exported assets in three slightly different directories. One designer exports to `tokens/`; another exports to `design-tokens/`; a third commits to `src/styles/tokens/`. Nobody knows which one the iOS build is reading. The Figma file is correct. Production has four different versions of correct, applied inconsistently across platforms.

Version chaos is a naming and governance failure on the surface, but its root is the same: the manual crossing invites human variation. Each export is an independent act with no enforced destination, no enforced filename, no enforced validation. Every crossing adds entropy.

**Broken trust** is the quietest failure, and the most expensive to repair. Engineers stop checking the Figma file because experience has taught them it does not match production. Designers stop specifying precisely because engineers are going to "just figure it out" anyway. The design system degrades into a mood board — a reference, a vibe, not a contract. The symptoms are developers hardcoding values because "the tokens keep changing," and designers specifying components they privately expect will not be built accurately.

Broken trust is terminal. It means the synchronization problem has been running long enough to produce cultural damage. The fix is technical — build the pipeline — but rebuilding the trust requires demonstrated reliability over time, not just a fixed process.

---

## What Needs to Happen Instead

The diagnosis leads directly to the prescription. If the Figma file is genuinely the source of truth — if that phrase is to mean something beyond a hopeful policy statement — then the only sustainable connection between it and production is an automated pipeline that reads, transforms, and distributes without a human in the loop.

Not a better naming convention. Not a more careful export checklist. Not a Confluence page documenting the correct procedure. An automated pipeline.

What I mean by pipeline is a sequence of automated steps with a defined input, a defined set of outputs, and a defined trigger. The input is a Figma file key and credentials. The outputs are platform-ready artifacts: token JSON files, exported SVG assets, component documentation, machine-readable specs. The trigger is an event — a Figma library publish, a CI run, a scheduled job. The pipeline runs, reads the current graph state from the Figma API, transforms it, validates it, and writes to production. No human performs the crossing.

![Five-stage pipeline flow diagram: Trigger (LIBRARY_PUBLISH) leads to API Read (GET /v1/files/:key), Transform (DTCG JSON), Validate (aliases and values), and PR/Deploy (tokens.json), with a red failure branch from Validate showing the job failing rather than opening a PR](../images/01-why-your-figma-file-is-lying-to-you-fig-03.png)
*Figure 1.3 — The automated crossing eliminates the snapshot. The current graph state is always readable.*

The human's role in this system is governance, not crossing. A designer decides what is authoritative. An engineer decides what the pipeline is allowed to read and write, what changes require human review before reaching production, what the validation rules are. The judgment is human. The mechanical act of moving a value from Figma to a token file is not.

This is the difference between the Zeplin Era and the extraction layer. Zeplin gave you better access to the last known state. The extraction layer eliminates the last-known-state problem by making the current state queryable at any time. The API doesn't give you a better snapshot. It gives you the source, re-readable on demand.

---

## When Manual Export Is Actually Fine

I want to be honest about the cases where the pipeline is not worth building, because overselling it does nobody any good.

Manual export is a reasonable choice when the team is small enough — two to four people — that communication overhead is low and synchronization failures surface quickly. It is reasonable when there is one platform and one codebase, because there is no divergence opportunity: one manual crossing, one destination, one file to check. It is reasonable when the design system is stable and changes infrequently — twice a year, say — because the risk window is short and the crossing cost is low. And it is reasonable when nothing in the production build actually reads from Figma; if no build tool, code generator, or automated agent is consuming Figma data, there is nothing for drift to break.

| Factor | Manual export is acceptable | Pipeline is required |
|---|---|---|
| Team size | 2–4 people; communication overhead low | 5+ people; crossed wires are structural, not personal |
| Platform count | Single platform, one codebase, one destination | Two or more platforms (web + iOS, web + Android, etc.) |
| Change frequency | Rare (twice a year or less); short risk window | Monthly or more; frequent crossings compound error |
| Downstream automation | No build tool, code generator, or AI agent reads Figma data | Any pipeline step or agent consumes Figma output as input |
| Compliance requirements | No contractual brand or accessibility audit obligation | Contractual WCAG compliance, brand governance, or legal audit trail required |

Manual export is not acceptable when multiple platforms need to stay synchronized. Each crossing is an independent act; platforms diverge by default. It is not acceptable when the design system changes frequently, because frequent crossings mean frequent opportunities for error. It is not acceptable when a build tool, code generator, or AI agent is reading Figma data — these consumers need reliable, validated, versioned inputs, which snapshots cannot provide. And it is not acceptable when brand compliance or accessibility is a contractual requirement, because "we meant to update it" is not an audit defense.

The practical version of this decision: if any of the "not acceptable" conditions are true for your team, the manual workflow will eventually produce a failure. The only question is when, and whether you find out before or after it matters.

---

## The Concrete Before and After

Let me make the pipeline concrete so the abstraction has somewhere to land.

**Before**, in the manual workflow: a designer updates color variables in Figma. The designer or a design system engineer opens a plugin — Tokens Studio, say — exports the current token set to a JSON file, names it based on judgment (`tokens.json`, `design-tokens-v3.json`, `tokens_final_actually.json`), and either Slacks it to the engineering team or commits it manually if they have repository access. Engineers update their build configuration to point at the new file. Some environments are updated; others are not. Three months later there are four token files in the repository, each slightly different, each referenced by a different platform build, and nobody can say with confidence which one is correct.

**After**, in the pipeline workflow: the designer publishes the variable library in Figma. A webhook fires, triggering a CI job. The job calls the Figma Variables API with the canonical file key, reads the current variable state, transforms it to DTCG-compatible JSON, and writes `tokens/tokens.json` to a deterministic path. A validation step checks for broken aliases and malformed values; if validation fails, the job fails and no pull request is opened. If validation passes, the job opens a pull request with the exact diff: which token values changed, which modes, which collections. Engineers review the diff. The PR is merged. Every platform build that reads `tokens/tokens.json` picks up the change on next build.

One source of truth. One token file. One crossing, automated. The diff is visible and reviewable. The history is in Git. The validation is enforced. The filename is not a function of whoever happened to run the export last Tuesday.

| | Manual export | Automated pipeline |
|---|---|---|
| Who performs the crossing | Designer or design-system engineer, manually | CI job triggered by Figma webhook — no human required |
| Output filename | Whatever the exporter named it (`tokens_final_v3.json`) | Deterministic path (`tokens/tokens.json`) — always the same |
| Validation | None enforced; errors visible only in production | Broken aliases and malformed values caught before PR opens |
| Audit trail | Slack DM, email, or Git commit with human-provided message | Git diff in pull request — exact token changes, modes, collections |
| Drift risk | High — production diverges between exports with no alert | Low — pipeline runs on every library publish; diff is immediately visible |
| Platform consistency | Each platform updated independently; divergence is default | All platforms read the same output file on next build |

The before-and-after is not primarily a tooling change. It is a governance change: the decision about what is authoritative and who is allowed to produce it. The tooling makes that governance enforceable rather than aspirational. That is the whole point.

---

## What I Don't Fully Understand

There is a thing about this problem I have not fully resolved, and I want to be honest about it.

The pipeline eliminates the manual crossing, but it does not eliminate human judgment about what gets crossed. Someone has to decide that a variable name change is intentional, not a typo. Someone has to notice when a semantic token is accidentally mapped to a primitive value. Someone has to review the PR diff and understand whether a two-value change in the `--color-primary` token is the expected result of a deliberate brand decision or a mistake in the transform script.

The pipeline makes that review possible — the diff is right there in the PR, reviewable by anyone with repository access. But it does not make that review easy for non-engineers. A designer who needs to verify that the pipeline correctly captured their Figma change has to either trust the automated validation or learn to read a JSON diff in a pull request. Neither option is fully satisfying.

The tools are getting better. Figma's own Variables API is relatively new; the workflows for connecting it to design review rather than just engineering review are still being worked out. This book covers the extraction layer as it exists today, which is primarily an engineering tool. The design-legible version of that pipeline — one where a designer can verify correctness in a Figma-native interface rather than a GitHub PR — is a thing I expect to exist in a few years and that I cannot fully describe yet.

---

## What Comes Next

Chapter 2 explains what the Figma API actually exposes. The node graph you will read from, the rate limits you will hit, the plan gates that control which endpoints are available to which subscription tiers, and the first real code artifact: `figma-ping.js`, a small script that verifies your credentials and session are healthy before you write a single line of extraction logic.

The failure is diagnosed. The structure is clear. The prescription is an extraction layer. Let's build it.

---

**LLM Exercises**

*Use these with Claude or any capable language model to deepen your understanding of the concepts in this chapter.*

**1. Generate and examine.** Ask the model to describe the synchronization problem in a domain you know well — not design systems, but something from your own work or field. Ask it to identify where "snapshots" exist in that domain and what the equivalent of "drift" looks like. Compare its answer to the structure described in this chapter.

**2. Apply to known context.** Describe a specific handoff workflow from a project you have worked on — the actual steps, the actual tools, the actual people involved. Ask the model to map each step to one of the three failure modes (silent drift, version chaos, broken trust) and explain its reasoning. Push back if its categorization seems wrong.

**3. Stress-test a specific claim.** This chapter argues that the manual crossing is structural, not a communication or process failure, and therefore cannot be fixed by better communication or more careful process. Present this argument to the model and ask it to steelman the opposing view: that disciplined process and strong communication can, in practice, prevent synchronization failures at scale. Evaluate how convincing you find the counterargument.

**4. Draft or audit a professional deliverable.** Write a one-paragraph summary of the synchronization problem as you would explain it to a non-technical stakeholder — a product manager, a VP, a client — who is asking why the team needs time to build pipeline infrastructure. Then ask the model to critique your explanation for clarity, accuracy, and whether it makes the business case effectively.

---

## Chapter 1 Exercises: Why Your Figma File Is Lying to You
**Project:** figma-tools — Your Design System Extraction Toolkit
**This chapter adds:** The project CHARTER — you pick your Figma file, articulate the synchronization problem you are solving, decide pipeline-vs-manual, and initialize the repository structure and README that every subsequent chapter will build on.

---

### Exercise 1 — When to Use AI

You have just read a chapter that builds a precise conceptual distinction — synchronization problem vs. communication problem — and maps three distinct failure modes. AI is useful at specific points in the work you are about to do.

**Drafting the charter narrative.** Once you have made your own judgment call about which failure mode your team is experiencing, AI can draft the one- to two-paragraph narrative that explains the situation to non-technical stakeholders. You bring the diagnosis; the model brings the prose.
*Why AI works here:* drafting — turning a structured finding into readable professional language is a pattern-completion task. The judgment about which failure mode applies is yours. The words are negotiable.

**Generating the decision matrix for your context.** The chapter supplies a decision rule (pipeline vs. manual). AI can apply it to a described scenario — your team size, platform count, change frequency — and produce a structured argument for one option. You then verify that argument against what you actually know about your situation.
*Why AI works here:* options — presenting the relevant factors and their implications in a given scenario is well within what a capable model can do, especially when the decision rule is already published in the chapter.

**Reformatting the synchronization problem for a specific audience.** Once you know what you want to say, AI can rewrite a technical explanation for a PM, a legal team, an engineering manager — adjusting register, cutting jargon, sharpening the business case.
*Why AI works here:* reformatting — changing the register and vocabulary of a technically accurate statement for a different audience is a style transformation, not a factual judgment.

**The tell:** you can read the output against the chapter's specific definitions (synchronization problem ≠ communication problem; the three failure modes are named and described). If the model's output conflates them, you will catch it.

---

### Exercise 2 — When NOT to Use AI

Three things in this chapter's work require your judgment, not a model's.

**Diagnosing which failure mode your team actually has.** Silent drift, version chaos, and broken trust have different symptoms and different urgency. Identifying which one applies requires knowing your team's actual history — the specific incident that made you pick up this book, the cultural dynamics, the trust level between design and engineering. A model cannot know those things. If you describe your situation and ask the model to diagnose it, you will get a plausible-sounding answer that is calibrated to the description you gave, not to the reality.
*Why AI fails here:* missing ground truth — the model has no access to your organization's actual state.

**Deciding pipeline vs. manual for your specific situation.** The chapter supplies a decision rule, but applying it requires honest answers to questions about your team that only you can answer: Are there actually multiple platforms? Is the design system actually changing frequently? Is there actual downstream automation consuming Figma data? A model will apply the rule to whatever inputs you provide. If you idealize your situation in the description, the model will recommend a pipeline. If you understate it, the model will recommend manual. It cannot verify the inputs.
*Why AI fails here:* calibration — the model's output is only as good as your description, and you are the one who knows whether the description is accurate.

**Identifying the root cause of broken trust on your specific team.** Broken trust is a social and historical phenomenon. It has causes that are specific to particular people, specific incidents, specific organizational pressures. A model can describe what broken trust generally looks like and how it generally develops. It cannot tell you why it happened on your team or what the correct first step toward repair is.
*Why AI fails here:* causal identification — tracing the actual cause of a cultural failure on a specific team requires knowledge of that team's history that no model has.

**The tell:** the output will sound completely reasonable and will fit the description you gave. If you gave it a description that was not fully accurate, the output will be confidently wrong in a way that is very hard to detect without independent knowledge of the situation.

**Series connection:** Chapter 1 establishes the hardest tier of human judgment — Tier 5 (causal identification in organizational context) and Tier 4 (evaluation against criteria where the criteria depend on knowledge the model doesn't have). All subsequent chapters will work at Tier 4 and below as the problems become more technical and the ground truth becomes more verifiable.

---

### Exercise 3 — LLM Exercise

**What you're building:** A project charter draft — a structured statement of the synchronization problem you are solving, the failure mode you have identified, and the decision you have made about pipeline vs. manual.

**Tool:** Claude (claude.ai or API). A single-context conversation works well here because the output is a prose document, not code, and the task is drafting — helping you articulate a judgment you have already made.

**The Prompt:**

```
I am starting a design system tooling project. I need help drafting a short project charter.

Context about my situation:
- Team: 3 designers, 6 engineers, 2 platforms (web and iOS)
- Design tool: Figma, with an active component library
- Current workflow: designers export token JSON manually using Tokens Studio, commit to a shared folder, engineers update their build configs when they notice the file has changed
- How often the design system changes: roughly monthly updates to color tokens, occasional component additions
- The specific failure I want to address: we discovered during a compliance audit that the iOS build was using last quarter's color values — the web had been updated but nobody had re-exported the iOS token file

Based on this, please:
1. Identify which of these three failure modes best describes the situation: silent drift, version chaos, or broken trust. Explain your reasoning in one paragraph.
2. Apply the pipeline-vs-manual decision rule to my situation. The rule factors are: team size, platform count, change frequency, downstream automation, and compliance requirements. State which factors push toward pipeline and which (if any) push toward manual.
3. Draft a two-paragraph project charter narrative suitable for sharing with a non-technical stakeholder — a product manager or department head — explaining why we are investing time in this tooling work.

Keep the diagnosis crisp and the charter narrative free of technical jargon.
```

**What this produces:** A structured charter draft you can adapt — the failure mode diagnosis gives you language for internal conversations, the decision rule application gives you the business case, and the narrative gives you a starting point for a README or project proposal.

**How to adapt this prompt:**
- *For your own project:* replace the Context block with your actual team size, platforms, workflow, and the specific incident that triggered the work. The more specific you are, the more useful the output.
- *For ChatGPT or Gemini:* the prompt works as written. These models will apply the same decision rule; compare whether they weight the factors differently.
- *For a Claude Project:* set the system prompt to "You are helping a design systems engineer build a project charter for automating Figma-to-production token delivery." This anchors the model's role across multiple follow-up turns.

**Connection to previous chapters:** This is Chapter 1 — the charter is the foundation. Everything built in subsequent chapters is in service of the problem you articulate here.

**Preview of next chapter:** Chapter 2 asks you to call the Figma API for the first time. The charter you draft here determines which Figma file you will test against — you should have a file key ready.

---

### Exercise 4 — CLI Exercise

**What you're building:** The figma-tools repository — the scaffold that every subsequent chapter will build on. A correct initial setup means every later tool has a consistent home.

**Tool:** Claude Code

**Skill level:** Beginner — this is a repo initialization task. No API calls yet. No credentials. Just structure.

**Setup:**
- [ ] Claude Code is installed and running
- [ ] You have a directory where you want the project to live (e.g., `~/projects/figma-tools`)
- [ ] You have Git installed

**The Task:**

```
Initialize a new figma-tools project repository in the current directory. I am building a CLI design system extraction toolkit for a Figma file. Here is what I need:

1. Run `git init` to initialize the repository.

2. Create this folder structure:
   - `scripts/` — for CLI tools (empty for now, add a .gitkeep)
   - `.figma-fixtures/` — for local API fixtures (empty for now, add a .gitkeep)
   - `figma-output/` — for generated output files (empty for now, add a .gitkeep)

3. Create a `.gitignore` file that excludes:
   - `.env` and `.env.local` and `.env.*.local`
   - `.figma-fixtures/` (fixtures contain file structure data; do not commit)
   - `figma-output/` (generated output; do not commit)
   - `node_modules/`

4. Create a `README.md` with these sections:
   - Project name: figma-tools
   - One sentence: what problem this project solves (use this: "Automates extraction of design tokens, component inventory, and assets from a Figma file into production-ready artifacts, eliminating manual export and the drift it causes.")
   - Setup section with placeholder instructions for: installing Node, setting up .env, running the first CLI tool
   - A CLI tools table with columns: Command, Description, Chapter — leave it empty for now with a note "Tools added per chapter"

5. Create a `CLAUDE.md` file at the project root. It should contain these standing rules for Claude Code working in this repo:
   - Never hardcode credentials or tokens in any script file. All secrets must be read from environment variables.
   - Never commit `.env` files or `.figma-fixtures/` contents.
   - When generating CLI scripts, always read FIGMA_TOKEN and FIGMA_FILE_KEY from process.env, never from hardcoded values.
   - Scripts in `scripts/` are Node.js. Use ES modules (.mjs) for new scripts unless a file specifies otherwise.

Do not create any additional files. Do not run npm init. Do not install any packages. Leave the scripts/ directory empty except for the .gitkeep.

Verify by listing the directory structure when done.
```

**Expected output:** A directory tree showing `scripts/`, `.figma-fixtures/`, `figma-output/`, `.gitignore`, `README.md`, and `CLAUDE.md`. The `.gitignore` should exclude `.env` and the fixture/output directories.

**What to inspect:**
- Open `.gitignore` and confirm `.env` and `.figma-fixtures/` are listed
- Open `CLAUDE.md` and confirm the no-hardcoded-secrets rule is present and specific
- Run `git status` to confirm the repo is initialized and the gitignored directories do not appear as untracked

**If it goes wrong:**
- *Claude Code creates extra files or installs packages:* this usually means the scope instruction was not specific enough. Re-run with "Do not install any packages or create any files beyond those listed."
- *CLAUDE.md is missing or vague:* the standing rules in CLAUDE.md are enforced in every subsequent chapter. If they are not specific — if they say "be careful with secrets" rather than naming the exact env variable — rewrite them before moving on.

**CLAUDE.md / AGENTS.md note:** The `CLAUDE.md` created here is the governing document for every Claude Code task in this book. The no-hardcoded-secrets rule added in this exercise will be read by Claude Code on every subsequent chapter's task. Write it once, precisely, and it will hold.

---

### Exercise 5 — AI Validation Exercise

**What you're validating:** The project charter drafted in Exercise 3.

**Validation type:** Conceptual accuracy — checking whether the charter correctly identifies the failure mode and applies the pipeline-vs-manual decision rule.

**Risk level:** Medium. The charter is a communication document, not a production artifact. A wrong diagnosis here means building the wrong pipeline or framing the project incorrectly to stakeholders — recoverable, but costly.

**Setup:** Use the charter draft from Exercise 3. You are running this validation before sharing the charter with anyone or using it to scope the project.

**The Validation Task:**

```
I have a project charter for a Figma-to-production tooling project. Please validate it against these criteria:

[PASTE YOUR CHARTER TEXT HERE]

Check each item and respond with PASS, FAIL, or FLAG (needs review):

CORRECTNESS
[ ] The failure mode named in the charter (silent drift / version chaos / broken trust) matches the described incident. If the charter names a failure mode that does not fit the described incident, flag it.
[ ] The pipeline-vs-manual recommendation is consistent with the factors described. If the charter recommends a pipeline but the described situation fits the "manual is fine" criteria (small team, one platform, infrequent changes, no downstream automation), flag it.

COMPLETENESS
[ ] The charter states what specific problem is being solved — not just "we need a pipeline" but what production failure the pipeline prevents.
[ ] The charter names at least one concrete factor that makes manual export insufficient for this team.

SCOPE
[ ] The charter does not promise specific technical deliverables (tool names, API endpoints, token formats) that have not yet been decided. Scope creep in a charter is a risk.
[ ] The non-technical narrative (if present) avoids jargon that a product manager or VP would not understand.

CHAPTER-SPECIFIC CRITERIA
[ ] The charter distinguishes the synchronization problem from a communication problem. If it frames the solution as "better communication" or "clearer process," flag it — this is the core conceptual error this chapter addresses.
[ ] The charter treats the Figma file as the source of truth and production as the target — not the reverse.

FAILURE MODE CHECK
[ ] "Fluent but wrong": Does the charter sound authoritative but misidentify the failure mode? Check whether the described incident maps to the named failure mode or whether the model picked the most dramatic-sounding option.
[ ] "Pipeline when manual is correct": Does the charter recommend a pipeline for a situation that actually meets the "manual is fine" criteria? This is the chapter-specific failure mode — AI tends to recommend automation because this is a book about automation.
[ ] "Synchronization framed as communication": Does the charter suggest that the fix involves better Figma-to-engineering communication rather than eliminating the manual crossing? This is the core conceptual error this chapter is designed to prevent.
```

**What to do with your findings:** Any FAIL or FLAG means the charter needs revision before you use it to scope the project. The most important flags are the chapter-specific ones — if the charter misidentifies the failure mode or recommends a pipeline when manual would be appropriate, the project is scoped incorrectly from day one.

**AI Use Disclosure prompt:** Add these two sentences to any internal document where you share the charter: "This project charter was drafted with AI assistance and validated using a structured checklist. The failure mode diagnosis and pipeline-vs-manual decision represent my own judgment about our team's situation."

**Series connection:** The failure mode to watch for throughout this series is "fluent but wrong" — output that is grammatically confident and structurally correct but factually or conceptually mistaken. In Chapter 1 the specific form is mis-framing the synchronization problem as a communication problem, which is a Tier 5 (causal identification) error that no amount of domain knowledge about Figma APIs will help you catch. You have to know the distinction before you can validate it.
