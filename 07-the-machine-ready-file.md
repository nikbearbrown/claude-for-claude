# Chapter 7 — The Machine-Ready File

*A file that looks correct to a designer can be structurally opaque to a pipeline — and the pipeline will not always tell you which one it's reading.*

---

The pipeline did not fail on day one. It failed six weeks in, after the sprint where the design system designer reorganized the variable collections.

She had good reasons. The old structure — one massive collection named "Tokens" — was unwieldy at the scale the team had grown to. She split it into three: Primitives, Semantics, and Component. She renamed a handful of variables along the way to match the slash conventions from Chapter 4. The Figma file looked cleaner. The component library published without errors. Product designers could finally find what they needed.

The token pipeline ran that night. It completed without a hard failure. It silently dropped 47 of the 183 semantic tokens. The CSS it wrote had `var(--color-brand-primary)` referencing a variable that no longer existed in the output. The staging build deployed. The design review the following Thursday was the first time anyone saw that the button primary color had reverted to the browser default blue.

The pipeline had been reading a file it thought was machine-ready. It was not. No one had defined what machine-ready meant. No one had checked.

---

## The Implicit Assumptions a Pipeline Makes

When a pipeline reads a Figma file, it does not read with judgment. It reads with assumptions. Each assumption is a place the pipeline can fail silently — producing output that is structurally valid but semantically wrong, which is a worse failure than crashing, because nobody knows to look.

The naming assumption: variable and component names follow a consistent slash-separated hierarchy. If some names use spaces, some use underscores, some have no hierarchy at all, the transformation step produces garbage or silently drops values it cannot classify. The pipeline does not complain. It processes what it can and ignores the rest.

The alias assumption: when a semantic token references a primitive, the alias chain resolves to a raw value. If the primitive was renamed or deleted — as it was in the story above — the chain is broken. The pipeline either writes an unresolved reference into the output or crashes on the resolve step. Which one it does depends on implementation details the user may not have read.

The publication assumption: component metadata is only fully available for published library components. If components are unpublished, documentation generators, code generators, and MCP consumers get incomplete data without knowing it is incomplete. They will report what they have as if it were the whole picture.

The structure assumption: most token pipelines are written to expect a variable collection architecture that separates primitives from semantics. A single flat collection is not invalid — Figma will not reject it — but it breaks the transformation logic that determines output intent. The pipeline cannot infer which tokens are decisions and which are vocabulary if both live in the same collection with the same depth.

The description assumption: when a pipeline builds component documentation, generates a design spec, or provides context to an AI coding agent, it reads the `description` field on components and variables. In most real files, those fields are empty. The pipeline has nothing to pass downstream except names and values, and a name like `Button/Primary/Default` without a description tells a code generator approximately nothing about when to use it.

The export assumption: asset export pipelines expect exportable nodes to be named consistently, to have export settings configured, and for those names to map to meaningful paths in the repository. A frame named `Frame 47` with no export settings is invisible to the pipeline.

| Assumption | What breaks when violated | Failure mode | How to detect |
|---|---|---|---|
| **Naming** — variable and component names follow a consistent slash-separated hierarchy with lowercase, hyphen-only segments | Transformation step produces garbage or silently drops values it cannot classify | Silent — pipeline processes what it can and ignores the rest | Run `checkVariableNames()` in preflight; any name failing `/^[a-z0-9][a-z0-9\-]*$/` per segment is flagged |
| **Alias** — every semantic token alias chain resolves to a raw value via a defined primitive | Pipeline writes an unresolved reference into the output or crashes on the resolve step; `var(--color-brand-primary)` references a variable that no longer exists | Silent or crash depending on implementation | Run `checkAliasChains()` in preflight; check `value.type === 'VARIABLE_ALIAS'` and verify the target `variableId` exists in the same file |
| **Publication** — component metadata is fully available only for published library components | Documentation generators, code generators, and MCP consumers get incomplete data and report it as if it were complete | Silent — consumers receive partial results without indication of incompleteness | Compare last-published timestamp against a maximum staleness threshold (fourteen days by default); flag unpublished components consumed by known pipelines |
| **Structure** — at least one collection separates primitives from semantics | Pipeline cannot infer which tokens are decisions and which are vocabulary; flat single-collection files break transformation logic that determines output intent | Silent — output is structurally valid but semantically meaningless | Check that `variableCollections` contains at least two named groups; warn if all variables share one collection with the same depth |
| **Description** — the `description` field on components and variables is populated for all non-primitive tokens | Pipeline passes only names and values downstream; a name like `Button/Primary/Default` without a description tells a code generator nothing about when to use it | Silent — documentation is generated from names alone, losing all intent | Run `checkComponentDescriptions()` in preflight; empty `description` on a published component is advisory by default, blocking for MCP context providers |
| **Export** — exportable nodes are named consistently, have export settings configured, and map to valid filesystem paths | Asset export pipeline sees nothing to export; or it exports to paths that conflict, use reserved characters, or are Figma-generated defaults like `Frame 47` | Silent for missing settings; crash or overwrite for path conflicts | Run `checkExportTargetNames()` in preflight; flag nodes with `exportSettings.length > 0` whose names match `/^(Frame\|Group\|Rectangle) \d+$/` or contain reserved characters |

These are not exotic edge cases. They are the normal state of a Figma file that has been worked on by a team for more than a few months. Files accrete. Conventions drift. A variable added by a designer on a deadline who hasn't read the convention document is a broken alias chain waiting to fire.

---

## What Machine-Readiness Actually Means

Machine-readiness is not a score. It is not an aspiration. It is a contract — a specific set of conditions that must be true before a pipeline is authorized to read the file and trust what it finds.

The contract has five categories, each with two severity levels: **blocking** (the pipeline must not proceed) and **advisory** (the pipeline can proceed but the finding should be logged and reviewed).

**Category one: naming contract.** All variables follow the slash-separated hierarchy convention from Chapter 4. Variable names contain no spaces, no uppercase characters, and no special characters other than hyphens within a segment. Component names follow a consistent capitalization convention. Layer names on exportable assets are not Figma-generated defaults — no `Frame 47`, no `Group 12`, no `Rectangle 8`. Naming violations are blocking unless they affect only non-exported, non-variable nodes that nothing in the pipeline touches.

**Category two: variable architecture.** At least one collection exists for primitives and at least one for semantic aliases. No semantic token aliases chain through more than three levels — `semantic → primitive` is normal; `semantic → semantic → semantic → primitive` is a sign of architectural confusion that will produce unpredictable results when a primitive changes. All alias chains resolve to a raw value; no broken references. [verify — current as of writing: the Figma Variables API exposes `resolvedType` and the alias target's `variableId`; a broken alias occurs when the target variable does not exist in the same file or in an enabled library] Broken aliases are always blocking. Duplicate values — the same color at two different names with no semantic distinction between them — are advisory.

**Category three: publication state.** The component library has been published at least once. No components that were previously published have been moved to an unpublished location without a corresponding library update. The last publication timestamp is within a threshold acceptable to the team — warn if the library has not been re-published in more than fourteen days. Unpublished state for components that downstream pipelines are known to consume is blocking.

**Category four: component documentation.** All published components have a non-empty `description` field. All component sets — variant groups — have a description explaining what the variants represent. Variable descriptions are populated for all non-primitive tokens: a primitive like `color/palette/blue-500` is self-documenting by name; a semantic token like `color/button/primary` is not. Empty descriptions on published components are advisory by default, and become blocking if the pipeline is a documentation generator or MCP context provider whose output will be treated as authoritative.

**Category five: export targets.** All frames and components intended for export have export settings configured. Export target names do not conflict with each other across pages. Export target names map to valid filesystem paths — no reserved characters, no leading slashes. Missing export settings on intended export targets are blocking for asset export pipelines and advisory for everything else.

![Severity matrix showing five machine-readiness categories against blocking and advisory severity columns](../images/07-the-machine-ready-file-fig-01.png)
*Figure 7.1 — Machine-readiness severity matrix*

The distinction between blocking and advisory is important because it determines the pipeline's behavior when findings exist. A pipeline that halts on every advisory finding will never run in a real project — real projects accumulate advisory findings faster than they are resolved. A pipeline that ignores blocking findings will silently produce wrong output and deploy it. The severity classification is the policy decision that determines which failures the team accepts responsibility for surfacing immediately versus monitoring over time.

---

## The Automation Contract: FIGMA.md

The readiness contract is about file structure. There is a second contract the machine-readiness standard requires, and it is about authorization: what is a pipeline permitted to do with what it finds?

Without a declared governance document, a pipeline has to guess. Can it overwrite existing files? Commit changes directly to main? Delete assets that no longer appear in the file? The only safe default is to fail on ambiguity — which means the pipeline will fail constantly in ways that require human judgment to resolve, defeating the purpose of automation.

The solution is a `FIGMA.md` file at the root of the repository that owns the pipeline code. It is the governing document for any automation — including AI coding agents given access to the Figma file via MCP — that reads or acts on the Figma file. It is not an access control mechanism; access is controlled at the API token level. It is a declared intent document: the set of assumptions the pipeline makes, written down and reviewed by the team.

```markdown
# FIGMA.md — Automation Governance

## File Key
FIGMA_FILE_KEY=abc123def456
<!-- stored in .env; this file documents intent, not credentials -->

## What automation may do without human review
- Read the file and write local JSON fixtures
- Extract variables and write DTCG-compatible token JSON to /tokens/
- Export named assets and write to /src/assets/icons/
- Run the preflight check and exit with non-zero code if blockers are found
- Open a pull request with generated output for human review

## What automation must not do without human review
- Commit generated files directly to main without a PR
- Delete files from /src/assets/ without confirming the node still exists in Figma
- Modify any Figma file contents via the Plugin API
- Infer semantic meaning from unnamed or ambiguously named nodes
- Use node IDs not confirmed stable (see Node ID Stability below)

## What this pipeline does NOT do
- Make design decisions
- Resolve naming conflicts
- Determine which components should be published
- Override the designer's intent as expressed in variable values or component structure

## Node ID Stability
Node IDs in Figma are stable as long as the node is not deleted and recreated.
A rename does not change the node ID. A copy-paste creates a new ID.
If node IDs in the asset manifest change, the pipeline will log a warning
and require manual confirmation before updating the manifest.

## Rate Limit Policy
Requests are batched to 50 nodes per image request.
Retries use exponential backoff with a maximum of 3 attempts.
If rate limits are exhausted, the pipeline exits with code 2 and logs
the retry-after header. [verify — current as of writing]
```

![Hub diagram showing FIGMA.md at the center with arrows to CI pipeline, preflight script, and AI coding agent](../images/07-the-machine-ready-file-fig-02.png)
*Figure 7.2 — FIGMA.md as central governance document for CI, preflight, and AI coding agents*

The value of this document is not primarily technical. It is organizational. When a new engineer joins the team and asks "what is this pipeline allowed to do?", the answer is in `FIGMA.md`. When an AI coding agent is given Figma access and needs to know whether it can infer semantic intent from an unnamed node, the answer is in `FIGMA.md`. When the team debates whether to allow direct commits to main for generated token files, the document is the artifact that makes the debate concrete — edit the document, review the edit, merge or reject it.

---

## The Preflight Script

The preflight script is the enforcement mechanism for the readiness contract. It runs before any pipeline step in CI, reads the live file, checks against the five categories, and exits with a non-zero code if blocking issues are found. If it exits non-zero, subsequent pipeline steps do not run. That is the contract in code.

```javascript
// figma-preflight.mjs
// Usage: node figma-preflight.mjs
// Requires: FIGMA_TOKEN, FIGMA_FILE_KEY in environment
// [verify — current as of writing] Variables API endpoint and response shape

import { writeFileSync } from 'fs';

const TOKEN    = process.env.FIGMA_TOKEN;
const FILE_KEY = process.env.FIGMA_FILE_KEY;

if (!TOKEN || !FILE_KEY) {
  console.error('[preflight] ERROR: FIGMA_TOKEN and FIGMA_FILE_KEY are required.');
  process.exit(1);
}

const BASE = 'https://api.figma.com/v1';

async function get(path) {
  const res = await fetch(`${BASE}${path}`, {
    headers: { 'X-Figma-Token': TOKEN }
  });
  if (!res.ok) {
    const body = await res.text();
    throw new Error(`Figma API error ${res.status}: ${body}`);
  }
  return res.json();
}

function slugValid(name) {
  const segments = name.split('/');
  if (segments.length < 2) return false;
  return segments.every(seg => /^[a-z0-9][a-z0-9\-]*$/.test(seg));
}

function checkVariableNames(variables) {
  const findings = [];
  for (const [id, variable] of Object.entries(variables)) {
    if (!slugValid(variable.name)) {
      findings.push({
        severity: 'blocking',
        category: 'naming',
        message:  `Variable name does not follow slug convention: "${variable.name}"`,
        id
      });
    }
  }
  return findings;
}

function checkAliasChains(variables) {
  const findings = [];
  const variableIds = new Set(Object.keys(variables));
  for (const [id, variable] of Object.entries(variables)) {
    for (const [mode, value] of Object.entries(variable.valuesByMode)) {
      if (value && value.type === 'VARIABLE_ALIAS') {
        if (!variableIds.has(value.id)) {
          findings.push({
            severity: 'blocking',
            category: 'aliases',
            message:  `Broken alias in "${variable.name}" (mode ${mode}): references missing variable ${value.id}`,
            id
          });
        }
      }
    }
  }
  return findings;
}

function checkComponentDescriptions(components) {
  const findings = [];
  for (const [id, component] of Object.entries(components)) {
    if (!component.description || component.description.trim() === '') {
      findings.push({
        severity: 'advisory',
        category: 'documentation',
        message:  `Component has empty description: "${component.name}"`,
        id
      });
    }
  }
  return findings;
}

function checkExportTargetNames(document) {
  const findings = [];
  const reservedChars = /[<>:"/\\|?*]/;

  function walk(node) {
    if (node.exportSettings && node.exportSettings.length > 0) {
      if (reservedChars.test(node.name)) {
        findings.push({
          severity: 'blocking',
          category: 'export',
          message:  `Export target name contains reserved characters: "${node.name}"`,
          id: node.id
        });
      }
      if (/^(Frame|Group|Rectangle|Ellipse) \d+$/.test(node.name)) {
        findings.push({
          severity: 'blocking',
          category: 'export',
          message:  `Export target has a Figma-generated default name: "${node.name}"`,
          id: node.id
        });
      }
    }
    if (node.children) node.children.forEach(walk);
  }

  walk(document);
  return findings;
}

async function run() {
  console.log('[preflight] Reading file...');
  const [fileData, varData] = await Promise.all([
    get(`/files/${FILE_KEY}`),
    get(`/files/${FILE_KEY}/variables/local`)
  ]);

  const findings = [];
  findings.push(...checkVariableNames(varData.variables ?? {}));
  findings.push(...checkAliasChains(varData.variables ?? {}));
  findings.push(...checkComponentDescriptions(fileData.components ?? {}));
  findings.push(...checkExportTargetNames(fileData.document));

  const blocking = findings.filter(f => f.severity === 'blocking');
  const advisory = findings.filter(f => f.severity === 'advisory');

  console.log('\n=== Preflight Report ===');
  console.log(`Blocking: ${blocking.length}  Advisory: ${advisory.length}`);

  if (advisory.length > 0) {
    console.log('\n--- Advisory ---');
    advisory.forEach(f => console.log(`  [${f.category}] ${f.message}`));
  }
  if (blocking.length > 0) {
    console.log('\n--- Blocking ---');
    blocking.forEach(f => console.log(`  [${f.category}] ${f.message}`));
  }

  writeFileSync(
    'preflight-report.json',
    JSON.stringify({ timestamp: new Date().toISOString(), fileKey: FILE_KEY, blocking: blocking.length, advisory: advisory.length, findings }, null, 2)
  );
  console.log('\nReport written to preflight-report.json');

  if (blocking.length > 0) {
    console.error(`\n[preflight] FAILED: ${blocking.length} blocking issue(s). Fix before running pipelines.`);
    process.exit(1);
  }
  console.log('\n[preflight] PASSED. File is machine-ready.');
}

run().catch(err => {
  console.error('[preflight] Unexpected error:', err.message);
  process.exit(1);
});
```

Wire it into CI before every pipeline step:

```bash
npm run figma:preflight && npm run figma:tokens && npm run figma:assets
```

The `&&` is not cosmetic. If `figma:preflight` exits non-zero, the subsequent commands do not run. That is the enforcement.

| Finding | Before remediation (sample from 14 blocking, 31 advisory) | After remediation (0 blocking, 3 advisory) |
|---|---|---|
| **[naming]** Variable name does not follow slug convention | `color/Brand Primary` — space and uppercase in segment | All 47 renamed to `color/brand/primary` etc. — slug-valid throughout |
| **[naming]** Export target has a Figma-generated default name | `Frame 47` with SVG export settings configured | Renamed to `icon-close` matching repository asset manifest |
| **[aliases]** Broken alias in `color/button/primary` (mode: default) | References deleted variable `VariableID:89:4` from removed Primitives collection | Alias chain re-wired to `color/palette/blue/600` in new Primitives collection |
| **[aliases]** Broken alias in `color/button/primary` (mode: dark) | Same root cause — 47 aliases broken in bulk after collection reorganization | Batch re-alias corrected via Figma Variables panel; all 47 now resolve |
| **[export]** Export target name contains reserved characters | `Icons/Close:Default` — colon is a reserved filesystem character | Renamed to `icons-close-default`; slash replaced with hyphen |
| **[documentation]** Component has empty description: `Button/Primary/Default` | `description: ""` — empty on all 12 Button variant components | 12 Button variants now have descriptions. 3 advisory findings remain for secondary components awaiting copy review |
| **[documentation]** Component has empty description: `Input/Text/Default` | `description: ""` | Still empty — 3 advisory findings are candidates for documentation improvement, not pipeline blockers |

A file that passes preflight with zero blocking findings is not a perfect file. It is a file whose structural conditions for pipeline reliability have been met. The design review can then focus on what machines cannot assess: whether the semantic tokens represent the right decisions, whether the component architecture makes sense for the product, whether the exported assets will render correctly across browsers.

---

## What the Preflight Cannot Catch

I want to be honest about the limits, because treating the preflight as a complete quality gate is itself a failure mode.

It cannot tell you whether `color/button/primary` maps to the right primitive for your brand. It only tells you whether the alias is unbroken. A designer who maps the primary button color to a gray instead of the brand blue will not be caught by a naming or alias check — both the name and the alias are structurally valid.

It cannot tell you whether the component architecture makes sense for code generation. Components with correct names and non-empty descriptions can still be structured in ways that make automation difficult: too many variant properties, variants that represent states which should be handled in code rather than in Figma, components nested at the wrong level of abstraction. These are design judgment calls that require a human.

It cannot tell you whether exported SVGs will render correctly across browsers. An SVG that passes the export-target-name check can still contain text that was not converted to outlines, transparency that renders unexpectedly on dark backgrounds, or filters that require browser-specific behavior. The preflight checks the pipeline's assumptions; it does not check the asset's contents.

And it cannot catch conditions it does not know about. The preflight is the current enumeration of known failure modes. When the pipeline encounters a new one — and it will — the correct response is to add a check to the preflight and rerun, so the same failure cannot recur silently.

---

## The Failure Modes of the Preflight Itself

A false pass is the preflight's most dangerous failure: the file passes all checks, the pipeline runs, and the output is still wrong because the pipeline encountered a condition the preflight did not check for. The preflight is a floor, not a ceiling. Teams that treat it as a ceiling stop extending it when they find new failure modes, and the debt accumulates.

Stale fixture is the most common operational failure: running the preflight against a locally saved JSON response rather than the live API. The fixture was accurate when saved. The file has changed since. CI must always run against the live API. Local fixtures are for development iteration only, and should be marked clearly in the codebase as not authoritative.

The Variables API plan gate is a structural constraint worth knowing about explicitly. The `GET /v1/files/:key/variables/local` endpoint [verify — current as of writing] requires an Enterprise plan. On Professional plans, the variables check will return an authorization error. The preflight must handle this gracefully: log that the variables check was skipped due to plan constraints, treat it as advisory rather than blocking, and document the non-Enterprise alternative. On Professional plans, the workaround is using the Tokens Studio plugin to export variables before running the preflight — covered in Chapter 8.

---

## Before This Was a Script

Long before automated preflight tools existed, design system teams enforced machine-readiness through a "definition of done" checklist in Confluence or Notion — a static document a designer was expected to consult before declaring a component ready for handoff. The pattern was reasonable. The execution was fragile. The checklist was maintained by whoever wrote it, usually the design systems lead. When that person was unavailable, the checklist was not consulted. When the checklist was updated, no one remembered to verify that existing components still met the new criteria. The checklist measured stated intent, not actual state.

The preflight script is the same concept running against the live file instead of human memory. The checklist still exists — it is encoded as check functions in the script. The difference is that the check runs on every CI invocation and exits with a code that CI cannot ignore.

The teams running the most reliable token pipelines in the mid-2020s had, often without framing it this way, built automated definition-of-done checks. They had discovered that the moment the check lived in code rather than documentation, its enforcement rate went from aspirational to mandatory. The conceptual framing arrived later. The practice preceded it.

---

Chapter 8 builds the token extraction pipeline on top of this contract. The pipeline assumes the preflight passed. Run it first.

---

**LLM Exercises**

*Use these with Claude or any capable language model to deepen your understanding of the concepts in this chapter.*

**1. Generate and examine.** Ask the model to describe what "machine-readiness" means in a different automated pipeline context — a CI/CD pipeline for a web application, a data ingestion pipeline, an API integration. Ask it to identify the equivalent of "blocking" versus "advisory" findings in that domain. Then compare its structure to the five-category contract in this chapter and note which categories transfer and which are Figma-specific.

**2. Apply to known context.** Describe your team's current Figma file structure — the number of variable collections, whether components are published, whether export targets have configured settings, roughly how consistent the naming is. Ask the model to predict which of the five categories is most likely to have blocking findings and explain its reasoning. Then run the actual preflight and compare the prediction to the output.

**3. Stress-test a specific claim.** This chapter argues that the severity classification — blocking versus advisory — is the most important policy decision in the preflight, and that getting it wrong in either direction causes real problems. Present this claim to the model and ask it to construct a scenario where treating advisory findings as blocking would improve pipeline reliability, and a scenario where it would make the pipeline unusable. Evaluate whether the chapter's default severity assignments are calibrated correctly for your team's situation.

**4. Draft or audit a professional deliverable.** Write a `FIGMA.md` for your current project — the two sections on what automation may do without review and what it must not do. Ask the model to review it for completeness, ambiguity, and coverage of edge cases it would expect a pipeline to encounter in the first six months of operation. Ask it to identify the single highest-risk item that belongs in the "must not" list that you might have missed.

---

## Chapter 7 Exercises: The Machine-Ready File
**Project:** figma-tools — Your Design System Extraction Toolkit
**This chapter adds:** `FIGMA.md` — the governance document that declares what every automation (scripts, CI, and future AI agents) is authorized to read, write, and refuse.

---

### Exercise 1 — When to Use AI

You have just run `figma-preflight.mjs` and have a `preflight-report.json` in hand. Several tasks follow from that report. Here is where AI assistance adds genuine value.

**Task A — Drafting the "what automation may not do" section of `FIGMA.md`.**
Write out the ten or fifteen things your pipeline should never do autonomously, then hand that list to an AI and ask it to sharpen the language, identify gaps, and flag anything ambiguous enough to cause a conflict between two engineers reading the same line. The AI is doing structured drafting and gap-filling — two things it handles well when you supply the raw material.

*Why AI works here:* drafting. You have the domain knowledge and the list; the AI improves coverage and prose consistency.

**Task B — Translating preflight findings into a prioritized remediation checklist.**
Your report shows 14 blocking and 31 advisory findings. Paste the full JSON into an AI conversation and ask it to group findings by fix effort (rename, delete-and-recreate, fill in description, configure export settings) and produce a draft checklist ordered by blast radius — which fix unblocks the most downstream pipeline steps. The AI is reformatting structured data into an actionable document.

*Why AI works here:* reformatting and option-generation. The findings are already structured; the AI is applying a known classification scheme.

**Task C — Generating a boilerplate `figma-preflight.mjs` check function for a new condition.**
You have discovered a new failure mode — say, variable collections with no modes defined. Describe the condition to an AI and ask it to write a check function that matches the style and error-output format of the existing checks in the script. The AI is producing boilerplate against a clear pattern.

*Why AI works here:* boilerplate. The pattern (walk the data, push a finding, return the array) is explicit and the AI can follow it faithfully.

**The tell:** you can evaluate every output against the preflight report, the live file state, or the existing code. If the AI drafts a `FIGMA.md` clause that contradicts a check the script already enforces, you will catch it. Independent criteria exist; use them.

---

### Exercise 2 — When NOT to Use AI

Machine-readiness is partly mechanical and partly a governance decision with organizational weight. Here is where AI breaks down.

**Task A — Deciding which blocking findings to override.**
Your pipeline is due tomorrow. Two blocking findings remain: a variable collection that cannot be renamed before the deadline, and a component whose description the original designer no longer works at the company to provide. Someone has to decide whether to run anyway, document the exception in `FIGMA.md`, and accept the downstream risk. AI can list options. It cannot make this call.

*Why AI fails here:* values and causal identification. The decision involves organizational trust, deadline pressure, and an accurate assessment of which specific pipeline outputs will degrade — not a pattern-matching problem.

**Task B — Setting severity thresholds for your team.**
The chapter gives a default: missing descriptions are advisory, broken aliases are blocking. Your team may need different calibration — a documentation team might make empty descriptions blocking; a token-only pipeline might not care about export-target names at all. Deciding which failures your team is willing to tolerate requires knowing your pipeline's actual consumers, their failure tolerance, and your designer workflows.

*Why AI fails here:* calibration and missing ground truth. AI has no visibility into your team's risk appetite, SLA commitments, or the actual downstream consequences of each finding category in your system.

**Task C — Determining whether a structural change to variable collections was intentional.**
The preflight shows 47 broken aliases after a reorganization. Were those aliases intentional deletions, accidental casualties of a rename, or evidence that the reorganization is incomplete? The AI did not attend the Figma working session. It cannot distinguish a design decision from a mistake.

*Why AI fails here:* missing ground truth. The answer requires institutional knowledge about what changed and why — context that exists only in the team's memory and, if they were diligent, a Figma version history note.

**The tell:** if you find yourself wondering whether the AI's recommendation is correct, and you have no independent way to check it, you are in Exercise 2 territory.

**Series connection:** Tier 4 (metacognitive — knowing when to trust the output) and Tier 6 (governance — the decisions encoded in `FIGMA.md` carry organizational authority that AI cannot confer).

---

### Exercise 3 — LLM Exercise

**What you're building this chapter:** a first draft of `FIGMA.md` for the figma-tools project, incorporating the five machine-readiness categories as the explicit basis for the "what automation must not do" section.

**Tool:** Claude (not a Project). A standard conversation gives you the drafting collaboration this task needs without requiring persistent memory of your file structure.

**The Prompt:**

```
I am building a CLI tool called figma-tools that runs against a Figma design system file. I have just completed a machine-readiness preflight and the file passes all five categories: naming contract, variable architecture, publication state, component documentation, and export targets.

Help me draft a FIGMA.md governance file for this project. The document should:

1. Declare the file key placeholder (I'll fill in the real value from my .env — write it as `FIGMA_FILE_KEY=<set in .env>`)
2. List what automation may do without human review — be specific to these operations: reading the file and writing local JSON fixtures, extracting variables and writing DTCG token JSON to /tokens/, exporting named assets to /src/assets/icons/, running the preflight and exiting non-zero on blockers, opening a pull request with generated output
3. List what automation must not do without human review — include: committing generated files directly to main without a PR, deleting files from /src/assets/ without confirming the node still exists in Figma, inferring semantic meaning from unnamed or ambiguously named nodes, using node IDs not confirmed stable
4. Add a "What this pipeline does NOT do" section covering: design decisions, naming conflict resolution, deciding which components to publish, overriding designer intent in variable values
5. Add a "Node ID Stability" section explaining that renames are stable, copy-paste creates new IDs, and that manifest changes require manual confirmation
6. Add a "Rate Limit Policy" section stating: 50-node batches, exponential backoff with max 3 retries, exit code 2 on exhaustion

Format as a Markdown file. Keep each item in the lists to one sentence. No bullet-point nesting beyond one level.
```

**What this produces:** a complete, copy-paste-ready `FIGMA.md` for the figma-tools root directory that you review, adjust to match your actual file key and pipeline scope, and commit. This document will be read by CI, by future engineers, and (in Chapter 13) by an AI coding agent given Figma MCP access.

**How to adapt this prompt:**
- *Own project:* replace the operation lists in points 2 and 3 with your actual pipeline's current scope. Add any operations specific to your platform (Storybook sync, Zeroheight updates, etc.).
- *ChatGPT or Gemini:* works equally well — the task is structured drafting, and the prompt is fully self-contained. No context about prior chapters is needed.
- *Claude Project:* if you store your figma-tools repo context in a Project, Claude can reference the existing `figma-preflight.mjs` script to ensure the "what automation may do" list matches what the script actually checks, reducing the risk of a `FIGMA.md` that contradicts the code.

**Connection to previous chapters:** the machine-readiness categories in the prompt come directly from this chapter's five-category contract. The rate limit policy and node ID stability notes anticipate Chapter 9's export automation constraints. The document you produce here is the governance foundation every subsequent pipeline chapter assumes.

**Preview of next chapter:** Chapter 8 will add `extract-tokens.mjs` and `validate-tokens.mjs` to figma-tools. The CI workflow in that chapter reads `FIGMA.md` to determine whether the pipeline is authorized to proceed — this document is what makes that authorization explicit.

---

### Exercise 4 — CLI Exercise

**What you're building:** a `FIGMA.md` governance file and a `preflight-report.json` written to the figma-tools root, using Claude Code to run the preflight script and scaffold the governance document.

**Tool:** Claude Code (default)

**Skill level:** Intermediate — you need `FIGMA_TOKEN` and `FIGMA_FILE_KEY` in your environment, and `figma-preflight.mjs` committed to the figma-tools repo from the chapter.

**Setup:**
- [ ] `figma-ping.js` from Chapter 2 passes (confirms token and file key work)
- [ ] `figma-read.mjs` from Chapter 3 has produced at least one successful fixture
- [ ] `figma-preflight.mjs` is committed to the figma-tools root
- [ ] `FIGMA_TOKEN` and `FIGMA_FILE_KEY` are in your `.env` or shell environment

**The Task:**

```
You are working in the figma-tools repository. Do the following, in order:

1. Read figma-preflight.mjs to understand its check functions and output structure.

2. Run: node figma-preflight.mjs
   - If it exits non-zero, read the console output and preflight-report.json.
   - Do NOT attempt to fix the Figma file or modify the script. Just report what you see.
   - If it exits zero, continue to step 3.

3. Read preflight-report.json and extract:
   - Total blocking count
   - Total advisory count
   - The category distribution of findings (naming / aliases / documentation / export)

4. If FIGMA.md does not exist at the repository root, create it using this structure:
   - File Key section (placeholder comment noting the real value is in .env)
   - What automation may do without human review (based on scripts currently in this repo)
   - What automation must not do without human review (include: no direct commits to main, no deleting assets without confirming node existence, no inferring semantic meaning from unnamed nodes)
   - What this pipeline does NOT do (design decisions, naming conflict resolution)
   - Node ID Stability section
   - Rate Limit Policy section (50-node batches, 3 retries, exponential backoff)

   If FIGMA.md already exists, read it and append a "Last Preflight" section with the timestamp, blocking count, and advisory count from preflight-report.json.

5. Do not modify figma-preflight.mjs. Do not run any command that writes to src/, tokens/, or dist/. Do not commit anything.

Verification: after completing, show me the first 30 lines of FIGMA.md and the summary line from the preflight output (the "Blocking: X  Advisory: Y" line).
```

**Expected output:** a `FIGMA.md` at the figma-tools root and a `preflight-report.json` from the live run. Claude Code's response should include the first 30 lines of `FIGMA.md` and the preflight summary line.

**What to inspect in the output:**
- Does `FIGMA.md` include the five governance sections from the chapter?
- Do the "what automation may do" items match the scripts that actually exist in the repo?
- Is the preflight summary line plausible for your file (not all zeros on a real file with real variables)?

**If it goes wrong:** the most common failure here is a 403 from the Variables API on a non-Enterprise plan. This is a plan-tier issue, not a code bug. If you see `403 Forbidden` in the preflight output, note it in your `FIGMA.md` under a "Plan Constraints" section: the Variables check is skipped on Professional plans, and that finding is advisory rather than blocking. Do not remove the Variables check from the script — document the constraint instead.

**CLAUDE.md / AGENTS.md note:** add this standing rule to your CLAUDE.md after this exercise — `figma-tools: never modify Figma file content via any API call. FIGMA.md is the governance document; read it before any write operation.`

---

### Exercise 5 — AI Validation Exercise

**What you're validating:** the `FIGMA.md` produced by Exercise 3 or Exercise 4.

**Validation type:** document completeness and governance correctness.

**Risk level:** Medium. A `FIGMA.md` that looks authoritative but contains over-broad permissions or missing constraints will be treated as gospel by CI and, in Chapter 13, by an AI coding agent. A document that grants more authority than the team intends is harder to notice than a broken script — it does not throw an error.

**Setup:** use the `FIGMA.md` from Exercise 4 (live preflight output) if available. If you have not completed Exercise 4, use the draft produced by Exercise 3.

**The Validation Task:**

```
Validation checklist for FIGMA.md — Chapter 7

Correctness
[ ] Every item in "what automation may do" corresponds to a script that actually exists in figma-tools (no phantom operations)
[ ] Every item in "what automation must not do" is phrased as a prohibition, not a preference ("must not" / "never" — not "should avoid")
[ ] The Node ID Stability section accurately describes Figma's behavior: renames are stable, copy-paste creates new IDs
[ ] The Rate Limit Policy numbers (50-node batches, 3 retries) match the values in figma-preflight.mjs or are documented as defaults pending script implementation

Completeness
[ ] All five machine-readiness categories from the chapter (naming, variable architecture, publication state, component documentation, export targets) are reflected somewhere in the document — either as permitted operations or as explicit out-of-scope items
[ ] A "What this pipeline does NOT do" section exists and covers design decisions and naming conflict resolution

Scope
[ ] The document does not grant permission to commit directly to main (the PR requirement from the chapter must be implicit or explicit)
[ ] The document does not grant permission to infer semantic meaning from unnamed nodes

Chapter-specific criterion 1 — governance grant specificity
[ ] Permissions are scoped to named paths (e.g., "/tokens/", "/src/assets/icons/") rather than broad grants ("write anywhere" or "modify any file")

Chapter-specific criterion 2 — AI agent readability
[ ] Every section is unambiguous when read by a system that cannot ask clarifying questions — no "as appropriate" or "when necessary" language that requires judgment to interpret

Failure-mode check
[ ] "Fluent but wrong": does any clause sound authoritative but contradict the chapter's stated constraints? (Example: "automation may delete files from src/assets/ when the manifest is updated" — this violates the chapter's no-delete-without-confirmation rule)
[ ] "Over-broad governance grant": does any permission clause lack a path scope or a condition that would let it be interpreted as blanket write access to the repository?
```

**What to do with your findings:**
- All boxes checked: `FIGMA.md` is ready to commit. Run it past one human teammate before merging.
- One box unchecked: fix the specific clause or add the missing section, then re-check that item only.
- Multiple boxes unchecked: the document needs a rewrite pass. Do it yourself — a document with multiple governance gaps is too load-bearing to iterate with AI.

**AI Use Disclosure prompt:** copy this into a comment at the top of the PR that introduces `FIGMA.md`:

```
This FIGMA.md was drafted with AI assistance (Claude) and validated against the Chapter 7 machine-readiness checklist. The governance clauses were reviewed by [your name] for accuracy against the current figma-tools scripts and the team's actual pipeline scope. Any discrepancies between this document and the scripts in this repo should be resolved by updating this document, not the scripts.
```

**Series connection:** the "over-broad governance grant" failure mode — a permission clause with no path scope — is a Tier 6 governance failure. It does not break the script; it creates ambiguity about what the pipeline is authorized to do, and that ambiguity compounds in Chapter 13 when an AI coding agent reads `FIGMA.md` as authoritative context. Catching it here prevents it from propagating.
