# Chapter 10 — Component Documentation Sync

*The Figma file knows what exists. The documentation site knows what existed. The gap between them widens every sprint.*

---

The design system has a documentation site. Someone wrote usage guidance for the Button component, added a do/don't example for the Modal, documented the four variants of the Card. The site looks authoritative. It has a clean nav, a search bar, a "last updated" timestamp.

Then the design team ships a quarter. The Button gets a `destructive` variant. The Card acquires a `compact` density option. The Modal's dismiss behavior changes. Nobody updates the documentation site. Nobody has time. The site is now lying — not dramatically, not in ways that cause immediate failures, but steadily and invisibly, in the way that documentation always lies when it is maintained by hand in a system that changes faster than the people maintaining it.

This is not negligence. It is the predictable outcome of a documentation artifact that is structurally disconnected from its source. The Figma file changes. The code changes. The documentation lags — by days, then weeks, then permanently. By the time a new engineer asks "what variants does this component support?", the documentation site is an artifact of a design file that no longer exists.

Documentation drift is the same synchronization problem as token drift, just slower-moving and therefore easier to ignore until it becomes embarrassing at a design review, a new-engineer onboarding, or an audit.

This chapter builds a CLI tool that reads the Figma library directly and generates three machine-readable artifacts: a component inventory, variant property tables, and a missing-description report. These give documentation platforms — Storybook, Zeroheight, Supernova, custom portals — machine-verifiable facts to build from. The human work of writing usage guidance still belongs to humans. The machine work of knowing what exists, what its properties are, and what has not been documented yet belongs to the CLI.

---

## What the API Actually Knows

Before writing a line of extraction code, it is worth being precise about what the Figma REST API exposes about components and what it does not. The boundary between machine-knowable and human-required facts is the architecture decision that determines everything about how the tool should work.

The API exposes, for every published component: its name, its description field (whatever the designer typed into Figma's component description box), the node ID, the component key used for library references, and — if the component belongs to a component set — the ID of that set. [verify — current as of writing] It exposes variant properties as a key-value map: for a button that exists in the set as `Size=Large, Variant=Primary`, the `variantProperties` field on that specific component node is `{ "Size": "Large", "Variant": "Primary" }`. Collect the variant properties across all components in a set and you have the complete variant dimensions table.

| Property | Exposed by API | Notes |
|---|---|---|
| Name | Yes | String exactly as entered in Figma |
| Description | Yes | Contents of the component's description field — may be empty |
| Variant properties | Yes | Key-value map: `{ "Size": "Large", "Variant": "Primary" }` |
| Component set membership | Yes | `componentSetId` links the variant to its parent set |
| Node ID | Yes | Stable within file; may change on structural refactor |
| Component key | Yes | More stable library reference for cross-file linking |
| Code Connect link | No | Configured separately via Code Connect CLI; not in API response |
| Usage guidance | No | Intent is not a property the file graph stores |
| Accessibility semantics | No | ARIA roles and keyboard behavior live in the engineering implementation |
| Do/don't examples | No | Editorial content; authored in documentation platform, not Figma |
| Whether documentation is correct | No | The API knows what exists, not whether descriptions are accurate |

What the API does not expose is harder to list because the absence is invisible. It does not know what the component is for. The description field holds whatever a designer typed — which may be a thorough explanation, a placeholder, or nothing at all. It does not know when to use a compact card versus a default card, or why the destructive button variant is red. It does not know whether the documentation on the site matches engineering reality. It knows what exists in the file. Intent is not a property the file graph stores.

This is the contract that governs the whole chapter. The CLI can tell you "the Button component set has 47 variants and 12 of them have no description." It cannot write the missing descriptions. That boundary is not a temporary limitation waiting for a more capable model — it is a structural fact about what "description" means. A generated description that says "This is the primary large button component" is technically a non-empty string, but it is not documentation. It is the appearance of documentation, which is worse than a clearly empty field because it satisfies coverage metrics without communicating anything useful.

Publication state is one more fact worth understanding before building the tool. Library components — the ones available to other files via `GET /v1/files/:key/components` [verify — current as of writing] — are only the published subset. Draft components exist in the full file response but not in the library endpoint. If your team works in a file where components are built but not yet published, the library endpoint will undercount the full component set. The full file response includes everything. Both are useful for different purposes; the tool needs to handle both cases.

---

## The Structure of the Tool

The CLI, `sync-docs.mjs`, does four things in sequence: fetch the component list from the full file response, extract variant structures by aggregating across component sets, compute coverage metrics, and write output in three formats. The fourth thing — writing output — is where the value is: not in the API call, but in the structured, diffable artifacts that downstream systems can consume without making their own API calls.

```javascript
// sync-docs.mjs
// [verify — current as of writing] Variables API endpoint and component response shape
// [illustrative — adapt to your file structure and documentation platform]

import { writeFileSync, mkdirSync } from 'fs';
import { join } from 'path';

const TOKEN    = process.env.FIGMA_TOKEN;
const FILE_KEY = process.env.FIGMA_FILE_KEY;
const OUT_DIR  = (process.argv.find(a => a.startsWith('--out=')) || '--out=docs-sync-output').split('=')[1];

if (!TOKEN || !FILE_KEY) {
  console.error('ERROR: FIGMA_TOKEN and FIGMA_FILE_KEY required.');
  process.exit(1);
}

const BASE = 'https://api.figma.com/v1';

async function figmaGet(path) {
  const res = await fetch(`${BASE}${path}`, {
    headers: { 'X-Figma-Token': TOKEN }
  });
  if (res.status === 429) {
    const retry = parseInt(res.headers.get('Retry-After') || '30', 10);
    console.warn(`Rate limited. Waiting ${retry}s...`);
    await new Promise(r => setTimeout(r, retry * 1000));
    return figmaGet(path);
  }
  if (!res.ok) throw new Error(`Figma API ${res.status}: ${await res.text()}`);
  return res.json();
}

async function main() {
  mkdirSync(OUT_DIR, { recursive: true });
  console.log('Fetching file...');

  const fileData        = await figmaGet(`/files/${FILE_KEY}`);
  const rawComponents   = fileData.components     || {};
  const rawSets         = fileData.componentSets  || {};

  // Index component sets
  const setIndex = {};
  for (const [nodeId, set] of Object.entries(rawSets)) {
    setIndex[nodeId] = {
      nodeId,
      name:        set.name,
      description: set.description || '',
      components:  []
    };
  }

  // Process components
  const inventory  = [];
  const missingDocs = [];

  for (const [nodeId, comp] of Object.entries(rawComponents)) {
    const description = comp.description || '';
    const entry = {
      nodeId,
      name:              comp.name,
      description,
      setId:             comp.componentSetId || null,
      variantProperties: comp.variantProperties || null,
      hasDescription:    description.trim().length > 0,
      descriptionLength: description.trim().length,
      isVariant:         !!comp.componentSetId
    };

    inventory.push(entry);
    if (entry.setId && setIndex[entry.setId]) {
      setIndex[entry.setId].components.push(entry);
    }

    if (!entry.hasDescription) {
      missingDocs.push({ nodeId, name: comp.name, type: 'component', issue: 'no-description', setId: entry.setId });
    } else if (entry.descriptionLength < 20) {
      missingDocs.push({ nodeId, name: comp.name, type: 'component', issue: 'description-too-short', description, setId: entry.setId });
    }
  }

  // Build variant tables
  const variantTables = [];
  for (const [setId, set] of Object.entries(setIndex)) {
    if (set.components.length === 0) continue;

    const dimensionValues = {};
    for (const comp of set.components) {
      if (!comp.variantProperties) continue;
      for (const [dim, val] of Object.entries(comp.variantProperties)) {
        if (!dimensionValues[dim]) dimensionValues[dim] = new Set();
        dimensionValues[dim].add(val);
      }
    }

    variantTables.push({
      setId,
      setName:        set.name,
      setDescription: set.description,
      dimensions:     Object.entries(dimensionValues).map(([d, v]) => ({ dimension: d, values: [...v].sort() })),
      componentCount: set.components.length
    });

    if (!set.description.trim()) {
      missingDocs.push({ nodeId: setId, name: set.name, type: 'component-set', issue: 'no-description' });
    }
  }

  // Coverage summary
  const totalSets = Object.keys(setIndex).length;
  const summary = {
    generatedAt:           new Date().toISOString(),
    fileKey:               FILE_KEY,
    totalComponents:       inventory.length,
    withDescription:       inventory.filter(c => c.hasDescription).length,
    withoutDescription:    inventory.filter(c => !c.hasDescription).length,
    thinDescriptions:      inventory.filter(c => c.hasDescription && c.descriptionLength < 20).length,
    totalComponentSets:    totalSets,
    setsWithDescription:   Object.values(setIndex).filter(s => s.description.trim().length > 0).length,
    missingDocCount:       missingDocs.length,
    coveragePercent:       inventory.length > 0
      ? Math.round((inventory.filter(c => c.hasDescription).length / inventory.length) * 100)
      : 0
  };

  // Write outputs
  writeFileSync(join(OUT_DIR, 'component-inventory.json'), JSON.stringify({ summary, components: inventory }, null, 2));
  writeFileSync(join(OUT_DIR, 'variant-tables.json'), JSON.stringify(variantTables, null, 2));
  writeFileSync(join(OUT_DIR, 'missing-docs.json'), JSON.stringify({ summary, findings: missingDocs }, null, 2));
  writeFileSync(join(OUT_DIR, 'docs-sync-report.md'), buildMarkdownReport(summary, missingDocs, variantTables));

  console.log(`\nDone. ${summary.totalComponents} components. ${summary.coveragePercent}% have descriptions.`);
  console.log(`${missingDocs.length} documentation gaps. Output: ${OUT_DIR}/`);

  const errorCount = missingDocs.filter(f => f.type === 'component-set').length;
  if (errorCount > 0) {
    console.error(`\n${errorCount} component set(s) have no description — CI blocking.`);
    process.exit(1);
  }
}

function buildMarkdownReport(summary, missingDocs, variantTables) {
  const lines = [
    '# Component Documentation Sync Report',
    `\nGenerated: ${summary.generatedAt}`,
    '\n## Coverage Summary\n',
    '| Metric | Value |', '|--------|-------|',
    `| Total components | ${summary.totalComponents} |`,
    `| With description | ${summary.withDescription} (${summary.coveragePercent}%) |`,
    `| Without description | ${summary.withoutDescription} |`,
    `| Thin descriptions (<20 chars) | ${summary.thinDescriptions} |`,
    `| Component sets | ${summary.totalComponentSets} |`,
    `| Sets with description | ${summary.setsWithDescription} |`
  ];

  if (missingDocs.length > 0) {
    const errors   = missingDocs.filter(f => f.type === 'component-set');
    const warnings = missingDocs.filter(f => f.type === 'component' && f.issue === 'no-description');
    const infos    = missingDocs.filter(f => f.issue === 'description-too-short');

    lines.push(`\n## Documentation Gaps (${missingDocs.length})\n`);
    if (errors.length)   { lines.push(`### Errors — Sets Without Description (${errors.length})\n`);   errors.forEach(f => lines.push(`- **${f.name}** \`${f.nodeId}\``)); }
    if (warnings.length) { lines.push(`\n### Warnings — Components Without Description (${warnings.length})\n`); warnings.forEach(f => lines.push(`- ${f.name} \`${f.nodeId}\``)); }
    if (infos.length)    { lines.push(`\n### Info — Thin Descriptions (${infos.length})\n`); infos.forEach(f => lines.push(`- ${f.name}: "${f.description}" \`${f.nodeId}\``)); }
  }

  if (variantTables.length > 0) {
    lines.push('\n## Variant Property Tables\n');
    for (const vt of variantTables) {
      lines.push(`### ${vt.setName} (${vt.componentCount} variants)\n`);
      if (vt.setDescription) lines.push(`*${vt.setDescription}*\n`);
      if (vt.dimensions.length > 0) {
        lines.push('| Dimension | Values |', '|-----------|--------|');
        vt.dimensions.forEach(d => lines.push(`| ${d.dimension} | ${d.values.join(', ')} |`));
      }
      lines.push('');
    }
  }

  return lines.join('\n');
}

main().catch(err => { console.error('Fatal:', err.message); process.exit(1); });
```

Wire it into CI:

```json
{
  "scripts": {
    "docs:sync":     "node sync-docs.mjs --out=docs-sync-output",
    "docs:sync:ci":  "node sync-docs.mjs --out=docs-sync-output && cat docs-sync-output/docs-sync-report.md"
  }
}
```

---

## What You Get

After one run, four files land in `docs-sync-output/`.

`component-inventory.json` is the full component list: name, description, variant properties, set membership, coverage flags. Feed this to a documentation site generator or diff it in CI to detect new components added since the last sync.

`variant-tables.json` is the structured variant data. For each component set, it contains every variant dimension and the complete set of possible values for that dimension. A documentation platform can render this as a property table without any additional API calls. It is also the diff target: run the tool before and after a library publish, compare the two files, and every new variant value is a documentation task.

`missing-docs.json` is the actionable gap report. Three severity tiers: errors are component sets with no description — these gate the pipeline; warnings are individual components missing descriptions; info is descriptions present but fewer than twenty characters, which is almost always a placeholder rather than real documentation.

`docs-sync-report.md` is a human-readable summary of the above, suitable for attaching to a PR comment or posting in Slack when the sync job runs.

| Tier | What it covers | CI behavior | Who fixes it | Typical examples |
|---|---|---|---|---|
| Error | Component sets with no description | Blocks pipeline — `process.exit(1)` | Designer opens Figma and writes a set-level description | "Button" set with no description field; "Card" set with empty string |
| Warning | Individual component variants with no description | Logged but does not block | Designer adds descriptions to individual variant nodes | "Button/Primary/Large" missing description; unnamed icon variants |
| Info | Descriptions present but fewer than 20 characters | Logged as informational | Designer replaces placeholder with substantive text | "See Figma" (8 chars); "TBD" (3 chars); "Button variant" (14 chars) |

---

## Connecting to Documentation Platforms

The three JSON files are the integration point. How they connect depends on which platform owns the documentation.

Storybook does not consume the Figma API directly. The connection works through Code Connect — a Figma feature [verify — current as of writing] that links a Figma component to its codebase implementation by embedding a code snippet in Figma's Dev Mode panel. When configured, a developer opening the Button component in Dev Mode sees the actual import statement and usage example alongside the design. Code Connect requires explicit configuration per component: installing the CLI (`npm install --save-dev @figma/code-connect`), creating a `.figma.connect.ts` file that maps the Figma node ID to the real component and its prop mappings, and running `figma connect publish` to push the mappings to Figma. It is not inferred automatically, and the missing-docs report should flag components without Code Connect links as documentation debt separate from missing descriptive text.

![Code Connect data flow diagram: Figma component node maps to a Code Connect config file, which is pushed via figma connect publish to the Figma Dev Mode panel, where the developer sees the import statement and usage example. A dashed path shows sync-docs.mjs detecting absence of Code Connect.](../images/10-component-documentation-sync-fig-01.png)

*Figure 10.1 — Code Connect data flow*

Zeroheight and Supernova both have native Figma integrations that sync component thumbnails and some metadata. [verify — current as of writing for both platforms] The native sync does not expose variant property tables or coverage metrics. The `variant-tables.json` from `sync-docs.mjs` supplements the native data with structured property tables that editors can incorporate into component pages. The more automated path, where available, uses the platform's API to push descriptions from the inventory JSON directly — but this requires deciding which system owns the canonical description. If Figma owns it, the CLI drives the sync. If the documentation platform owns it, the CLI produces a report but does not push.

Custom portals — a Next.js or Astro static site is common for design systems — can consume the inventory JSON as a build step. A generator script reads `component-inventory.json` and produces component pages, variant tables, and gap indicators. This gives the most control but requires maintaining the generator alongside the documentation platform. The tradeoff is explicit: more automation, more code to own.

---

## The Failure Modes

The most common failure is not a script error. It is the empty description field. In practice, most Figma files have large numbers of components with empty or placeholder descriptions. The designer who built the component knew what it was for; they did not write it down in a field that felt optional at the time. The sync tool surfaces this systematically and accurately, but the fix requires a human opening Figma and typing. This is design-side work. The CLI produces the report. It cannot do the writing.

Published versus draft confusion is the second most common operational problem. The `GET /v1/files/:key/components` library endpoint returns only published components. [verify — current as of writing] The full file response includes draft components as well. Running the sync against the full file will include components that are not yet available for use — which inflates the inventory and may produce documentation for components that engineers cannot actually import. A `--published-only` flag that filters to components in published sets is the clean solution; the implementation is a membership check against `componentSetId` cross-referenced against the set's publication state.

Variant property drift is the failure mode that the diff workflow is specifically designed to catch. When a designer adds a new variant value in Figma, the variant tables generated by the next sync run will include the new value immediately. If Code Connect mappings and Storybook stories do not account for the new value, the documentation is stale by one sprint before the sync even runs. The operational discipline is to treat a variant table diff as a trigger for documentation work, not just a informational notification.

The documentation platform de-sync is subtler: if description content has been edited directly in the documentation platform — descriptions written in Zeroheight's editor rather than in Figma — running a sync that overwrites platform content with Figma's description will destroy human-authored content. This is the canonical reason to decide ownership explicitly before automating: Figma owns the descriptions, or the platform does, and only one of those is acceptable for a given team. Mixed ownership is not a policy; it is deferred conflict.

![Documentation ownership decision tree: two branches — descriptions authored in Figma flow to the platform via sync, overwriting platform content; descriptions authored in the platform produce reports only, with no writes back to Figma. Both branches converge on a conflict scenario when both sources have content.](../images/10-component-documentation-sync-fig-02.png)

*Figure 10.2 — Documentation ownership decision tree*

---

## The Living Style Guide, Thirty Years Later

The documentation drift problem is not new. It is the same problem that motivated the living style guide concept in the mid-2010s, which was itself the same problem that motivated annotated CSS documentation tools before that. The insight — that hand-maintained documentation becomes stale, and that the solution is to generate documentation from source — has been rediscovered repeatedly as the definition of "source" has expanded.

The living style guide tools of the 2010s — KSS, Hologram, Storybook in its early form — generated documentation from annotated CSS and component code. The source was the code. If the code changed, the docs changed. This was a genuine improvement over static documentation wikis, and it solved the problem for everything that lived in code.

The gap it did not close was the design side. Design files existed in Figma as a separate artifact. The living style guide documented code reality; it did not document design intent. When the design changed and the code did not yet reflect it, the style guide showed what was built, not what was decided. The synchronization problem lived in the gap between the design file and the code, and the living style guide straddled neither.

Code Connect and programmatic component inventory are the current generation of this idea applied to the design-side gap. The Figma file is now the upstream source; the CLI extracts structured facts from it. The boundary between machine-knowable and human-required has not moved — facts are machine-extractable, intent is not. The living style guide could tell you the CSS custom property values; it could not tell you why the primary button is that particular shade of blue. The sync tool can tell you the variant dimensions; it cannot tell you when to use `size=compact` versus `size=default`. Only the machine's territory has expanded. The human's territory is the same.

---

**LLM Exercises**

*Use these with Claude or any capable language model to deepen your understanding of the concepts in this chapter.*

**1. Generate and examine.** Run `sync-docs.mjs` against your design system file, or describe your file's component structure to the model and ask it to generate a plausible `missing-docs.json` output based on what you've described. Ask the model to identify which findings are most urgent and why, and compare its prioritization to the three-tier severity structure in this chapter.

**2. Apply to known context.** Describe your documentation platform — Storybook, Zeroheight, a custom site, or something else — and ask the model to design the integration between the three output files from `sync-docs.mjs` and that platform's data model. Ask it to identify the specific step where ownership ambiguity (Figma versus platform) is most likely to cause a conflict, and propose a decision rule for resolving it.

**3. Stress-test a specific claim.** This chapter argues that generating descriptions using an LLM — filling in the empty description fields automatically — produces the appearance of documentation without communicating anything useful, and is therefore worse than leaving fields empty. Present this argument to the model and ask it to construct the strongest counterargument: a scenario where auto-generated descriptions are genuinely better than empty fields. Then evaluate whether the counterargument changes how you would configure the CI failure threshold.

**4. Draft or audit a professional deliverable.** Write the onboarding documentation for a new design system contributor that explains how the documentation sync pipeline works, what they are responsible for maintaining (descriptions in Figma), and what the machine handles automatically. Ask the model to critique it for clarity and completeness, and to identify the single most common mistake a new contributor would make based on how the documentation is written.

---

## Chapter 10 Exercises: Component Documentation Sync
**Project:** figma-tools — Your Design System Extraction Toolkit
**This chapter adds:** `sync-docs.mjs`, which generates a component inventory, variant property tables, and a severity-classified missing-description report from the Figma library API.

### Exercise 1 — When to Use AI

You have just run `sync-docs.mjs` for the first time. The output folder contains `component-inventory.json`, `variant-tables.json`, and `missing-docs.json`. Here is where AI earns its time on this chapter's work.

**Task 1: Prioritizing the missing-docs report.** You have 47 documentation gaps across three severity tiers. An LLM can read `missing-docs.json` and explain which categories of gap — missing component-set descriptions versus thin individual-component descriptions — are most likely to cause downstream failures for engineers using the library. It can propose a triage order based on which components appear most frequently in the codebase.

*Why AI works here:* Pattern recognition and synthesis across a structured list. The model is reading data and applying judgment rules you can verify against the chapter's severity framework. No hidden state.

**Task 2: Drafting variant dimension documentation stubs.** Given a `variant-tables.json` entry — a component set name, its dimensions, and the possible values for each — an LLM can generate a structured markdown stub per dimension: what the dimension controls, what each value means structurally (not behaviorally), and what a developer should verify before using each variant.

*Why AI works here:* Templated generation from structured data. The stub structure is regular; the model fills in what can be inferred from names and values. The developer reviewing the output provides the behavioral knowledge the model cannot.

**Task 3: Explaining the sync pipeline for stakeholders.** Writing a plain-language paragraph for a design director explaining why the CLI reports component sets with no description as a CI-blocking error — and why thin descriptions do not gate the pipeline — requires synthesizing technical rules into business context. An LLM drafts this well from the chapter's severity logic.

*Why AI works here:* Translation between technical and non-technical registers. The underlying logic is explicit; the model repackages it for a different audience.

**The tell:** If the input is a machine-readable artifact from `sync-docs.mjs` — a list, a JSON file, a schema — and the output is a prioritized list, a stub, or an explanation of documented rules, AI is appropriate. When the input requires knowing what a component actually does in production, hand the work to the human who wrote it.

### Exercise 2 — When NOT to Use AI

The following tasks look like documentation work. They are not tasks AI should own.

**Task 1: Writing usage guidance for a component.** The API knows that `Button` has a `destructive` variant. It does not know when to use the destructive button versus a secondary action with a red label, what accessibility implications the color carries, or which interaction patterns it is paired with in production. An LLM presented with the component name and variant properties will generate plausible-sounding guidance. Plausible is not correct.

*Why AI fails here:* Intent fabrication. The model has no access to the decisions made when the component was designed, the user research that informed those decisions, or the production contexts where the component has and has not worked. It will produce confident text about things it cannot know.

**Task 2: Writing accessibility notes.** A11y guidance for a Figma component — which ARIA role applies, which keyboard interactions are required, which screen reader announcements are expected — cannot be derived from the component's API response. The API returns visual properties. Accessibility semantics live in the engineering implementation and in the disability experience of users, neither of which the Figma API exposes.

*Why AI fails here:* Source unavailability. Even a capable model cannot derive keyboard interaction requirements or screen reader behavior from a JSON description of a Figma node. Output that looks authoritative and is fabricated is worse than no output.

**Task 3: Resolving description ownership conflicts.** The documentation platform has descriptions that differ from the Figma descriptions. Deciding which source is canonical — and therefore which content will be overwritten — requires knowing the team's workflows, who has been maintaining which source, and what the consequences are for each direction of sync. This is an organizational decision disguised as a technical one.

*Why AI fails here:* Human authority. No model has access to the team's history, responsibilities, or risk tolerance. A model asked to resolve ownership will propose a framework and miss the specific context that makes one choice right for this team.

**The tell:** If the task requires knowing what a component does (not what it is called or what variants it has), AI cannot reliably perform it. **Series connection:** Tier 4 (AI as generator operating on structured data) requires that the structure contain the relevant information. When it does not — when the facts live in designer intent, engineering implementation, or accessibility specifications — Tier 7 wisdom applies: human-authored usage guidance and accessibility notes are irreplaceable precisely because the machine has no access to what produced them.

### Exercise 3 — LLM Exercise

**What you're building:** A variant dimension documentation assistant that produces first-draft property tables for design system engineers to review and complete.

**Tool:** Claude (standard conversation, not a Project). Why Claude: this task requires reading a JSON artifact and producing structured markdown with reasoning about what each dimension's values imply. A single conversation with the full `variant-tables.json` as context is the right scope — no persistent memory needed, no ambiguity about what the source data is.

**The Prompt:**

```
I'm building documentation for a Figma design system component library. Below is the variant-tables.json output from a CLI tool that reads the Figma API. Each entry represents a component set with its variant dimensions and possible values.

For each component set in this JSON, produce a markdown documentation stub with this structure:

## [Component Set Name]

**Variants:** [N variants across [X] dimensions]

### Properties

| Dimension | Values | What it controls |
|-----------|--------|-----------------|
| [dim]     | [val1, val2, ...] | [inferred from dimension and value names] |

### Notes for reviewers
- [One sentence on what an engineer should verify before using this component]
- [Flag any dimension name that is ambiguous or likely to cause prop-mapping confusion]

Here is the JSON:

[PASTE your variant-tables.json content here]

Important: Only describe what the dimension and value names structurally imply. Do not invent behavioral guidance, accessibility requirements, or usage rules. Mark anything you cannot infer from the names with "[REVIEWER: please complete]".
```

**What this produces:** A markdown file with one stub per component set. Dimensions, values, and inferred descriptions in table form. Explicit "[REVIEWER: please complete]" markers wherever behavioral knowledge is required. This is a starting document, not a finished one — but it cuts first-draft time substantially and surfaces ambiguous dimension names that need renaming before the docs go live.

**How to adapt this prompt:**
- *Own project:* Replace the bracketed JSON paste with your actual `variant-tables.json` output. If the file is large, paste one component set at a time.
- *ChatGPT or Gemini:* Both handle this prompt structure well. The "[REVIEWER: please complete]" instruction is important — include it explicitly to prevent the model from filling gaps with fabricated guidance.
- *Claude Project:* If you work on the same design system regularly, create a Project with `brand-rules.json` and the naming conventions from Chapter 4 as context files. The model will apply your naming conventions when inferring what dimension values mean.

**Connection to previous chapters:** The variant-tables output this prompt consumes was produced by `sync-docs.mjs`, which builds on the component-reading pattern from `figma-read.mjs` (Chapter 3) and depends on the naming conventions from Chapter 4 to make dimension names meaningful. If dimension names are not human-readable — if they follow internal codes rather than the slash convention — the model's inferences will be less useful.

**Preview of next chapter:** Chapter 11 adds `monitor-brand.mjs`, which checks every node in the file against approved colors, type scales, and WCAG contrast thresholds. The component inventory from `sync-docs.mjs` will become context for understanding which components are generating compliance findings.

### Exercise 4 — CLI Exercise

**What you're building:** Extend `sync-docs.mjs` to flag component sets that have descriptions in Figma but no Code Connect mapping — a second tier of documentation debt distinct from the missing-description report.

**Tool:** Claude Code
**Skill level:** Intermediate — requires reading two output files and producing a merged gap report.

**Setup:**
- [ ] `sync-docs.mjs` exists in your `figma-tools` project and runs without errors (`npm run docs:sync`)
- [ ] `docs-sync-output/` contains `component-inventory.json`, `variant-tables.json`, and `missing-docs.json` from a recent run
- [ ] You have installed `@figma/code-connect` (`npm install --save-dev @figma/code-connect`) and run `figma connect publish` for at least one component, or you have a `code-connect.json` file from a prior session or provided as a fixture
- [ ] Node.js 18 or later is available

**The Task:**

```
Read sync-docs.mjs and the three output files in docs-sync-output/.

Add a new check to sync-docs.mjs: after the existing missing-docs computation, read a Code Connect index from a file path passed via --code-connect=<path>. If the file does not exist or the flag is not provided, skip this check gracefully and log "Code Connect check skipped — no --code-connect file provided."

When the Code Connect file is present:
- Parse it as JSON. Assume it is a flat object keyed by component key (the `key` field in each component inventory entry), with each value containing at least an `importPath` field.
- For each component set in the variant-tables output, check whether ALL of its component variants have Code Connect mappings. A set passes if every variant's key appears in the Code Connect index. A set fails if any variant's key is absent.
- Add a new section to missing-docs.json under a key "codeConnectGaps": an array of objects with { setName, setId, missingCount, totalVariants }.
- Add a new section to docs-sync-report.md: "## Code Connect Gaps" listing each failing set with its missing count.

Do not change the existing missing-docs severity logic. Do not touch variant-tables.json or component-inventory.json. Do not make any Figma API calls that are not already in the script.

Stop after writing the updated files. Do not run the script.

Verification: run `npm run docs:sync -- --code-connect=code-connect.json` (or the path to your Code Connect file). Confirm the new section appears in docs-sync-report.md.
```

**Expected output:** `docs-sync-output/missing-docs.json` now contains a `codeConnectGaps` array. `docs-sync-output/docs-sync-report.md` has a "## Code Connect Gaps" section. If `--code-connect` is omitted, the script exits cleanly with the skip message.

**What to inspect:** Open `docs-sync-report.md` and confirm the Code Connect gap count makes sense relative to how many components you have configured Code Connect for. If you have Code Connect for zero components, all sets should appear in the gaps list.

**If it goes wrong:** If the script throws on the Code Connect file parse, check that the file is valid JSON and that it is keyed by component `key` (not node ID). If all sets appear in the gaps list when you expect some to pass, confirm the key format in your Code Connect file matches the `key` field in `component-inventory.json`.

**CLAUDE.md / AGENTS.md note:** Add this to your project's `CLAUDE.md`: "Code Connect gap checks require a `--code-connect` flag pointing to the Code Connect CLI's output JSON. The gap check is informational only — it does not change CI exit behavior unless you add a `--strict-code-connect` flag."

### Exercise 5 — AI Validation Exercise

**What you're validating:** The variant dimension documentation stubs produced in Exercise 3.
**Validation type:** Output quality and failure-mode audit.
**Risk level:** Medium — fabricated usage guidance embedded in documentation stubs can persist for months before anyone notices it is wrong.

**Setup:** Use the markdown output from Exercise 3, or generate a representative sample: paste two or three entries from `variant-tables.json` into Claude and use the Exercise 3 prompt to produce stubs. Save the output as `variant-stubs-draft.md`.

**The Validation Task:**

```
Validate the following AI-generated variant documentation stubs against this checklist. For each item, mark Pass, Fail, or N/A and write one sentence explaining your assessment.

CORRECTNESS
[ ] Every dimension name in the stub matches exactly the dimension name in the source variant-tables.json — no paraphrasing, no reordering.
[ ] Every value listed for each dimension matches exactly the values in the source — no values added, none omitted.
[ ] No dimension is described as controlling something that contradicts what its values imply (e.g., a dimension named "State" described as controlling layout).

COMPLETENESS
[ ] Every component set in the source JSON has a corresponding stub in the output — none skipped, none merged.
[ ] The reviewer note section is present for every stub, even if the model had no flags to raise.

SCOPE
[ ] No stub contains usage guidance ("use this variant when...") — only structural descriptions of what dimensions and values are.
[ ] No stub contains accessibility requirements or keyboard interaction notes.
[ ] No stub contains claims about when one variant is preferred over another.

CHAPTER-SPECIFIC: HALLUCINATED USAGE GUIDANCE
[ ] Check each "What it controls" cell in the property tables. Flag any cell that describes a behavioral rule rather than a structural one. Example of a structural description: "Controls the vertical padding of the component container." Example of a hallucinated behavioral rule: "Use Compact when space is limited on mobile screens." The latter cannot be derived from the API response — it requires knowing the design intent.
[ ] Flag any "[REVIEWER: please complete]" that was replaced by a confident-sounding description. The instruction asked the model to mark unknowns; if it filled them in instead, those cells are fabrication candidates.

CHAPTER-SPECIFIC: ACCESSIBILITY NOTES
[ ] If any stub contains a sentence about screen reader behavior, ARIA roles, keyboard focus order, or contrast compliance, flag it as out of scope. The sync tool reads names and variant properties. It has no access to semantic HTML, interaction design, or accessibility testing results.

FAILURE-MODE CHECK
[ ] Fluent but wrong: Identify the single most confident-sounding cell in the output that is most likely to be wrong. What would a developer have to verify to confirm or refute it?
[ ] AI hallucinating usage guidance: Find one instance (if any) where the model described how or when to use a variant. Rewrite that cell as a structural description or a "[REVIEWER: please complete]" marker.

What to do with your findings: Any Fail on Correctness or on the hallucination checks means the stub requires line-by-line review before it enters the documentation site. Any Fail on Scope means the stub has content that should be deleted entirely — not edited, because the model cannot generate correct content for those cells from this data source. Pass on all items: the stub is ready for the human reviewer to complete the "[REVIEWER: please complete]" sections.

AI Use Disclosure prompt (copy this into your team's documentation PR): "Variant dimension stubs in this PR were generated by Claude using the variant-tables.json output from sync-docs.mjs. The stubs describe structural properties only. Usage guidance, accessibility notes, and behavioral descriptions have been authored or reviewed by [your name]."

**Series connection:** The failure mode here — AI confidently describing usage rules it cannot know — is the same failure the chapter warns against in the auto-generated descriptions section: "A generated description that says 'This is the primary large button component' is technically a non-empty string, but it is not documentation." The validation exercise trains you to see the difference between a string that passes coverage metrics and a string that communicates something. Tier 7 wisdom: the human who designed the component holds the intent. The machine holds the structure.
```
