# Chapter 12 — Figma as a Machine-Readable Specification

*When the output is not for a human but for a CLI to build from — more detail is better, not less.*

---

The code generator ran. It had been handed a JSON file that looked like a design spec: component names, some properties, a few color values. It produced a React component library. The design engineer did a review pass and found that every component was missing its disabled state, none of the spacing values matched the layout constraints in the Figma file, the variant prop names differed from the variant dimension names in Figma, and every token alias had been resolved to a raw hex value instead of a CSS custom property reference.

The code generator did not fail. It consumed the specification it was given and produced code accordingly. The specification was the problem. It had been written for humans, not machines.

A human reading a design spec applies decades of tacit knowledge to fill in the gaps. They know that "disabled" is a state even when it appears inconsistently across components. They know that `16px` probably means `spacing.md` in the token system, even if the spec does not say so. They know the border-radius on the Card probably matches the border-radius token, even though the spec shows only the rendered pixel value.

A machine knows none of this. It needs the complete alias chain — not the resolved value, but the token reference that produced it. It needs the variant property names exactly as they appear in Figma, because those names are the contract between the design component and the code component. It needs the layout constraints, the spacing values before rounding, the node IDs that uniquely identify each component across file versions.

`build-spec.mjs` emits a machine-readable component specification JSON that contains everything a code generator or AI coding agent needs, with nothing compressed out for the sake of human readability. The chapters that follow — MCP integration, CI orchestration — consume its output. A downstream CLI cannot make reliable decisions about what to generate unless the specification it reads is complete.

![Side-by-side comparison table showing information present and absent in a human-readable component doc versus a machine-readable spec for the Button component. Fields absent in the human doc — token alias chain, node ID, layout constraints, spacing values, Code Connect import path — are annotated with the code generation failure mode each absence causes.](../images/12-figma-as-a-machine-readable-specification-fig-01.png)

*Figure 12.1 — Human-readable doc versus machine-readable spec for the same component*

---

## The Two Consumer Types

The distinction between human and machine consumers is structural, not cosmetic.

A human documentation consumer wants compression. The canonical usage, not every variant value. Usage guidance, not a complete enumeration of layout constraints. The example that covers 80% of use cases, not the edge cases. Compression makes documentation scannable. A human who needs more detail can open the Figma file directly.

A machine consumer — a code generator, an AI coding agent, a CLI that produces component scaffolding — needs completeness. Missing information forces the machine to guess. Guessing produces wrong code. There is no equivalent of "open the Figma file to check" for a CLI running in CI.

| Information field | Human doc | Machine spec | Consequence if absent from spec |
|---|---|---|---|
| Component name | Present | Present (name + key + nodeId) | — |
| Description | Present (authored) | Partial — may be empty | Generator cannot produce JSDoc; falls back to empty comment |
| Variant dimensions | Compressed — canonical example only | Complete exact map (variantProperties) | Generator guesses at prop types; produces union types from partial data |
| Token alias chain | Absent | Present (tokenAlias per fill) | Generator emits raw hex values instead of `var(--token-name)` references |
| Node ID | Absent | Present (nodeId + stable key) | Generator cannot uniquely identify component across file versions |
| Layout constraints | Absent | Present (constraints, sizingMode) | Generator defaults to `width: auto`; may produce broken layouts |
| Spacing values | Absent | Present (paddingTop/Right/Bottom/Left) | Generator invents padding; values will not match the design token scale |
| Typography details | Absent (style reference only) | Present (font family, size, weight, style reference) | Generator cannot produce correct text-style declarations |
| Code Connect / import path | Absent | Present if configured (codeConnect.importPath) | Generator invents import path; engineer receives broken import statements |

The machine spec is not a better version of the human doc. It is a different artifact with a different structure and purpose. Using the machine spec as documentation would produce something unreadable. Using human documentation as code generator input would produce code that guesses at what was compressed out.

---

## What the Figma API Provides

The Figma REST API exposes more than most practitioners use. The documentation sync in Chapter 10 needed names, descriptions, and variant properties. A machine-readable specification needs everything the API can provide.

From `GET /v1/files/:key` [verify — current as of writing], at the component level: `id` (the node ID, stable within a file version but not across structural refactors); `key` (the component library key, more stable than node ID across file changes); `componentSetId` (the parent set, if this component is a variant); `variantProperties` (the exact key-value map of variant dimensions and values for this component node); `description`; `absoluteBoundingBox` (pixel dimensions and canvas position); `constraints` (horizontal and vertical sizing behavior — `SCALE`, `FIXED`, `CENTER`, `STRETCH`, `INHERIT`) [verify]; `layoutMode` (`HORIZONTAL`, `VERTICAL`, `NONE`); `primaryAxisSizingMode` and `counterAxisSizingMode` (`FIXED` or `HUG`); the padding and gap values; `fills`, `strokes`, `effects`; `styles` (links to named color, text, and effect styles).

From `GET /v1/files/:key/variables/local` (Enterprise only) [verify]: variable collections, individual variables with type and per-mode values, and alias chains — a variable whose value is itself a reference to another variable, enabling the primitive → semantic → component token layering from Chapter 4.

When Code Connect is configured and published, the component-to-code mapping is accessible via the Code Connect CLI output. The exact REST surface for Code Connect data is still evolving [verify]. The stable approach is to maintain a `code-connect.json` generated by the Code Connect CLI and merge it into the spec at build time.

---

## The Component Specification Schema

A schema is a contract. Before writing `build-spec.mjs`, define what downstream tools will consume.

```json
{
  "$schema": "http://json-schema.org/draft-07/schema",
  "title": "ComponentSpec",
  "type": "object",
  "required": ["nodeId", "key", "name", "specVersion", "generatedAt"],
  "properties": {
    "specVersion":    { "type": "string" },
    "generatedAt":    { "type": "string", "format": "date-time" },
    "nodeId":         { "type": "string" },
    "key":            { "type": "string" },
    "name":           { "type": "string" },
    "description":    { "type": "string" },
    "componentSetId": { "type": ["string", "null"] },
    "variantProperties": {
      "type": ["object", "null"],
      "description": "Exact key-value map of variant dimensions for this component node"
    },
    "dimensions": {
      "type": "object",
      "properties": {
        "width":        { "type": "number" },
        "height":       { "type": "number" },
        "widthSizing":  { "type": "string", "enum": ["FIXED", "HUG", "FILL"] },
        "heightSizing": { "type": "string", "enum": ["FIXED", "HUG", "FILL"] }
      }
    },
    "layout": {
      "type": "object",
      "properties": {
        "mode":                  { "type": "string" },
        "paddingTop":            { "type": "number" },
        "paddingBottom":         { "type": "number" },
        "paddingLeft":           { "type": "number" },
        "paddingRight":          { "type": "number" },
        "itemSpacing":           { "type": "number" },
        "counterAxisSpacing":    { "type": "number" },
        "primaryAxisAlignItems": { "type": "string" },
        "counterAxisAlignItems": { "type": "string" }
      }
    },
    "fills": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "type":       { "type": "string" },
          "color":      { "type": "object" },
          "opacity":    { "type": "number" },
          "styleId":    { "type": ["string", "null"] },
          "styleName":  { "type": ["string", "null"] },
          "tokenAlias": { "type": ["string", "null"],
                         "description": "Token path derived from style name — null when style name does not resolve" }
        }
      }
    },
    "typography": {
      "type": ["object", "null"],
      "properties": {
        "fontFamily":   { "type": "string" },
        "fontWeight":   { "type": "number" },
        "fontSize":     { "type": "number" },
        "lineHeight":   {},
        "letterSpacing":{},
        "styleId":      { "type": ["string", "null"] },
        "styleName":    { "type": ["string", "null"] },
        "tokenAlias":   { "type": ["string", "null"] }
      }
    },
    "codeConnect": {
      "type": ["object", "null"],
      "properties": {
        "importPath":     { "type": "string" },
        "componentName":  { "type": "string" },
        "propMappings":   { "type": "object",
                           "description": "Maps Figma variant dimension names to code prop names" }
      }
    },
    "internalStructure": {
      "type": "array",
      "description": "Child nodes to depth 3 for code generators that need interior structure"
    }
  }
}
```

The `tokenAlias` field on fills and typography is the critical one. When a fill is applied via a named style that corresponds to a design token, the code generator should emit `color: var(--color-brand-primary)` — not `color: #1A56DB`. Building that alias requires tracing: fill → style reference → style name → token name convention. The script below builds this chain where the data is available and marks it `null` where it is not.

---

## `build-spec.mjs`

```javascript
// build-spec.mjs
// [illustrative — adapt to your file structure, variable access, and Code Connect setup]

import { writeFileSync, readFileSync, mkdirSync, existsSync } from 'fs';
import { join } from 'path';

const TOKEN           = process.env.FIGMA_TOKEN;
const FILE_KEY        = process.env.FIGMA_FILE_KEY;
const OUT_DIR         = process.argv.find(a => a.startsWith('--out='))?.split('=')[1] || 'spec-output';
const CODE_CONNECT    = process.argv.find(a => a.startsWith('--code-connect='))?.split('=')[1] || null;
const SPEC_VERSION    = '1.0.0';
const BASE            = 'https://api.figma.com/v1';

if (!TOKEN || !FILE_KEY) {
  console.error('ERROR: FIGMA_TOKEN and FIGMA_FILE_KEY required.');
  process.exit(1);
}

async function figmaGet(path) {
  const res = await fetch(`${BASE}${path}`, { headers: { 'X-Figma-Token': TOKEN } });
  if (res.status === 429) {
    const wait = parseInt(res.headers.get('Retry-After') || '30', 10);
    console.warn(`Rate limited. Waiting ${wait}s...`);
    await new Promise(r => setTimeout(r, wait * 1000));
    return figmaGet(path);
  }
  if (!res.ok) throw new Error(`Figma API ${res.status}: ${await res.text()}`);
  return res.json();
}

// Variables API: Enterprise only — degrade gracefully on 403
// [verify — current plan requirements for /variables/local endpoint]
async function fetchVariables() {
  try {
    return await figmaGet(`/files/${FILE_KEY}/variables/local`);
  } catch (e) {
    if (e.message.includes('403')) {
      console.warn('Variables API: 403 — Enterprise plan required. Spec will lack token alias chains.');
      return null;
    }
    throw e;
  }
}

function buildStyleIndex(fileData) {
  const index = {};
  for (const [id, style] of Object.entries(fileData.styles || {})) {
    index[id] = { name: style.name, type: style.styleType, description: style.description || '' };
  }
  return index;
}

function loadCodeConnect(path) {
  if (!path || !existsSync(path)) return {};
  try {
    return JSON.parse(readFileSync(path, 'utf8'));
  } catch {
    console.warn(`Could not parse Code Connect file at ${path}`);
    return {};
  }
}

// Convert style name to token alias using the path-slash convention from Chapter 4.
// "color/brand/primary" → "color.brand.primary" → maps to var(--color-brand-primary).
// Returns null if name does not follow the convention.
function styleNameToTokenAlias(name) {
  if (!name || !name.includes('/')) return null;
  return name.replace(/\//g, '.');
}

function resolveFills(node, styleIndex) {
  if (!Array.isArray(node.fills)) return [];
  return node.fills
    .filter(f => f.visible !== false)
    .map(fill => {
      const styleId   = node.styles?.fill || node.styles?.fills || null;
      const styleName = styleId && styleIndex[styleId] ? styleIndex[styleId].name : null;
      return {
        type:       fill.type,
        color:      fill.color || null,
        opacity:    fill.opacity ?? 1,
        styleId,
        styleName,
        tokenAlias: styleName ? styleNameToTokenAlias(styleName) : null
      };
    });
}

function resolveTypography(node, styleIndex) {
  if (node.type !== 'TEXT' || !node.style) return null;
  const s = node.style;
  const styleId   = node.styles?.text || null;
  const styleName = styleId && styleIndex[styleId] ? styleIndex[styleId].name : null;
  return {
    fontFamily:          s.fontFamily,
    fontWeight:          s.fontWeight,
    fontSize:            s.fontSize,
    lineHeight:          s.lineHeightPx || s.lineHeightPercent || s.lineHeightUnit || null,
    letterSpacing:       s.letterSpacing,
    textAlignHorizontal: s.textAlignHorizontal,
    textDecoration:      s.textDecoration || null,
    styleId,
    styleName,
    tokenAlias:          styleName ? styleNameToTokenAlias(styleName) : null
  };
}

// Recursive interior node structure — limited to 3 levels to keep spec files manageable.
// Increase depth for generators that need complete interior structure; monitor output size.
function buildNodeSpec(node, styleIndex, depth = 0) {
  const spec = {
    nodeId:     node.id,
    type:       node.type,
    name:       node.name,
    fills:      resolveFills(node, styleIndex),
    typography: resolveTypography(node, styleIndex),
  };

  if (node.layoutMode && node.layoutMode !== 'NONE') {
    spec.layout = {
      mode:                  node.layoutMode,
      paddingTop:            node.paddingTop    || 0,
      paddingBottom:         node.paddingBottom || 0,
      paddingLeft:           node.paddingLeft   || 0,
      paddingRight:          node.paddingRight  || 0,
      itemSpacing:           node.itemSpacing   || 0,
      counterAxisSpacing:    node.counterAxisSpacing || 0,
      primaryAxisAlignItems: node.primaryAxisAlignItems  || null,
      counterAxisAlignItems: node.counterAxisAlignItems  || null,
      primaryAxisSizingMode: node.primaryAxisSizingMode  || null,
      counterAxisSizingMode: node.counterAxisSizingMode  || null
    };
  }

  if (node.absoluteBoundingBox) {
    spec.dimensions = {
      width:  node.absoluteBoundingBox.width,
      height: node.absoluteBoundingBox.height
    };
  }

  if (node.constraints) spec.constraints = node.constraints;

  if (node.cornerRadius !== undefined)      spec.cornerRadius  = node.cornerRadius;
  if (node.rectangleCornerRadii)            spec.cornerRadii   = node.rectangleCornerRadii;

  if (node.effects?.length)
    spec.effects = node.effects.filter(e => e.visible !== false);

  if (depth < 3 && node.children?.length) {
    spec.children = node.children.map(c => buildNodeSpec(c, styleIndex, depth + 1));
  }

  return spec;
}

function buildComponentSpec(nodeId, comp, styleIndex, codeConnectIndex) {
  return {
    specVersion:   SPEC_VERSION,
    generatedAt:   new Date().toISOString(),
    nodeId,
    key:           comp.key,
    name:          comp.name,
    description:   comp.description || '',
    componentSetId:comp.componentSetId || null,
    variantProperties: comp.variantProperties || null,
    fills:         resolveFills(comp, styleIndex),
    layout:        comp.layoutMode && comp.layoutMode !== 'NONE' ? {
      mode:         comp.layoutMode,
      paddingTop:   comp.paddingTop    || 0,
      paddingBottom:comp.paddingBottom || 0,
      paddingLeft:  comp.paddingLeft   || 0,
      paddingRight: comp.paddingRight  || 0,
      itemSpacing:  comp.itemSpacing   || 0
    } : null,
    dimensions:    comp.absoluteBoundingBox ? {
      width:  comp.absoluteBoundingBox.width,
      height: comp.absoluteBoundingBox.height
    } : null,
    constraints:   comp.constraints || null,
    codeConnect:   codeConnectIndex[comp.key] || null,
    internalStructure: (comp.children || []).map(c => buildNodeSpec(c, styleIndex))
  };
}

async function main() {
  mkdirSync(OUT_DIR, { recursive: true });

  console.log('Fetching file...');
  const fileData = await figmaGet(`/files/${FILE_KEY}`);

  console.log('Fetching variables (Enterprise only — degrades gracefully)...');
  const variablesData = await fetchVariables();

  const styleIndex       = buildStyleIndex(fileData);
  const codeConnectIndex = loadCodeConnect(CODE_CONNECT);

  const rawComponents    = fileData.components    || {};
  const rawComponentSets = fileData.componentSets || {};

  // Build component set index
  const setSpecs = {};
  for (const [setId, set] of Object.entries(rawComponentSets)) {
    setSpecs[setId] = {
      specVersion: SPEC_VERSION,
      generatedAt: new Date().toISOString(),
      nodeId:      setId,
      name:        set.name,
      description: set.description || '',
      variants:    []
    };
  }

  const componentSpecs = [];
  for (const [nodeId, comp] of Object.entries(rawComponents)) {
    const spec = buildComponentSpec(nodeId, comp, styleIndex, codeConnectIndex);
    componentSpecs.push(spec);
    if (comp.componentSetId && setSpecs[comp.componentSetId]) {
      setSpecs[comp.componentSetId].variants.push(spec);
    }
  }

  const manifest = {
    specVersion:        SPEC_VERSION,
    generatedAt:        new Date().toISOString(),
    fileKey:            FILE_KEY,
    fileName:           fileData.name,
    lastModified:       fileData.lastModified,
    totalComponents:    componentSpecs.length,
    totalComponentSets: Object.keys(setSpecs).length,
    hasVariableData:    variablesData !== null,
    hasCodeConnect:     Object.keys(codeConnectIndex).length > 0,
    componentKeys:      componentSpecs.map(c => c.key),
    componentSetNames:  Object.values(setSpecs).map(s => s.name)
  };

  writeFileSync(join(OUT_DIR, 'manifest.json'),       JSON.stringify(manifest,                    null, 2));
  writeFileSync(join(OUT_DIR, 'components.json'),     JSON.stringify(componentSpecs,              null, 2));
  writeFileSync(join(OUT_DIR, 'component-sets.json'), JSON.stringify(Object.values(setSpecs),     null, 2));

  // Per-component files for large systems and MCP context loading
  const compDir = join(OUT_DIR, 'components');
  mkdirSync(compDir, { recursive: true });
  for (const spec of componentSpecs) {
    const safeName = spec.name.replace(/[^a-zA-Z0-9-_]/g, '-').toLowerCase();
    writeFileSync(join(compDir, `${safeName}-${spec.nodeId}.json`), JSON.stringify(spec, null, 2));
  }

  console.log(`\nDone.`);
  console.log(`${componentSpecs.length} component specs → ${OUT_DIR}/`);
  console.log(`Variable data: ${variablesData ? 'yes' : 'no (Enterprise required or use Tokens Studio)'}`);
  console.log(`Code Connect:  ${Object.keys(codeConnectIndex).length} mappings loaded`);

  // Contract validation inline
  const failures = [];
  for (const spec of componentSpecs) {
    if (!spec.key)           failures.push(`${spec.name}: missing key`);
    if (!spec.nodeId)        failures.push(`${spec.name}: missing nodeId`);
    if (!spec.specVersion)   failures.push(`${spec.name}: missing specVersion`);
  }
  if (failures.length) {
    console.error('\nContract failures:');
    failures.forEach(f => console.error(' ', f));
    process.exit(1);
  }
}

main().catch(err => { console.error('Fatal:', err.message); process.exit(1); });
```

Running it:

```bash
node build-spec.mjs --out=spec-output
node build-spec.mjs --out=spec-output --code-connect=code-connect.json
```

```json
{
  "scripts": {
    "figma:spec":      "node build-spec.mjs --out=spec-output",
    "figma:spec:full": "node build-spec.mjs --out=spec-output --code-connect=code-connect.json"
  }
}
```

---

## The Token Alias Chain: The Non-Enterprise Path

The alias chain — tracing from a raw fill value back through a style reference to a design token — is where the machine-readable spec most clearly outperforms human documentation. A human reader can infer that the primary button's blue fill probably comes from the `brand-primary` token. A code generator cannot infer this. It must be told.

The full alias chain requires the Variables API, which is gated behind the Enterprise plan [verify]. Three alternatives exist for other plan tiers.

The first is Tokens Studio. The Tokens Studio plugin exports a JSON file containing variables and their alias relationships regardless of plan tier. `build-spec.mjs` can be extended to merge a Tokens Studio JSON into the spec, resolving style names to token aliases. The Tokens Studio format is parseable and stable enough to build on [verify].

The second is the style-name convention. If the team follows the naming convention from Chapter 4 — `color/brand/primary` for color styles, matching the token hierarchy — the script can infer the token alias from the style name. `styleNameToTokenAlias` does this: `color/brand/primary` becomes `color.brand.primary`, which maps to `--color-brand-primary` in CSS. This works only when style names match token names; it breaks silently when they diverge.

The third is Style Dictionary integration. Run Style Dictionary against a maintained token source and produce a style-ID-to-token-name lookup table. Merge that table into `build-spec.mjs` at build time. This is the most reliable non-Enterprise approach but requires maintaining Style Dictionary configuration separately.

The script degrades gracefully — it emits whatever alias information it can build and marks the rest `null`. A contract test should warn (not fail) when token aliases are absent, so the generator knows to fall back to resolved hex values rather than failing silently.

![Three-column diagram showing paths to token alias resolution: the Enterprise path uses the Variables API directly and returns complete alias chains; the Tokens Studio path exports a tokens.json that build-spec.mjs merges at build time; the style-name convention path infers aliases from slash-path style names like "color/brand/primary" → "color.brand.primary". All three paths converge on the tokenAlias field in the component spec fill object, which is null when no path is available.](../images/12-figma-as-a-machine-readable-specification-fig-02.png)

*Figure 12.2 — Three paths to token alias resolution*

---

## W3C DTCG as the Interchange Format

The component spec JSON above is specific to this toolchain. When specs need to cross tool boundaries — different teams, different code generators, different platforms — the W3C Design Tokens Community Group (DTCG) format is the emerging standard for token interchange. [verify — current DTCG specification status]

DTCG defines a JSON format for token data where `$value`, `$type`, and `$description` are the primitive fields:

```json
{
  "color": {
    "brand": {
      "primary": {
        "$value": "#1A56DB",
        "$type": "color",
        "$description": "Primary brand color"
      }
    }
  }
}
```

Aliases use `{dot.path}` references:

```json
{
  "button": {
    "background": {
      "$value": "{color.brand.primary}",
      "$type": "color"
    }
  }
}
```

`build-spec.mjs` can be extended to emit DTCG-compatible token data for each component — the component-level tokens that are specific to that component, not the global token set. Style Dictionary supports DTCG input natively [verify], which means a spec with DTCG token data can be processed by Style Dictionary to produce CSS custom properties, Swift tokens, Android XML, or any other platform target from the same source.

---

## Contract Tests

A machine-readable spec is only useful if it is complete. Contract tests are the mechanism that prevents it from silently regressing.

| Check name | Failure type | What the downstream consumer does when this check fails |
|---|---|---|
| Missing `nodeId` | Hard fail — exit 1 | Consumer cannot identify the component in the Figma file; all subsequent operations on this spec entry fail |
| Missing `key` | Hard fail — exit 1 | Consumer cannot construct a stable library reference; component identity across file versions is broken |
| Missing `specVersion` | Hard fail — exit 1 | Consumer cannot determine whether the spec format it reads matches the format the generator produced |
| Missing Code Connect mapping | Warn — exit 0 | Generator infers import path from component name; output may have wrong package name or import syntax |
| Fills with no style reference and no token alias | Warn — exit 0 | Generator emits raw hex values (`color: #1A56DB`) instead of CSS custom property references (`color: var(--color-brand-primary)`) |
| Style name contains `/` but `tokenAlias` is null | Hard fail — exit 1 | Alias derivation failed on data it should have handled; indicates a bug in `styleNameToTokenAlias` or unexpected style name format |

```javascript
// test-spec-contract.mjs
// Run after build-spec.mjs

import { readFileSync } from 'fs';

const components = JSON.parse(readFileSync('spec-output/components.json', 'utf8'));
let failures = 0;

for (const spec of components) {
  if (!spec.nodeId)      { console.error(`FAIL: ${spec.name} missing nodeId`);      failures++; }
  if (!spec.key)         { console.error(`FAIL: ${spec.name} missing key`);          failures++; }
  if (!spec.specVersion) { console.error(`FAIL: ${spec.name} missing specVersion`);  failures++; }

  if (!spec.codeConnect) {
    console.warn(`WARN: ${spec.name} has no Code Connect mapping — generator will infer import`);
  }

  // Fills present but no style reference and no token alias means generator uses raw hex
  if (spec.fills?.some(f => f.type === 'SOLID' && !f.styleId && !f.tokenAlias)) {
    console.warn(`WARN: ${spec.name} has fills without style reference — generator will use raw hex values`);
  }
}

if (failures > 0) {
  console.error(`\n${failures} contract failure(s). Spec is not safe for code generation.`);
  process.exit(1);
}

console.log(`Contract check passed. ${components.length} components verified.`);
```

```json
{
  "scripts": {
    "figma:spec:check": "node build-spec.mjs --out=spec-output && node test-spec-contract.mjs"
  }
}
```

The distinction between failures and warnings is deliberate. A missing `nodeId` or `key` is a hard failure — the downstream consumer cannot identify the component at all. A missing Code Connect mapping or an unresolved fill are warnings — the generator can fall back to reasonable defaults, but the output will be less precise than it could be. Both deserve to be visible; only the hard failures should halt the build.

---

## How This Feeds MCP and the AI Coding Agent

The component spec JSON from `build-spec.mjs` is the structured context that makes an AI coding agent's output trustworthy. Without it, an agent working in an MCP session has to infer component structure from what is visible in the Figma canvas — richer than nothing but structurally incomplete.

With the spec, the agent knows the exact variant dimensions and values for each component, so it can generate a TypeScript props type without guessing. It knows the token alias for each fill, so it emits `color: var(--color-brand-primary)` rather than `color: #1A56DB`. It knows the Code Connect import path, so it writes `import { Button } from '@acme/design-system'` rather than inventing an import. It knows the layout constraints, so it can decide whether a component should be `width: 100%` or `width: fit-content`.

The spec is also a fixture for regression testing. A code generator that has consumed a spec should produce identical output from an identical spec. Storing the spec in version control means a Figma change that alters the spec produces a diff — and that diff is the signal that either triggers a code generation run or flags a change that needs human review before code is generated.

For large design systems — 500 components or more — loading the entire `components.json` into an agent's context is impractical. The per-component files in `spec-output/components/` exist for this reason. An MCP workflow loads only the components relevant to the current generation task, treating the per-component spec as a targeted context window rather than a complete file read.

![Infographic showing four output artifacts from build-spec.mjs flowing to downstream consumers: manifest.json (totalComponents, lastModified, hasVariableData) flows to CI decisions; components.json (all components, full spec, fills, layout, codeConnect) flows to code generator batch runs; components/*.json (one file per component) flows to MCP agent context loading via targeted loading; component-sets.json (variant dimensions and values) flows to TypeScript prop type generation and Code Connect prop mapping.](../images/12-figma-as-a-machine-readable-specification-fig-03.png)

*Figure 12.3 — How build-spec.mjs output flows into downstream consumers*

---

## Failure Modes

**The spec is large.** A design system with 500 components and deep node trees produces a spec file several megabytes in size. This is not a problem for a CLI writing to disk. It is a problem for an AI coding agent loading the entire spec into context. Use the per-component files and load only what is relevant to the current task.

**Node IDs are not stable across file restructuring.** When a component is moved to a different page or rebuilt, its node ID changes. The component `key` field is more stable — it persists across moves within the same file. Build downstream tools to key on `key`, not `nodeId`, for component identity. Store both in the spec so the choice is available.

**Variable data requires Enterprise.** The graceful degradation means the spec is generated without alias chains on non-Enterprise plans. A code generator consuming a spec with no alias chains will use resolved hex values. This is not wrong — it is a known limitation documented in the manifest's `hasVariableData` field. The Tokens Studio and style-name-convention alternatives partially close this gap.

**Code Connect must be maintained separately.** `build-spec.mjs` reads Code Connect data from a file generated by the Code Connect CLI. When components are added or variant names change, the Code Connect file must be updated. Stale Code Connect data produces missing or incorrect import paths. Track Code Connect updates as part of the library publish process.

**Internal structure depth.** The script limits traversal to three levels of depth. Some generators need the complete interior — every nested frame, every text layer. Increasing the depth limit is correct for those generators; monitor output size, since deep traversals on complex components can produce spec files that are impractical to diff or load into an MCP session.

**The silent mismatch between style names and token names.** The `styleNameToTokenAlias` function assumes style names follow the token hierarchy. When they do not — when a style is named "Button Background" rather than "color/brand/primary" — the function returns `null` rather than a wrong alias. Nulls are visible in the warnings from the contract test. Wrong aliases would not be. The function fails safely by returning `null` on names that do not contain `/`, which is why the naming convention from Chapter 4 matters for this pipeline: it is what makes the alias derivation possible at all.

---

## Decision Rules

Generate the spec whenever the Figma library is published, a new component is added, or variant properties change.

Use `build-spec.mjs` output as the source for code generators, AI coding agent context, design-to-code pipeline inputs, and component scaffolding CLIs.

Do not use it as human-readable documentation. The spec is for machines. The documentation from Chapter 10 is for humans.

Store `components.json` and `manifest.json` in version control as generated files. Their diffs are meaningful — they show exactly what changed in the design system between two runs.

Run contract tests before any code generation run. A spec with missing required fields or stale Code Connect data produces bad code. The contract test is the gatekeeper.

On non-Enterprise plans, use Tokens Studio JSON merged into the spec, or rely on style-name conventions. Document the limitation in the manifest's `hasVariableData` field.

---

## What Comes Next

The spec is the structured context the next chapter needs. Chapter 13 connects the design system file to an AI coding agent via the Figma MCP server, using the spec as the foundation that makes the agent's output trustworthy. An agent that knows the variant dimensions, the token aliases, and the import paths can generate code that matches the design system. An agent that has only the canvas has to guess at all three.

---

## LLM Exercises

**Exercise 1 — Generate and examine.**
Paste the `resolveFills` function and the `styleNameToTokenAlias` function into an LLM conversation. Ask it to trace what happens when a component has a fill applied via a style named `"Button Background"` — a name that does not follow the slash-path convention. Ask: what does `tokenAlias` contain in the output? What does the code generator do with that? Then ask the LLM to propose a modified version of `styleNameToTokenAlias` that also handles a secondary convention — for example, camelCase names like `"colorBrandPrimary"` — without breaking the existing slash-path handling.

**Exercise 2 — Apply to known context.**
Run `build-spec.mjs` against a Figma file you have access to (or build a minimal fixture from the output of `figma-read.mjs` from Chapter 3). Open `component-sets.json`. Find a component set with at least three variants. Write a short Node.js script that reads that component set's entry and produces a TypeScript union type for each variant dimension — for example, `type Size = 'sm' | 'md' | 'lg'`. Run the script and verify the output matches what you would write by hand.

**Exercise 3 — Stress-test a specific claim.**
The chapter claims that the spec should be stored in version control and that its diffs are meaningful. Ask an LLM to argue the opposite case: what are the arguments against storing generated files in version control? What problems does it cause? How do the most mature CI-driven projects handle generated output? Evaluate the argument against your team's current practice — do the tradeoffs the LLM identifies apply to your situation?

**Exercise 4 — Draft a professional deliverable.**
You are proposing to add `build-spec.mjs` and `test-spec-contract.mjs` to your team's CI pipeline. Write a brief technical design document (one to two pages) for your engineering team explaining: what the spec generator does, what problem it solves, what it costs (CI time, maintenance of Code Connect), and what the failure modes are. Ask an LLM to draft the first version, then revise it to match the level of technical detail your team expects in design documents.

---

## Chapter 12 Exercises: Figma as a Machine-Readable Specification
**Project:** figma-tools — Your Design System Extraction Toolkit
**This chapter adds:** `build-spec.mjs`, which emits a schema-validated component specification JSON including resolved token alias chains, full variant property mappings, layout constraints, and Code Connect annotations, plus `test-spec-contract.mjs` which verifies the spec is complete before any code generation run.

### Exercise 1 — When to Use AI

The spec output from `build-spec.mjs` is the most information-dense artifact in the figma-tools pipeline. Here is where AI adds real leverage.

**Task 1: Generating TypeScript prop type definitions from component-sets.json.** Each entry in `component-sets.json` contains every variant dimension and its possible values. An LLM can read this structured data and produce a TypeScript interface per component set — variant dimension names become prop names, possible values become union types. This is templated generation from complete, structured input.

*Why AI works here:* The input is machine-generated and complete. The mapping from variant dimensions to TypeScript union types is a well-defined transformation with no ambiguous steps. The output can be verified mechanically against the source JSON.

**Task 2: Explaining what a null tokenAlias means in a generated spec.** When `tokenAlias` is `null` on a fill, it means the style name does not follow the slash-path convention from Chapter 4, or the fill was applied without a style reference. An LLM can read a specific component's spec entry, identify all `null` tokenAlias fields, and explain what a code generator will emit for each — raw hex values — and what the developer should do to fix the upstream naming.

*Why AI works here:* Diagnostic reasoning from structured data. The model traces the logic documented in the chapter (`styleNameToTokenAlias` returns null for names without `/`) and applies it to a concrete spec entry. The chain of reasoning is checkable.

**Task 3: Writing the `manifest.json` summary for a release changelog.** `manifest.json` contains `totalComponents`, `totalComponentSets`, `hasVariableData`, `hasCodeConnect`, and `lastModified`. An LLM can compare two manifest files — before and after a design sprint — and produce a human-readable changelog: which counts changed, whether Code Connect coverage improved, and what the `lastModified` delta was.

*Why AI works here:* Comparison and narration from structured numerical data. The facts are in the files. The model is translating them into prose.

**The tell:** If the task is generating code or prose from the spec's structured fields — variant dimensions, token aliases, layout constraints, node IDs — AI is appropriate and the output is verifiable against the source. When the task is deciding what the correct prop mapping should be for a component whose variant names do not obviously correspond to code prop names, that decision belongs to the engineer who owns the component contract.

### Exercise 2 — When NOT to Use AI

The following tasks look like spec work but require human judgment that the spec cannot supply.

**Task 1: Deciding what a variant dimension's correct code prop name should be.** The spec contains Figma variant dimension names — `Size`, `Variant`, `State`. The Code Connect mapping translates these to code prop names. When the mapping is not obvious — when `Variant` in Figma should become `intent` in the code component because the engineering team adopted different naming — the translation requires knowing the engineering decision. The spec records what the Figma file says. The Code Connect mapping records what the code team decided. Only a human who was part of that decision can make the mapping correctly.

*Why AI fails here:* Organizational authority. The model can propose a mapping, but the mapping is a contract between design and engineering. A model-proposed mapping that is confidently wrong and accepted without review will cause code generation to produce components with the wrong prop names.

**Task 2: Verifying that an alias chain is semantically correct.** A `tokenAlias` of `color.button.primary-background` is syntactically valid — it follows the slash-path convention. Whether `color.button.primary-background` is the correct token for the primary button's background — whether it resolves through the right intermediate tokens in the token hierarchy, whether it produces the right value in all modes — requires running the token pipeline from Chapter 8 and checking the resolved value against design intent. The spec records what the API said the style name was. It cannot tell you whether the style name correctly represents the token hierarchy.

*Why AI fails here:* Pipeline verification. The alias chain must be chased through the actual token pipeline to confirm correctness. A model cannot do this — it does not have access to the token pipeline's resolved output.

**Task 3: Deciding when to regenerate the spec and commit the diff.** The chapter recommends generating the spec on library publish. But whether a given spec diff — new components, changed variant dimensions, updated layout constraints — should trigger an immediate code generation run or wait for human review is a team decision. It depends on how mature the Code Connect mappings are, whether the changed components are in active use, and what the engineering team's capacity is for reviewing generated code. These are not computable from the spec itself.

*Why AI fails here:* Workflow authority. The model can describe the tradeoffs. It cannot make the call for your team.

**The tell:** Any task that requires knowing what a spec field should be (not what it is) requires a human. **Series connection:** Tier 4 (AI as generator from structured data) requires complete, correct input. The spec is the most complete structured artifact in this pipeline — but Tier 4 still only applies to generating from what the spec contains. When the spec is incomplete (null token aliases, missing Code Connect), Tier 4 cannot fill those gaps reliably.

### Exercise 3 — LLM Exercise

**What you're building:** A TypeScript prop type generator that reads `component-sets.json` and produces a typed interface for each component set's variant dimensions.

**Tool:** Claude (standard conversation). Why Claude: this task requires reading structured JSON and applying a consistent, verifiable transformation to produce TypeScript code. Claude's output can be checked line-by-line against the source JSON — each union type value should correspond exactly to a value in the `dimensions` array.

**The Prompt:**

```
I'm building a design system toolchain. The JSON below is the component-sets.json output from a Figma spec generator. Each entry represents a Figma component set with its variant dimensions and possible values.

For each component set, produce a TypeScript interface with this structure:

export interface [PascalCase component set name]Props {
  [camelCase dimension name]: [union type of all values as string literals];
  // add a JSDoc comment on each prop line: "Figma dimension: [original dimension name]. Values from Figma API."
}

Rules:
- Component set name → PascalCase (e.g., "button group" → "ButtonGroupProps")
- Dimension name → camelCase (e.g., "Has Icon" → "hasIcon")
- All variant values → string literal union, values exactly as they appear in the JSON (no normalization, no lowercase)
- Add "| undefined" to any dimension that has only one value — it may be optional in code
- Do not add any props that are not in the JSON
- Do not infer behavioral meaning from prop names — no JSDoc beyond the factual comment specified

After the interfaces, add a section called "// Mapping notes" that flags:
- Any dimension name that contains a space (these may cause prop name disagreements with the engineering team)
- Any dimension with more than 8 values (large unions that may indicate Figma organization problems)
- Any component set with only one dimension (potentially a standalone rather than a variant family)

Here is component-sets.json:

[PASTE component-sets.json content here]
```

**What this produces:** One TypeScript interface per component set, with union types derived from exact variant values. A mapping notes section that surfaces dimension names likely to need human review before the Code Connect configuration is written. This is a starting point for the Code Connect prop mapping — not the mapping itself.

**How to adapt this prompt:**
- *Own project:* Paste your actual `component-sets.json`. If the file contains more than 20 component sets, process it in batches of 5–10 to keep the output reviewable.
- *ChatGPT or Gemini:* Both handle this prompt well. The "Do not infer behavioral meaning" instruction is important — include it explicitly. Without it, models tend to add comments like "// Use for primary actions" that have no source in the JSON.
- *Claude Project:* Create a Project with your team's Code Connect prop naming conventions as a context document. The model will flag cases where Figma dimension names diverge from the engineering team's naming rules, surfacing decisions before they become bugs.

**Connection to previous chapters:** The `component-sets.json` this prompt consumes was produced by `build-spec.mjs`, which draws on the component-reading from Chapter 3, the naming conventions from Chapter 4 (slash-path style names that become token aliases), and the variant property extraction from Chapter 10's `sync-docs.mjs`. A component set with missing or unclear dimension names — a documentation gap from Chapter 10 — will produce an interface with ambiguous prop names here.

**Preview of next chapter:** Chapter 13 passes `build-spec.mjs` output directly to a Claude Code session via the Figma MCP server. The TypeScript interfaces produced in this exercise are exactly the kind of context the agent uses to generate component code without guessing at prop types. A spec that produces clean interfaces here is a spec the agent can build from in Chapter 13.

### Exercise 4 — CLI Exercise

**What you're building:** A contract test runner that verifies the `build-spec.mjs` output against the chapter's schema and flags the specific failure modes the chapter warns about: missing required fields, null token aliases, and missing Code Connect mappings.

**Tool:** Claude Code
**Skill level:** Intermediate to Advanced — requires reading the spec schema, extending the inline contract check in `build-spec.mjs`, and adding a standalone test file.

**Setup:**
- [ ] `build-spec.mjs` exists and produces `spec-output/components.json` without errors (`npm run figma:spec`)
- [ ] `spec-output/manifest.json` is present and `hasVariableData` is recorded (true or false)
- [ ] `docs-sync-output/missing-docs.json` from Chapter 10 is present (used to cross-check which components had documentation gaps)
- [ ] `brand-compliance-output/compliance-report.json` from Chapter 11 is present and shows zero errors (the spec should not be generated from a file with active compliance failures — add this as a preflight check)
- [ ] Node.js 18 or later is available

**The Task:**

```
Read build-spec.mjs, the spec-output/ directory, and the contract test stub in the chapter (test-spec-contract.mjs).

Create a standalone file called test-spec-contract.mjs in the project root with the following checks. Run the checks against spec-output/components.json. Do not modify build-spec.mjs.

Hard failures (exit 1 if any):
1. Any component spec missing nodeId, key, or specVersion.
2. Any component spec where variantProperties is present but contains a key that is an empty string.
3. Any component spec where fills contains a SOLID fill with a non-null styleId but a null tokenAlias AND the style name contains "/" (this indicates the alias derivation failed on a name that should have produced an alias — the chapter's styleNameToTokenAlias function only returns null for names without "/").

Warnings (log but do not exit 1):
4. Any component spec with no codeConnect entry — log the component name.
5. Any component spec where all fills have null tokenAlias — log the component name and fill count.
6. Any component spec where variantProperties is null (standalone component with no variant dimensions) — log the count.

After running checks, read brand-compliance-output/compliance-report.json. If bySeverity.error > 0, print: "WARNING: spec was generated from a file with [N] compliance errors. Code generation output may propagate brand violations." Do not exit 1 for this — it is a warning.

Add a "figma:spec:check" script to package.json: "node build-spec.mjs --out=spec-output && node test-spec-contract.mjs"

Do not delete or modify any existing files in spec-output/. Do not make any Figma API calls. Do not add any external npm dependencies.

Stop after writing test-spec-contract.mjs and updating package.json.

Verification step: run `npm run figma:spec:check`. The contract test should complete and print a summary line: "Contract check: [N] hard failures, [N] warnings, [N] components verified."
```

**Expected output:** `test-spec-contract.mjs` in the project root. `package.json` updated with `"figma:spec:check"`. Running the script produces a structured pass/fail output with counts, plus the compliance warning if the compliance report shows errors.

**What to inspect:** After the run, look at the warnings list. A high count of "no Code Connect entry" warnings means the spec is incomplete for code generation — the code generator will have to infer import paths. A high count of "null tokenAlias" warnings on components that use style-referenced fills means the style names do not follow the slash-path convention.

**If it goes wrong:** If hard failure 3 fires unexpectedly, open the specific component spec file in `spec-output/components/` and check the fill's `styleId` and `styleName`. If `styleName` contains `/` but `tokenAlias` is null, `styleNameToTokenAlias` may be receiving the name with leading or trailing whitespace — the chapter's implementation trims for `/` but not for whitespace. If the compliance warning fires and you expected the report to be clean, re-run `npm run brand:check` and confirm the errors are resolved before running the spec check.

**CLAUDE.md / AGENTS.md note:** Add to your project's `CLAUDE.md`: "Run `npm run figma:spec:check` before any code generation run. The contract test is the gatekeeper — a spec with hard failures produces incorrect code. Do not bypass the test to speed up a generation run."

### Exercise 5 — AI Validation Exercise

**What you're validating:** A component spec entry from `spec-output/components.json` — specifically, whether the `tokenAlias` fields are semantically correct or merely syntactically valid.
**Validation type:** Semantic correctness audit — the keystone failure mode of this chapter.
**Risk level:** High — a spec that passes schema validation and the contract test but contains wrong token aliases will cause a code generator to emit code that references non-existent CSS custom properties or the wrong token, silently, at code generation time.

**Setup:** Use the `spec-output/components.json` produced by Exercise 4, or generate a representative example: run `build-spec.mjs` against any Figma file that has at least two components with style-referenced fills. Pick one component spec entry to validate. Also open `tokens.json` or the Tokens Studio export from Chapter 8, if available — this is the ground truth for what token aliases should resolve to.

**The Validation Task:**

```
Validate the following component spec entry from spec-output/components.json against this checklist. For each item, mark Pass, Fail, or N/A and write one sentence.

CORRECTNESS
[ ] For each fill with a non-null tokenAlias: does the alias string exactly match the format produced by styleNameToTokenAlias? The function replaces "/" with "." in the style name. So a style named "color/brand/primary" should produce tokenAlias "color.brand.primary". Check character by character.
[ ] For each fill with a null tokenAlias: is the styleId also null (no style applied), OR does the styleName not contain "/" (name doesn't follow convention)? If styleId is non-null and styleName contains "/" but tokenAlias is null, this is a hard failure — the alias derivation failed on data it should have handled.
[ ] Does the variantProperties map contain the exact key-value pairs from the Figma API? Check against the component-inventory.json from Chapter 10 for the same component — the variant dimensions should match exactly.

COMPLETENESS
[ ] Are all fills listed in the spec, including fills with opacity < 1 and fills marked visible: false? (The chapter notes that resolveFills filters invisible fills — check whether this is intentional or a gap for your use case.)
[ ] Is layout present if the component uses Auto Layout? Cross-check: if the compliance report from Chapter 11 logged a spacing finding for this component, the layout object should show the off-scale values.

SCOPE
[ ] Does the spec contain any field that is not in the schema defined in the chapter? Extra fields are not failures, but they should be documented if a downstream code generator might read them.

CHAPTER-SPECIFIC: SCHEMA-VALID BUT SEMANTICALLY WRONG
[ ] This is the keystone check. Take the tokenAlias for one fill — say "color.brand.primary". Open tokens.json from Chapter 8 (or the Tokens Studio export). Does "color.brand.primary" exist as a token path? Does it resolve to the color value you see in fill.color in the spec? If the alias string is well-formed but does not exist in the token file, the spec is schema-valid and contract-test-passing but semantically wrong. A code generator will emit `color: var(--color-brand-primary)` for a CSS custom property that does not exist.
[ ] Check one alias chain: if the token at "color.brand.primary" is itself an alias (its $value is a {reference}), trace it to the resolved value. Does the resolved value match the hex value in fill.color? A mismatch here means the alias chain points to the right token name but the token's value has drifted — the spec was generated from a different version of the token file than the one currently in use.

CHAPTER-SPECIFIC: UNRESOLVED ALIAS OR WRONG VARIANT→PROP MAPPING
[ ] If codeConnect is present: check that each key in propMappings corresponds to an actual dimension name in variantProperties. A propMapping entry for "intent" when the Figma dimension is "Variant" is a wrong variant-to-prop mapping — it will cause the code generator to emit props the component does not accept.
[ ] If codeConnect is null: log that this component's code generator output will use inferred import paths, which may be incorrect. This is a warning, not a failure.

FAILURE-MODE CHECK
[ ] Fluent but wrong: identify the field in the spec that looks most authoritative but is most likely to be wrong. Is it a tokenAlias derived from a style name that does not match the token hierarchy? A codeConnect propMapping that was written before the engineering team renamed a prop?
[ ] Schema-valid but semantically wrong: confirm or deny that you found at least one tokenAlias that passes the schema and the contract test but does not exist in the token file. If you found one, write the alias string and the token path it should have been.

What to do with your findings: A Fail on the schema-valid-but-semantically-wrong check means the spec cannot be trusted for code generation until the token pipeline (Chapter 8) and the style names in Figma are reconciled. The fix is upstream: rename the style in Figma to match the token path, or rename the token to match the style name, and regenerate. Patching the spec JSON by hand is not the fix — it will be overwritten on the next `build-spec.mjs` run.

AI Use Disclosure prompt (mandatory — copy this into any PR that uses spec output for code generation): "Component specs in this PR were generated by build-spec.mjs and validated by test-spec-contract.mjs. Token alias correctness was manually verified against tokens.json by [your name] on [date]. Any tokenAlias values not present in the token file have been flagged and are excluded from code generation."

**Series connection:** The schema-valid-but-semantically-wrong failure mode is the keystone lesson of this chapter and of the Tier 4 risk pattern: a machine-generated artifact can satisfy every structural check and still be wrong in the way that matters most to the downstream consumer. The contract test is necessary but not sufficient. The human validation step — tracing one alias chain from spec to token file to resolved value — is what the test cannot do. That trace is Tier 7 work: it requires knowing what the token system is supposed to produce, which requires knowing what the design decided.
