# Chapter 3 — Reading a Figma File Programmatically

*The map you need before the territory will let you in.*

---

Forty-seven thousand lines. That is what you get when you pipe a medium-sized design system file through the Figma REST API and count what comes back. The data is all there — every component, every color value, every variable, every nested frame — and none of it is findable. You search for a component you know exists. It is in there, somewhere, nested at depth seven. You search for the color token you want. The value is there, but it is stored as `{ "r": 0.082, "g": 0.337, "b": 0.855, "a": 1 }`, not `#1558D6`. You search for variables. They are not in the main document response at all.

This is the gap between having access to data and understanding its shape. The API gave you everything. The API told you nothing.

This chapter is the map.

![Diagram showing the four top-level keys of the Figma file API response: document (node tree), components (flat ID-to-meta index), styles (color, text, and effect styles), and variables (Enterprise only, separate from the document tree), with an arrow showing how boundVariables in the document tree cross-reference the variables block](../images/03-reading-a-figma-file-programmatically-fig-01.png)
*Figure 3.1 — Four top-level keys. Variables live outside the document tree.*

---

## The Document Graph

A Figma file is a tree. Every visible object on the canvas is a node. Every invisible container — frames, groups, pages — is a node. The document itself is a node: the root.

The tree has a consistent shape at the top:

```
DOCUMENT (root, id "0:0")
  └── CANVAS (one per page)
        └── FRAME | COMPONENT | COMPONENT_SET | GROUP | ...
              └── (any node type, recursively)
```

Every node has four properties that are always present: `id`, which is unique within the file and stable for the life of the node; `name`, which is the layer name as shown in the Layers panel; `type`, which tells you what kind of node this is; and `children`, which is an array of child nodes if there are any, and absent entirely on leaf nodes.

| Type | What it is | When you encounter it | Key properties to check |
|---|---|---|---|
| `DOCUMENT` | Root of the file; always id `"0:0"` | Always the first node | `children` (one CANVAS per page) |
| `CANVAS` | A page in the file | Always a direct child of DOCUMENT | `name` (page name), `children` (top-level objects) |
| `FRAME` | A layout container — screen, section, artboard | Most common structural node | `absoluteBoundingBox`, `layoutMode`, `fills`, `children` |
| `COMPONENT` | A reusable component definition | Source of truth for a component | `name`, `description`, `fills`, `boundVariables`, `children` |
| `COMPONENT_SET` | A variant group containing COMPONENT children | When variants exist | `name`, `children` (each a COMPONENT variant) |
| `INSTANCE` | A placed use of a component | On the canvas — placed elements | `componentId` (points back to COMPONENT definition) |
| `GROUP` | A legacy grouping container | Older files; avoid for new structures | `children` |
| `TEXT` | A text layer | Labels, headings, body copy | `characters`, `style`, `styleOverrideTable`, `boundVariables` |
| `RECTANGLE` | A rectangle shape | UI panels, dividers, backgrounds | `fills`, `strokes`, `cornerRadius`, `boundVariables` |
| `VECTOR` | A vector path (icons, illustrations) | Icons, custom shapes | `fills`, `strokes` |

The types you will encounter most often are FRAME (layout containers — screens, sections, artboards), COMPONENT (a reusable component definition, with a `componentId`), COMPONENT_SET (a variant group containing COMPONENT children), INSTANCE (a placed usage of a component, with a `componentId` pointing back to the source definition), and TEXT (a text layer, with its `characters`, `style`, and `styleOverrideTable`).

Beyond those four universal properties, most nodes also carry `fills` (an array of fill objects — colors, gradients, images), `strokes`, `effects`, `absoluteBoundingBox` (position and size on canvas), and — critically — `boundVariables`.

That last one matters more than it looks. A fill that appears in the `fills` array as a solid color may actually be resolved from a variable. The `fills` array gives you the computed value; `boundVariables` tells you which variable produced it. For any pipeline that generates output a human will maintain, you almost always want the variable reference, not the resolved value. The variable is the token. The token is what you maintain. Chasing resolved values means chasing a moving target every time someone updates the design system.

---

## Where Variables Live

Variables are not embedded in the node tree. This surprises people. They appear at the top level of the file response, alongside the document, in their own section.

```json
{
  "document": { ... },
  "components": { ... },
  "styles": { ... },
  "variables": {
    "variables": {
      "VariableID:12:45": {
        "id": "VariableID:12:45",
        "name": "color/brand/primary",
        "resolvedType": "COLOR",
        "valuesByMode": {
          "12:0": { "r": 0.082, "g": 0.337, "b": 0.855, "a": 1 },
          "12:1": { "r": 0.031, "g": 0.243, "b": 0.624, "a": 1 }
        },
        "description": "Primary brand color. Use for CTAs, links, and focus rings.",
        "hiddenFromPublishing": false,
        "variableCollectionId": "VariableCollectionId:12:0"
      }
    },
    "variableCollections": {
      "VariableCollectionId:12:0": {
        "id": "VariableCollectionId:12:0",
        "name": "Brand Colors",
        "modes": [
          { "modeId": "12:0", "name": "Light" },
          { "modeId": "12:1", "name": "Dark" }
        ],
        "defaultModeId": "12:0"
      }
    }
  }
}
```

Variables live in collections. A collection has one or more modes — Light and Dark, Mobile and Desktop, Brand A and Brand B. Each variable has a value per mode. If you want the dark-mode value of `color/brand/primary`, you look up mode ID `12:1` in `valuesByMode`. The value at that key is either a raw value (a color, a number, a string, a boolean) or an alias reference.

Aliases are where it gets interesting. A variable value that looks like `{ "type": "VARIABLE_ALIAS", "id": "VariableID:11:30" }` is a pointer to another variable. That target variable may itself be an alias. You follow the chain until you hit a terminal raw value. This indirection is intentional — it is how design systems implement semantic tokens that point at primitive tokens — and your extraction code needs to handle it explicitly or you will silently produce wrong output.

![Diagram showing the alias chain from a COMPONENT node's boundVariables reference through semantic token "color/brand/primary" (VariableID:12:45), which has a VARIABLE_ALIAS value pointing to primitive "color/primitive/blue-500" (VariableID:11:30), which holds the terminal raw color value {r:0.082, g:0.337, b:0.855} equivalent to #1558D6](../images/03-reading-a-figma-file-programmatically-fig-02.png)
*Figure 3.2 — Alias chain: semantic → primitive → terminal value. Resolve per mode.*

One more thing to know about variables before we move on: the `variables` field in the file response may not appear at all, or may appear empty, depending on your plan tier. [verify — current behavior by plan tier] This is not an error in your code. It is a gate Figma has placed between plan tiers and programmatic variable access. If your variable inventory comes back empty on a file where you know variables exist, check the plan before debugging the extraction logic.

---

## Components and Styles: Two Places Each

Components appear in two places in the response, for two different purposes.

At the top level, `components` is a flat index — a map from component ID to metadata. Each entry has the component's name, description, stable library key, and the ID of its component set if it belongs to a variant group. This is what you use for a component inventory: you iterate the map, you do not need to touch the document tree at all.

In the document tree, COMPONENT and COMPONENT_SET nodes are the actual definitions, with their full property graphs — fills, text layers, layout constraints, bound variables. This is what you need when you want to inspect a specific component's structure.

Figma has two token-like systems, and a fully instrumented file may use both. Styles are the older system: named, reusable fills, text properties, effects, grids. Variables are the newer system, introduced as Figma's answer to design tokens, with multi-mode support, alias chains, and a richer data model. Most design systems are somewhere in the middle of migrating from styles to variables. Your extraction code has to handle both or it will miss whichever system a given file happens to use.

---

## What Is Not in the Response

This matters as much as what is in it.

Prototype interactions are absent. The connections between frames, the trigger types, the animation settings, the smart-animate configurations — none of this is in the document graph returned by `GET /v1/files/:key`. [verify — whether prototype data is at a separate endpoint] It exists in the Figma editor, but it does not come through the REST surface this chapter works with.

Font files are absent. You can see which font families and weights are in use by inspecting the `style` properties on TEXT nodes. You cannot download the fonts through the Figma API. If your pipeline needs fonts, source them independently.

Component usage counts are absent. The REST API tells you that a component exists and where it is defined. It does not tell you how many times it has been placed across a file or a team. That number is visible in the Figma UI; it is not programmatically accessible via REST. [verify — current REST capabilities for usage data]

Plugin-private data is absent. Data stored through the Plugin API's `setPluginData` method is not accessible via REST. If a plugin is being used to store token metadata alongside components, you cannot reach it from outside Figma without the plugin itself.

| Data | In `GET /v1/files/:key`? | Where to get it if not here |
|---|---|---|
| Document node tree | Yes — `document` key | This endpoint |
| Components flat index | Yes — `components` key | This endpoint |
| Styles flat index | Yes — `styles` key | This endpoint |
| Variables and collections | Only on Enterprise plan — `variables` key | Plugin API (in-app); Tokens Studio export for non-Enterprise |
| Prototype interactions | No | Separate prototype endpoint (verify current availability) |
| Font files | No | Source independently from font vendor or CDN |
| Component usage counts | No | Figma UI (Assets panel); not available via REST |
| Plugin-private data (`setPluginData`) | No | Only accessible from within the plugin that stored it |

---

## `figma-read.mjs`

The tool that makes this chapter concrete is `figma-read.mjs`. It fetches the file, writes a local fixture, and produces three outputs: a component inventory, a variable inventory, and a missing-description report. It is the second named CLI artifact in this book.

The `.mjs` extension signals ES module format. Node 18 and later support top-level `await` in ES modules — use it here rather than wrapping everything in an immediately-invoked async function.

```javascript
#!/usr/bin/env node
// figma-read.mjs — Figma file reader, fixture writer, and inventory reporter
// Illustrative. Review and adapt before use in production.
// Usage: FIGMA_TOKEN=... FIGMA_FILE_KEY=... node figma-read.mjs

import fs from 'fs';
import path from 'path';
import { fileURLToPath } from 'url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));

const FIGMA_TOKEN = process.env.FIGMA_TOKEN;
const FIGMA_FILE_KEY = process.env.FIGMA_FILE_KEY;
const BASE_URL = 'https://api.figma.com'; // [verify — current base URL]

if (!FIGMA_TOKEN || !FIGMA_FILE_KEY) {
  console.error('Error: FIGMA_TOKEN and FIGMA_FILE_KEY must be set.');
  process.exit(1);
}

// ── 1. Fetch the file ─────────────────────────────────────────────────────────

async function fetchFile(fileKey) {
  const url = `${BASE_URL}/v1/files/${fileKey}`;
  console.log(`Fetching: ${url}`);

  const res = await fetch(url, {
    headers: { 'X-Figma-Token': FIGMA_TOKEN }, // [verify — header name current]
  });

  if (res.status === 429) {
    const retryAfter = parseInt(res.headers.get('Retry-After') || '10', 10);
    console.warn(`Rate limited. Wait ${retryAfter}s before retrying.`);
    process.exit(2);
  }

  if (!res.ok) {
    const body = await res.json().catch(() => ({}));
    console.error(`Figma API error: ${res.status} — ${body.err || 'unknown'}`);
    process.exit(1);
  }

  return res.json();
}

// ── 2. Write a local fixture ──────────────────────────────────────────────────

function writeFixture(data, fileKey) {
  const dir = path.join(__dirname, '.figma-fixtures');
  fs.mkdirSync(dir, { recursive: true });
  const filename = `${fileKey}-${new Date().toISOString().slice(0, 10)}.json`;
  const filepath = path.join(dir, filename);
  fs.writeFileSync(filepath, JSON.stringify(data, null, 2), 'utf-8');
  console.log(`Fixture written: ${filepath}`);
  return filepath;
}

// ── 3. Walk the node tree ─────────────────────────────────────────────────────

function walk(node, visitor, depth = 0) {
  visitor(node, depth);
  if (node.children) {
    for (const child of node.children) {
      walk(child, visitor, depth + 1);
    }
  }
}

// ── 4. Extract component inventory ───────────────────────────────────────────

function extractComponents(data) {
  const inventory = [];
  const componentMap = data.components || {};

  for (const [id, meta] of Object.entries(componentMap)) {
    inventory.push({
      id,
      name: meta.name,
      description: meta.description || '',
      key: meta.key,
      componentSetId: meta.componentSetId || null,
      hasDescription: Boolean(meta.description && meta.description.trim()),
    });
  }

  return inventory.sort((a, b) => a.name.localeCompare(b.name));
}

// ── 5. Extract variable inventory ────────────────────────────────────────────

function hexFromColor(color) {
  const r = Math.round(color.r * 255).toString(16).padStart(2, '0');
  const g = Math.round(color.g * 255).toString(16).padStart(2, '0');
  const b = Math.round(color.b * 255).toString(16).padStart(2, '0');
  const hex = `#${r}${g}${b}`.toUpperCase();
  if (color.a !== undefined && color.a < 1) {
    const a = Math.round(color.a * 255).toString(16).padStart(2, '0');
    return `${hex}${a}`;
  }
  return hex;
}

function resolveValue(value, variables) {
  if (value && value.type === 'VARIABLE_ALIAS') {
    const target = variables[value.id];
    if (!target) return { type: 'UNRESOLVED_ALIAS', id: value.id };
    const firstModeId = Object.keys(target.valuesByMode)[0];
    return resolveValue(target.valuesByMode[firstModeId], variables);
  }
  if (value && typeof value === 'object' && 'r' in value) {
    return { type: 'COLOR', hex: hexFromColor(value), raw: value };
  }
  return { type: 'SCALAR', raw: value };
}

function extractVariables(data) {
  const variablesData = data.variables;
  if (!variablesData || !variablesData.variables) {
    return { available: false, variables: [], collections: [] };
  }

  const rawVars = variablesData.variables;
  const rawCollections = variablesData.variableCollections || {};

  const collections = Object.entries(rawCollections).map(([id, col]) => ({
    id,
    name: col.name,
    modes: col.modes,
    defaultModeId: col.defaultModeId,
  }));

  const variables = Object.entries(rawVars).map(([id, v]) => {
    const collection = rawCollections[v.variableCollectionId];
    const modes = collection ? collection.modes : [];
    const valuesByMode = {};
    for (const { modeId, name } of modes) {
      const raw = v.valuesByMode[modeId];
      valuesByMode[name] = resolveValue(raw, rawVars);
    }
    return {
      id,
      name: v.name,
      resolvedType: v.resolvedType,
      description: v.description || '',
      collectionName: collection ? collection.name : 'unknown',
      hiddenFromPublishing: v.hiddenFromPublishing,
      valuesByMode,
    };
  });

  return { available: true, variables, collections };
}

// ── 6. Reports ────────────────────────────────────────────────────────────────

function reportComponents(components) {
  const total = components.length;
  const withDescription = components.filter((c) => c.hasDescription).length;
  const missing = components.filter((c) => !c.hasDescription);

  console.log(`\n── Component inventory ──`);
  console.log(`  Total components: ${total}`);
  console.log(`  With description: ${withDescription}`);
  console.log(`  Missing description: ${missing.length}`);

  if (missing.length > 0) {
    console.log(`\n  Components missing descriptions:`);
    for (const c of missing.slice(0, 10)) {
      console.log(`    - ${c.name} (${c.id})`);
    }
    if (missing.length > 10) {
      console.log(`    ... and ${missing.length - 10} more`);
    }
  }

  return { total, withDescription, missingDescription: missing.length, components, missingDescriptionComponents: missing };
}

function reportVariables(variableData) {
  console.log(`\n── Variable inventory ──`);
  if (!variableData.available) {
    console.log('  Variables API not available (likely non-Enterprise plan).');
    console.log('  Use Tokens Studio or Plugin API for token extraction (Chapter 8).');
    return variableData;
  }

  const { variables, collections } = variableData;
  console.log(`  Collections: ${collections.length}`);
  console.log(`  Variables: ${variables.length}`);

  for (const col of collections) {
    const colVars = variables.filter((v) => v.collectionName === col.name);
    console.log(`\n  Collection: ${col.name} (${col.modes.map((m) => m.name).join(', ')})`);
    console.log(`    Variables: ${colVars.length}`);
    for (const v of colVars.slice(0, 5)) {
      console.log(`    - ${v.name} [${v.resolvedType}]`);
    }
    if (colVars.length > 5) console.log(`    ... and ${colVars.length - 5} more`);
  }

  return variableData;
}

function reportPages(data) {
  const pages = data.document.children || [];
  console.log(`\n── Page / frame structure ──`);
  console.log(`  Pages: ${pages.length}`);
  for (const page of pages) {
    const topLevel = page.children || [];
    console.log(`\n  Page: "${page.name}"`);
    console.log(`    Top-level objects: ${topLevel.length}`);
    const types = {};
    for (const node of topLevel) types[node.type] = (types[node.type] || 0) + 1;
    for (const [type, count] of Object.entries(types)) {
      console.log(`      ${type}: ${count}`);
    }
  }
}

// ── 7. Write machine-readable output ─────────────────────────────────────────

function writeOutput(components, variableData, outputDir) {
  fs.mkdirSync(outputDir, { recursive: true });

  const componentInventoryPath = path.join(outputDir, 'component-inventory.json');
  fs.writeFileSync(
    componentInventoryPath,
    JSON.stringify({
      generatedAt: new Date().toISOString(),
      fileKey: FIGMA_FILE_KEY,
      total: components.total,
      withDescription: components.withDescription,
      missingDescription: components.missingDescription,
      components: components.components,
    }, null, 2),
    'utf-8'
  );
  console.log(`\nOutput written: ${componentInventoryPath}`);

  if (components.missingDescriptionComponents.length > 0) {
    const missingPath = path.join(outputDir, 'missing-descriptions.md');
    const lines = [
      '# Components Missing Descriptions',
      '',
      `Generated: ${new Date().toISOString()}`,
      `File key: ${FIGMA_FILE_KEY}`,
      '',
      `${components.missingDescriptionComponents.length} components have no description field.`,
      '',
      '| Name | ID |',
      '|---|---|',
      ...components.missingDescriptionComponents.map((c) => `| ${c.name} | ${c.id} |`),
    ];
    fs.writeFileSync(missingPath, lines.join('\n'), 'utf-8');
    console.log(`Output written: ${missingPath}`);
  }

  if (variableData.available) {
    const variablesPath = path.join(outputDir, 'variable-inventory.json');
    fs.writeFileSync(
      variablesPath,
      JSON.stringify({
        generatedAt: new Date().toISOString(),
        fileKey: FIGMA_FILE_KEY,
        collections: variableData.collections,
        variables: variableData.variables,
      }, null, 2),
      'utf-8'
    );
    console.log(`Output written: ${variablesPath}`);
  }
}

// ── Main ──────────────────────────────────────────────────────────────────────

console.log('=== figma-read: reading file and building inventory ===');

const data = await fetchFile(FIGMA_FILE_KEY);
const fixturePath = writeFixture(data, FIGMA_FILE_KEY);

reportPages(data);
const components = reportComponents(extractComponents(data));
const variableData = reportVariables(extractVariables(data));

const outputDir = path.join(__dirname, 'figma-output');
writeOutput(components, variableData, outputDir);

console.log('\n=== figma-read complete ===');
console.log(`Fixture: ${fixturePath}`);
console.log(`Output: ${outputDir}/`);
```

Running it requires nothing beyond Node 18 — no dependencies, no package installation, no build step:

```bash
node figma-read.mjs
```

The expected output on a real design system file is not the tidy inventory you might hope for. A missing-description count of 124 out of 147 components is not unusual. That finding is not a failure of the script. It is a finding about the file — and Chapter 5 will turn findings like that into structured audit output that a team can act on.

---

## The walk Function

The `walk` function is three lines long and is the foundation of every traversal in this book:

```javascript
function walk(node, visitor, depth = 0) {
  visitor(node, depth);
  if (node.children) {
    for (const child of node.children) {
      walk(child, visitor, depth + 1);
    }
  }
}
```

It is a pre-order depth-first traversal. The visitor sees a parent node before any of its children. When the visitor is called, it receives the node and its depth in the tree — depth is useful when you need to limit traversal or format output hierarchically.

If you have written JavaScript for the DOM, you have written something functionally identical. `document.querySelectorAll('.button')` is a tree traversal with a built-in selector engine; `walk` exposes the same traversal as a general-purpose visitor so you can write any selector you need.

The same pattern appears in TypeScript's compiler (visiting AST nodes), Babel's transform pipeline (visiting and transforming AST nodes), and ESLint's rule engine (visiting AST nodes with a rule function as the visitor). The Figma document tree is a gentler introduction than any of those: the node types correspond to things you can see on the canvas, the structure is rarely more than ten levels deep, and there are no type narrowing puzzles. If you have never written a tree traversal before, this is a good place to start.

From `walk`, building specific queries is direct. Finding all instances of a component:

```javascript
function findInstances(document, componentId) {
  const instances = [];
  walk(document, (node) => {
    if (node.type === 'INSTANCE' && node.componentId === componentId) {
      instances.push(node);
    }
  });
  return instances;
}
```

Finding all fills that are hardcoded colors, not bound to variables:

```javascript
function findHardcodedColors(document) {
  const findings = [];
  walk(document, (node) => {
    if (!node.fills) return;
    for (const fill of node.fills) {
      if (fill.type !== 'SOLID') continue;
      const isBound = node.boundVariables &&
        node.boundVariables.fills &&
        node.boundVariables.fills.some((b) => b);
      if (!isBound) findings.push({ nodeId: node.id, nodeName: node.name, fill });
    }
  });
  return findings;
}
```

That second function is the embryo of the audit logic Chapter 5 formalizes. Understanding the traversal here — why it works, what it is actually checking — means you can write any variant you need.

![Annotated tree diagram showing walk() visit order numbered 1 through 7: CANVAS at depth 0 (visit 1), FRAME at depth 1 (visit 2), COMPONENT_SET at depth 2 (visit 3), then Button/Default COMPONENT at depth 3 (visit 4) and its TEXT child at depth 4 (visit 5), then Button/Hover COMPONENT at depth 3 (visit 6) and its TEXT child at depth 4 (visit 7)](../images/03-reading-a-figma-file-programmatically-fig-03.png)
*Figure 3.3 — walk() pre-order traversal: parent before children, siblings left to right.*

---

## The Local Fixture

The fixture written by `figma-read.mjs` is not a nice-to-have. It is a core practice.

A full file fetch on a large design system takes several seconds and consumes rate-limit quota. Figma imposes rate limits on the REST API [verify — current rate limit values and headers]. If you are iterating on extraction logic — debugging a variable resolver, adjusting a traversal, testing a new output format — hitting the live API on every run means slow feedback loops and the real possibility of exhausting your quota mid-session. The fixture is a local snapshot of the full response, written on the first fetch, used for every subsequent run during development.

The pattern is a two-line conditional in any script that follows:

```javascript
const data = FIXTURE_PATH
  ? JSON.parse(fs.readFileSync(FIXTURE_PATH, 'utf-8'))
  : await fetchFile(FIGMA_FILE_KEY);
```

Two rules for fixture hygiene. First, add `.figma-fixtures/` to `.gitignore`. Fixtures contain your file structure, which may include unpublished components and internal naming. Do not commit them to a public repository. Second, name fixtures with the file key and date so you know exactly when they were generated — a fixture that is two weeks old is still useful for testing traversal logic, but not for auditing the current state of the file.

---

## Three Read Strategies

Not every question about a Figma file requires the full 47,000-line response. There are three read strategies, each suited to different questions.

`GET /v1/files/:key` — the full fetch — returns the complete document tree, all component metadata, all style metadata, and the variables block. Use this when you need to traverse the full graph: complete audits, compliance checks, any analysis where you do not know in advance which nodes you will care about. Accept that this is slow on large files. Write the fixture after the first fetch.

`GET /v1/files/:key?depth=N` — the depth-limited fetch — returns only the top N levels of the tree. [verify — depth parameter current behavior] Use this for structural reads: getting page names, counting top-level frames, checking whether certain node types are present at the top level. It is substantially faster on large files because the response is smaller by orders of magnitude.

`GET /v1/files/:key/nodes?ids=ID1,ID2,...` — the targeted fetch — returns specific nodes and their subtrees. Use this when you have a list of node IDs from a previous structural pass and need the full property graph of just those nodes. An audit script that first does a depth-limited structural pass to collect a list of component IDs, then fetches only those component subtrees, is much more efficient than fetching the entire file when only a fraction of it is relevant.

| Endpoint | When to use it | Speed | Tradeoffs |
|---|---|---|---|
| `GET /v1/files/:key` | Full audits, complete traversals, any analysis where you do not know in advance which nodes you need | Slow on large files (47k+ lines for a design system) | Complete but expensive; write a fixture after the first fetch |
| `GET /v1/files/:key?depth=N` | Structural reads: page names, top-level frame types, checking node presence without traversing subtrees | Fast — response is orders of magnitude smaller | Does not include full property graph; use for discovery, not extraction |
| `GET /v1/files/:key/nodes?ids=ID1,ID2,...` | Targeted extraction: you have a list of component IDs from a prior structural pass and need their full property graphs | Fast for small node sets; scales with the number of IDs | Requires a prior pass to collect IDs; building that pass requires the full fetch or a structural pass first |

The general workflow: do a full fetch once, write the fixture, develop against the fixture. In production, decide based on what you need — structural reads get the depth-limited endpoint, targeted reads get the nodes endpoint, full audits get the full fetch with rate-limit awareness.

---

## The Output Shape

`figma-read.mjs` produces three files. These are the stable interface for everything downstream.

`component-inventory.json` is a flat list of all component definitions: names, IDs, descriptions, and a boolean `hasDescription`. It is consumed by documentation sync in Chapter 10, audit tooling in Chapter 5, and specification generation in Chapter 12.

`variable-inventory.json` is a flat list of all variables with their collection membership, resolved type, description, and computed values per mode. It is consumed by token extraction in Chapter 8.

`missing-descriptions.md` is a markdown table of every component with an empty description field. It is a human-readable work item list — the kind of thing you share in a Slack channel or file as a GitHub issue rather than feeding to a program.

The discipline of producing these three files as a stable output shape, regardless of how the extraction logic evolves, is what allows downstream tools to depend on `figma-read.mjs` without coupling to its internals.

![Flow diagram with figma-read.mjs as a central black node taking two inputs — Figma REST API (live fetch, red border) and local fixture (.figma-fixtures/*.json, dashed arrow) — and producing three outputs: component-inventory.json consumed by Chapter 5 audit, Chapter 10 docs sync, and Chapter 12 spec generation; variable-inventory.json consumed by Chapter 8 token extraction; and missing-descriptions.md for the design team](../images/03-reading-a-figma-file-programmatically-fig-04.png)
*Figure 3.4 — figma-read.mjs as the stable interface for downstream tools.*

---

## Failure Modes

Understanding how this script fails is as important as understanding how it works.

**The large-file timeout.** For files with hundreds of components and deep nesting, the full fetch can take a long time — sometimes tens of seconds. If the connection drops before the response completes, you get an error rather than a partial response. There is no partially-delivered JSON here; the API returns the full body or nothing. Mitigation: add retry logic for the initial fetch, and consider whether the depth-limited or targeted strategies would serve your question better.

**The partial variable response.** On some plan configurations the `variables` block in the response may be present but empty — not absent, not a 403, just an empty object. This is different from the Enterprise plan gate (which returns a 403 directly). If your variable inventory reports zero variables but you know the file has variables, check: are the variables published? Local-only variables may not appear in all API response contexts. [verify — current visibility rules for unpublished variables]

**Stale node IDs.** Node IDs are stable for the lifetime of a node. When a designer deletes a component and recreates it, the recreated component has a new ID. Any downstream reference by the old ID is now broken. Fixtures written before the recreation carry the old IDs. This is a fundamental property of the data model, not a bug in your extraction code. Treat node IDs as stable within a session and across minor edits — not across file refactors.

**The archive page problem.** Most real design system files have an archive page where deprecated components live. `figma-read.mjs` as written includes everything in the file, including archived content, so the component count may be higher than you expect. If you want to exclude archived pages, filter by name before extracting:

```javascript
const EXCLUDED_PAGES = ['_Archive', 'Archive', '🗑️ Archive'];

const activePages = data.document.children.filter(
  (page) => !EXCLUDED_PAGES.some(
    (excluded) => page.name.toLowerCase().includes(excluded.toLowerCase())
  )
);
```

**Instance vs. definition confusion.** The `components` top-level map contains component definitions. Instances of those components appear in the document tree as nodes with `type === 'INSTANCE'` and a `componentId` pointing back to the definition. A search for `COMPONENT` nodes in the document tree finds definitions. A search for `INSTANCE` nodes finds usages. These are different questions. Conflating them is the most common beginner mistake in Figma graph traversal.

---

## What Comes Next

You can now read any Figma file programmatically. You have a fixture for offline development. You have a component inventory, a variable inventory, and a missing-description report — three files that are the stable input for every downstream tool in this book.

The gap that remains is not about reading. It is about trust. You can extract what the file contains; you cannot yet determine whether the file is structured in a way that a pipeline can rely on. Chapter 4 addresses naming as an API contract — why the names your designer gives to variables and components determine what your pipeline produces, and what the consequences are when those names drift. Chapter 5 builds the audit tool that checks a file systematically before you stake a production pipeline on it.

You know what the file contains. Now let's find out whether it is ready to be consumed.

---

## LLM Exercises

**Exercise 1 — Generate and examine.**
Paste the `walk` function and the `findHardcodedColors` function into a conversation with an LLM. Ask it to explain, step by step, what `boundVariables.fills.some((b) => b)` is checking and why a fill could pass the `fill.type === 'SOLID'` check but still be a variable-bound color. Ask the LLM to identify any edge case where this check would produce a false positive — a fill the code treats as hardcoded that is actually variable-bound.

**Exercise 2 — Apply to known context.**
Take the component inventory output produced by `figma-read.mjs` on a file you have access to. Paste a sample of the JSON (ten to twenty components) into an LLM conversation. Ask it to generate a shell command using `jq` that would filter the JSON to only components whose `name` starts with a specific prefix — for example, all components in the `Button /` family. Run the command and confirm the output is correct.

**Exercise 3 — Stress-test a specific claim.**
The chapter claims that you should almost always prefer the variable reference over the resolved fill value when building a pipeline. Ask an LLM to argue the opposite case: when would you specifically want the resolved value rather than the variable reference? Ask it to describe a concrete pipeline scenario where chasing the resolved value is the right choice. Evaluate the argument against what you know about your own design system.

**Exercise 4 — Draft a professional deliverable.**
Using `missing-descriptions.md` output from a real run of `figma-read.mjs` (or a plausible fabricated sample if you do not have access to a Figma file), ask an LLM to draft a brief message to your design team explaining the findings and requesting that descriptions be added. The message should explain why component descriptions matter for pipeline automation without being condescending. Revise the draft based on your knowledge of how your team communicates.

---

## Chapter 3 Exercises: Reading a Figma File Programmatically
**Project:** figma-tools — Your Design System Extraction Toolkit
**This chapter adds:** `figma-read.mjs` — fetches the full file, writes a local fixture, and produces three stable output files: `component-inventory.json`, `variable-inventory.json`, and `missing-descriptions.md`.

---

### Exercise 1 — When to Use AI

Three tasks in this chapter's work are good fits for AI assistance.

**Explaining an unfamiliar part of the response graph.** You paste a fragment of the raw Figma file response — a `boundVariables` entry, a `COMPONENT_SET` node, an alias chain — and ask the model to explain what each field means and how the extraction code should handle it. The response shape is documented; the model has seen it; the explanation is retrieval and synthesis, not judgment.
*Why AI works here:* pattern — the Figma document graph has a fixed schema. Explaining what fields mean in context of that schema is a pattern-completion task grounded in documentation that exists in the model's training data.

**Generating `jq` or JavaScript snippets to query a fixture.** You have a 47,000-line fixture file and you want to find all `COMPONENT_SET` nodes, or all fills where `boundVariables` is absent. AI can produce the `jq` filter or the `walk` visitor function for that query. You test the output against the real fixture.
*Why AI works here:* boilerplate — writing tree traversal visitors and JSON query expressions for a known schema is a mechanical task. You provide the target; the model provides the code. You verify it runs correctly against the fixture.

**Drafting the `missing-descriptions.md` message for the design team.** Once you have the actual count of components missing descriptions, AI can draft the message — explaining why descriptions matter for pipeline automation, setting the right tone, proposing a process for filling them in. You revise based on your knowledge of your team.
*Why AI works here:* drafting — writing a professional message that translates a technical finding into a request for action is a drafting task. The finding (the count and the component names) is yours; the communication is the model's contribution.

**The tell:** every output in this exercise category is verifiable against the actual fixture file or the actual tool output. If the model produces a `jq` query or a `walk` visitor, run it. If it explains a field, look it up in the raw fixture. Ground truth is immediately available.

---

### Exercise 2 — When NOT to Use AI

Three judgment calls in this chapter must be yours.

**Deciding which pages to exclude from the inventory.** `figma-read.mjs` includes all pages, including archive pages. The chapter shows a code pattern for excluding pages by name. The names of archive pages in your specific file are a local fact — `_Archive`, `🗑️ Deprecated`, `OLD - Do Not Use` — that the model does not know. Worse, if you ask the model to suggest which pages to exclude, it will produce a list of reasonable-sounding page names that may or may not match what your file actually contains. The only way to know is to run the script and read the page report.
*Why AI fails here:* missing ground truth — the model has no access to your file's actual page structure.

**Interpreting what a high missing-description count means for your team.** `figma-read.mjs` might report that 124 of 147 components lack descriptions. That number is a fact. What it means — whether it represents a process failure, a recent component explosion, a deliberate choice to skip documentation for internal-only components — requires knowing your team's history. A model will offer a plausible interpretation. It will not know whether those 124 components are archived variants that will never be documented, or active production components that someone was supposed to document three months ago.
*Why AI fails here:* missing ground truth — interpreting a count requires context about the history and intent behind the data that the model does not have.

**Deciding whether to resolve alias chains to raw values or preserve the alias references in your inventory.** The chapter explains the tradeoff: resolved values are immediately usable but drift from the design system; alias references require downstream resolution but stay correct when the design system changes. This decision depends on what downstream tools will consume the inventory and how they are built. No model can make this call without knowing your downstream architecture — and even if you describe it, the model will offer a recommendation based on general principles rather than specific knowledge of your build pipeline.
*Why AI fails here:* calibration — the model will recommend one option or the other based on common patterns. Your actual pipeline may have constraints that make the "common" answer wrong.

**The tell:** the model's interpretation of your file's data will sound reasonable and specific. Reasonableness is not the same as correctness when the ground truth is a specific file you own and the model has never seen.

**Series connection:** Chapter 3 is Tier 4 — judgment calls grounded in specific file content and downstream architecture. The key failure mode is AI assuming graph fields or response structures that are not actually present in your file's response — producing schema-valid but wrong analysis.

---

### Exercise 3 — LLM Exercise

**What you're building:** An enhanced alias resolution function — a version of `resolveValue` that handles multi-hop alias chains fully, reports the full resolution path, and flags circular references rather than silently failing.

**Tool:** Claude (claude.ai or API). This is a code generation task grounded in a specific, well-defined schema — a good fit for a focused conversation where you paste the relevant code and describe the desired behavior.

**The Prompt:**

```
I am building a Figma design token extractor in Node.js. I have a function that resolves variable alias chains:

function resolveValue(value, variables) {
  if (value && value.type === 'VARIABLE_ALIAS') {
    const target = variables[value.id];
    if (!target) return { type: 'UNRESOLVED_ALIAS', id: value.id };
    const firstModeId = Object.keys(target.valuesByMode)[0];
    return resolveValue(target.valuesByMode[firstModeId], variables);
  }
  if (value && typeof value === 'object' && 'r' in value) {
    return { type: 'COLOR', hex: hexFromColor(value), raw: value };
  }
  return { type: 'SCALAR', raw: value };
}

This function has three problems I want to fix:
1. It only resolves using the first mode ID, not a specified mode. I want to pass a target mode ID as a parameter.
2. It does not detect circular references — if variable A aliases B and B aliases A, it will loop forever.
3. It does not record the resolution path — I want the output to include the chain of variable names traversed to reach the final value, so I can debug alias chains in the design system.

Please rewrite resolveValue to:
- Accept a third parameter: modeId (the target mode to resolve for)
- Detect circular references using a visited set; if a circular reference is found, return { type: 'CIRCULAR_ALIAS', chain: [...visitedIds] }
- Include a resolutionPath array in the output: an array of { id, name } objects for each variable in the chain, in order
- Keep the same return structure for COLOR and SCALAR terminal values, but add resolutionPath to them

The variables object has this shape:
{
  "VariableID:12:45": {
    "id": "VariableID:12:45",
    "name": "color/brand/primary",
    "valuesByMode": {
      "12:0": { "r": 0.082, "g": 0.337, "b": 0.855, "a": 1 },
      "12:1": { "type": "VARIABLE_ALIAS", "id": "VariableID:11:30" }
    }
  }
}

Please also write three unit test cases using plain JavaScript (no test framework) that verify:
1. A direct value resolves correctly with an empty resolutionPath
2. A single-hop alias resolves to the terminal value with a one-entry resolutionPath
3. A circular reference returns { type: 'CIRCULAR_ALIAS', chain: [...] }
```

**What this produces:** A production-ready `resolveValue` function with circular-reference detection and resolution path tracking, plus three self-contained test cases you can run with `node` directly. The function replaces the version in `figma-read.mjs` and produces richer output in `variable-inventory.json`.

**How to adapt this prompt:**
- *For your own project:* if your variable schema differs from what the chapter shows (some design systems use a flat value rather than `valuesByMode`), paste a real fragment from your fixture file and ask the model to adapt the function to that shape.
- *For ChatGPT or Gemini:* the prompt works as written. Compare whether the circular-reference detection approach differs across models — some may use a counter, some a Set, some a string concatenation. All can work; compare which is clearest to you.
- *For a Claude Project:* set a system prompt that includes the full `figma-read.mjs` source and the fixture schema so the model always has the actual implementation context when you iterate on the function.

**Connection to previous chapters:** The alias chain structure this function resolves was introduced in Chapter 2 (the Variables API section) and surfaced by `figma-read.mjs` in this chapter. The fixture written by `figma-read.mjs` is the input you will use to test this function before replacing the original implementation.

**Preview of next chapter:** Chapter 4 builds on the naming structure of variable IDs and collection names to establish naming as an API contract. The resolution path this function now records — the full chain of variable names — is exactly the data Chapter 4's audit logic will inspect for naming convention violations.

---

### Exercise 4 — CLI Exercise

**What you're building:** `figma-read.mjs` — added to the figma-tools repository, wired to the existing `.env` setup, and confirmed producing the three output files against a real Figma file or a local sample fixture.

**Tool:** Claude Code

**Skill level:** Intermediate — you are adding the second named CLI artifact to the repository and testing it against a real API response. The fixture-writing behavior is new; the `.env` and `CLAUDE.md` conventions carry over from Chapter 2.

**Setup:**
- [ ] figma-tools repo exists with `scripts/figma-ping.js`, `.env` with real values, and `CLAUDE.md` with the no-hardcoded-secrets rule
- [ ] `npm run ping` passes (auth and file access checks show PASS)
- [ ] Node 18 or later installed

**The Task:**

```
In the figma-tools project, do the following:

1. Create `scripts/figma-read.mjs` using the figma-read.mjs implementation from Chapter 3 of the textbook. The script should:
   - Read FIGMA_TOKEN and FIGMA_FILE_KEY from process.env (never hardcoded)
   - Fetch the full file from GET /v1/files/:key
   - Write a local fixture to .figma-fixtures/[fileKey]-[date].json immediately after fetch
   - Walk the document graph and extract a component inventory
   - Extract the variable inventory (handling the case where variables are absent gracefully)
   - Write three output files to figma-output/:
     - component-inventory.json
     - variable-inventory.json (only if variables are available)
     - missing-descriptions.md (only if any components lack descriptions)
   - Print a summary to stdout: page count, component count, description coverage, variable availability

2. Add a "read" script to package.json:
   "read": "node scripts/figma-read.mjs"

3. Update README.md CLI tools table with:
   | npm run read | Fetch file, write fixture, produce component + variable inventory | Chapter 3 |

4. Add to .gitignore (if not already present):
   figma-output/

Do NOT modify figma-ping.js. Do NOT modify .env. Do NOT hardcode any token or file key. Do NOT delete or overwrite existing fixtures if any exist — write new ones with today's date in the filename.

Stop after completing these steps. I will run npm run read myself and review the output.
```

**Expected output:** `scripts/figma-read.mjs`, updated `package.json` and `README.md`, and a `.gitignore` entry for `figma-output/`. When you run `npm run read`, you should see a fixture written to `.figma-fixtures/`, a console summary, and output files in `figma-output/`.

**What to inspect:**
- Confirm `.figma-fixtures/` is in `.gitignore` (fixture files may contain unpublished component names)
- Open `figma-output/component-inventory.json` and check that `generatedAt`, `fileKey`, `total`, and `components` are all present
- If `missing-descriptions.md` was generated, open it and look at the first five entries — do the component names look like real components from your design system, or do they look like archived / internal variants?
- Check that the fixture filename includes today's date

**If it goes wrong:**
- *"Cannot read property 'children' of undefined":* the `document` field is absent from the response. Run `npm run ping` first — if file access is failing, the full fetch will fail too.
- *"Variable inventory: not available":* expected on non-Enterprise plans. The script should print the Tokens Studio alternative message; if it crashes instead, check that the `extractVariables` function handles a missing `variables` field without throwing.
- *Rate limit error (exit code 2):* wait the number of seconds specified in the output, then retry. If it keeps rate-limiting, you may have another process using the same token concurrently.

**CLAUDE.md / AGENTS.md note:** Add this standing rule to `CLAUDE.md` after the task completes: "Scripts in this repo must read from a local fixture when `FIXTURE_PATH` is set in the environment, and from the live API only when `FIXTURE_PATH` is absent. This prevents unnecessary API calls during iterative development." This fixture-first rule applies to every subsequent chapter that reads from the Figma API.

---

### Exercise 5 — AI Validation Exercise

**What you're validating:** The `component-inventory.json` and `variable-inventory.json` output files from Exercise 4.

**Validation type:** Schema correctness and graph accuracy — checking whether the output files contain what they claim to contain, and whether the extraction logic correctly read the document graph rather than assuming fields that may not be present.

**Risk level:** High. `component-inventory.json` is the stable input for Chapter 5 (audit), Chapter 10 (docs sync), and Chapter 12 (spec generation). If it is wrong — missing components, wrong IDs, incorrect description flags — every downstream tool will be wrong too. Fix it now.

**Setup:** Run `npm run read` to generate the output files. You will paste fragments of the output into the validation prompt. You will also need your fixture file path.

**The Validation Task:**

```
I ran figma-read.mjs against a Figma file and produced these output files. Please validate them against the criteria below.

component-inventory.json summary:
[PASTE THE TOP-LEVEL JSON: generatedAt, fileKey, total, withDescription, missingDescription]

First three components in the inventory:
[PASTE THREE ENTRIES FROM the components array]

variable-inventory.json status: [AVAILABLE / NOT AVAILABLE - state which]

If available, first two variables:
[PASTE TWO ENTRIES FROM the variables array]

Please check each item and respond PASS, FAIL, or FLAG:

CORRECTNESS
[ ] The fileKey in the output matches the FIGMA_FILE_KEY value I would expect. If it shows "your_file_key_here" or a placeholder, flag it — the script ran against the wrong environment.
[ ] Component IDs in the inventory follow the Figma node ID format (e.g., "1:23", "45:678") — a number, a colon, a number. If IDs look like hashes or GUIDs, flag it.
[ ] hasDescription is true only for components where the description field is non-empty and non-whitespace. Verify this against the pasted entries.

COMPLETENESS
[ ] The total count is plausible for a real design system file. A count of 0 or 1 on a real file is suspicious — flag it with "may indicate archive-only pages or a failed walk."
[ ] generatedAt is present and is a valid ISO 8601 timestamp.
[ ] If the variable inventory is marked NOT AVAILABLE, the output should note the reason (non-Enterprise plan or empty collection). If it is simply absent with no message, flag it.

SCOPE
[ ] The component entries do not contain node tree data (fills, strokes, children) — the top-level components map contains metadata only. If full node data appears in the inventory entries, the script is using the wrong data source.

CHAPTER-SPECIFIC CRITERIA
[ ] The componentSetId field is present on each entry (may be null). If the field is absent entirely from the entries, flag it — the schema is incomplete.
[ ] For variables: if valuesByMode entries exist, the mode names are human-readable strings (e.g., "Light", "Dark"), not raw mode IDs (e.g., "12:0"). The script should resolve mode IDs to names. If raw IDs appear, flag it.

FAILURE MODE CHECK
[ ] "Fluent but wrong": Does the output look complete and well-formed but contain a component count that cannot be right for a real design system (e.g., exactly 10 components when the ping showed a complex file)? This may indicate the walk stopped early or filtered too aggressively.
[ ] "Outdated API field assumption": Does the component inventory include a field that the Figma components map does not actually provide — for example, a variantProperties field that only appears on COMPONENT nodes in the document tree, not in the top-level components map? If the pasted entries include fields not listed in the chapter's schema description, flag them for verification against the actual fixture.
[ ] "Schema-valid but wrong graph field": Does the variable inventory show mode names that look correct but are actually defaulting to the first mode for all variables rather than correctly mapping mode IDs to names? Check: if the file has Light and Dark modes, do both appear in each variable's valuesByMode output, or only one?
```

**What to do with your findings:** Any FAIL or FLAG on the correctness or chapter-specific criteria means the inventory is not reliable enough to pass to downstream tools. The most important flag is a wrong or incomplete component count — if the inventory is missing components that exist in the file, Chapter 5's audit will miss them too. Compare your inventory count against the number visible in Figma's component panel before treating the inventory as production-ready.

**AI Use Disclosure prompt:** Add these two sentences to any PR, team message, or document where you share the inventory output: "The component and variable inventory was generated by figma-read.mjs with AI-assisted implementation and validated using a structured checklist. The output has been spot-checked against the source Figma file for component count and ID accuracy."

**Series connection:** The specific failure mode this exercise guards against — AI assuming graph fields not actually present in the response, producing schema-valid but wrong output — is the Tier 4 validation failure for Chapter 3. The fixture written by `figma-read.mjs` is the ground truth: if the model's description of the response schema does not match what is actually in the fixture, the fixture wins.
