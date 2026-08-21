# Chapter 11 — Brand Compliance Monitoring

*A programmatic report of every object in the file that deviates from brand guidelines — run on demand or on every commit.*

---

Three days before release, someone ran a last-minute visual QA pass over the marketing file. They found it: dark-gray text on a medium-gray background. The designer had used the right-looking color but picked it from the color picker instead of the style library. The contrast ratio was 2.9:1 against white and 1.8:1 against the gray. Both fail WCAG AA. Normal text requires 4.5:1. Large text requires 3:1. This was neither.

The component went live anyway because fixing it would delay the launch — and because nobody was sure how many other instances of the problem existed in the file. Running a search felt like opening a door nobody wanted to open three days before ship.

The answer to how many other instances exist is exactly what `monitor-brand.mjs` produces. The question you should be asking is not "how do we fix this?" but "why did we not know sooner?" A compliance tool run the week before launch instead of the day before launch catches the problem when it is one object. Run on every library publish, it catches it when it is one object. Compliance failures come in clusters — when one designer reaches for the color picker instead of the style library, it usually means the style library was harder to use than the color picker. The programmatic report turns that signal into a data file the team can act on, rather than a feeling that something might be wrong somewhere.

---

## What the API Exposes and Why It Is Enough

Before building anything, it is worth understanding what the detection mechanism actually is — because it is simpler than it sounds.

The Figma API distinguishes between two ways a fill can be applied to a node. [verify — current as of writing] A fill applied via a color style has a `styles.fill` property on the node, referencing the style's key. The raw color value is also present for rendering, but the style reference signals that the fill came from the library. A fill applied directly — from the color picker, from copy-paste, from an eyedropper — has no `styles.fill` property. Only the raw `fills` array with RGBA values.

This is the detection mechanism for hardcoded fills: any node with a solid fill and no `styles.fill` is a candidate for a compliance violation. Whether it is actually a violation depends on whether the raw RGBA value matches an approved palette entry within a tolerance threshold.

The same pattern applies to text. A text node styled via a text style has `styles.text` referencing the style key. A text node with inline typography has raw `style.fontSize`, `style.fontFamily`, and the rest — without a style reference, or with one that has been partially overridden.

Spacing is different because Figma does not have spacing styles as first-class objects. Auto Layout frames expose their padding and gap values as raw numbers — `paddingTop`, `paddingBottom`, `paddingLeft`, `paddingRight`, `itemSpacing`. [verify — current as of writing] There is no reference to check. Spacing compliance is therefore purely rule-based: is this value a member of the declared spacing scale?

These three detection mechanisms — style reference present or absent, approved color match within tolerance, value membership in a declared scale — are the entire foundation of `monitor-brand.mjs`. Everything else is walking the node tree and applying them.

---

## Defining the Rules

The rules belong in a configuration file committed to the repository, not hardcoded in the script. The configuration is the contract between the design system and the compliance tool. When the design system adds a new approved color, the rules file is updated. When the spacing scale changes, the rules file changes. The compliance tool adapts automatically.

```json
{
  "approvedColors": [
    { "name": "brand-primary",   "hex": "#1A56DB", "rgba": [26, 86, 219, 1] },
    { "name": "brand-secondary", "hex": "#6875F5", "rgba": [104, 117, 245, 1] },
    { "name": "neutral-900",     "hex": "#111928", "rgba": [17, 25, 40, 1] },
    { "name": "neutral-700",     "hex": "#374151", "rgba": [55, 65, 81, 1] },
    { "name": "neutral-500",     "hex": "#6B7280", "rgba": [107, 114, 128, 1] },
    { "name": "neutral-100",     "hex": "#F3F4F6", "rgba": [243, 244, 246, 1] },
    { "name": "white",           "hex": "#FFFFFF", "rgba": [255, 255, 255, 1] },
    { "name": "error-500",       "hex": "#F05252", "rgba": [240, 82, 82, 1] },
    { "name": "success-500",     "hex": "#0E9F6E", "rgba": [14, 159, 110, 1] }
  ],
  "approvedTypeSizes":    [11, 12, 14, 16, 18, 20, 24, 28, 32, 36, 48, 60, 72],
  "approvedFontWeights":  [400, 500, 600, 700],
  "approvedFontFamilies": ["Inter", "Inter Variable"],
  "spacingScale":         [0, 2, 4, 6, 8, 10, 12, 16, 20, 24, 28, 32, 40, 48, 56, 64, 80, 96],
  "minTouchTarget": 44,
  "colorTolerance": 2,
  "wcag": {
    "normalText": { "aa": 4.5, "aaa": 7.0 },
    "largeText":  { "aa": 3.0, "aaa": 4.5 },
    "largeTextThresholdPt":     18,
    "boldLargeTextThresholdPt": 14
  }
}
```

Two decisions in this configuration deserve explanation.

`colorTolerance` is set to 2 — a maximum per-channel delta of 2 out of 255. Exact RGBA matching is too strict. Screen rendering, rounding in the Figma color system, and opacity stacking mean that `rgba(26, 86, 219, 1)` and `rgba(27, 86, 219, 1)` are visually the same color but fail an exact match. A tolerance of 2 catches real palette deviations while avoiding false positives from floating-point rounding.

The WCAG thresholds are exact values from the WCAG 2.1 specification and are not configurable — they are not matters of team preference. [verify — WCAG 2.2 introduced no changes to contrast requirements; WCAG 3.0 proposes APCA, which remains a draft as of writing and is not normative for production compliance] What is configurable is whether a contrast failure is treated as an error or a warning by your CI gate. The recommendation: treat it as an error. A contrast failure is not a style preference. It affects users.

| Field | Type | What it governs | When to update |
|---|---|---|---|
| `approvedColors` | Array of `{ name, hex, rgba }` | The complete approved palette; any fill not matching within `colorTolerance` is flagged | When the design system adds or deprecates a brand color |
| `approvedTypeSizes` | Array of numbers (px) | Valid font sizes; text nodes outside this set are flagged | When the type scale is revised — add new sizes, do not remove unless deprecated |
| `approvedFontWeights` | Array of numbers | Valid font weight values (e.g., 400, 500, 600, 700) | When the brand adopts or drops a weight across the system |
| `approvedFontFamilies` | Array of strings | Permitted font family names; unapproved families produce errors | When a new typeface is formally added to or removed from the brand specification |
| `spacingScale` | Array of numbers (px) | Valid Auto Layout padding and gap values; off-scale values produce warnings | When the spacing system is updated — align with design token changes |
| `colorTolerance` | Number (0–255 per-channel max delta) | How closely a fill RGBA must match an approved color before flagging | Rarely — the default of 2 accounts for floating-point rounding in the Figma color system |
| `wcag.normalText.aa` / `wcag.largeText.aa` | Number (ratio) | WCAG 2.1 AA contrast thresholds: 4.5:1 normal text, 3:1 large text | Never — these are fixed WCAG 2.1 AA standards, not team preferences |

---

## The Contrast Calculation

The WCAG 2.1 contrast ratio formula is simple enough to implement correctly without reaching for a library. It is worth understanding the formula rather than treating it as a black box, because the formula determines which failures the tool catches and which it does not.

Relative luminance is computed from sRGB values by first linearizing each channel — removing the gamma encoding that display hardware applies — and then weighting the three channels by their contribution to human brightness perception:

```
if channel ≤ 0.04045:  linear = channel / 12.92
else:                   linear = ((channel + 0.055) / 1.055) ^ 2.4

L = 0.2126 × linearR + 0.7152 × linearG + 0.0722 × linearB
```

The weights — 0.2126, 0.7152, 0.0722 — reflect that the human visual system is most sensitive to green, less to red, and least to blue. A pure green at full brightness appears brighter than a pure blue at full brightness. The luminance formula encodes that perceptual reality.

The contrast ratio is then:

```
contrast = (L_lighter + 0.05) / (L_darker + 0.05)
```

The 0.05 addend prevents division by zero when both colors are pure black, and it slightly reduces the computed ratio for very dark color pairs — which matches perceptual reality for near-black combinations. The thresholds that apply to the ratio are: 4.5:1 for normal text (AA), 7:1 for normal text (AAA), 3:1 for large text (AA), 4.5:1 for large text (AAA). Large text is defined as 18pt or larger regular weight, or 14pt or larger bold.

```javascript
function srgbToLinear(c) {
  const s = c / 255;
  return s <= 0.04045 ? s / 12.92 : Math.pow((s + 0.055) / 1.055, 2.4);
}

function relativeLuminance(r, g, b) {
  return 0.2126 * srgbToLinear(r) + 0.7152 * srgbToLinear(g) + 0.0722 * srgbToLinear(b);
}

function contrastRatio(l1, l2) {
  const lighter = Math.max(l1, l2);
  const darker  = Math.min(l1, l2);
  return (lighter + 0.05) / (darker + 0.05);
}
```

The contrast check in `monitor-brand.mjs` propagates a `parentBg` color down the node tree, using the nearest solid fill ancestor as the background against which text nodes are checked. This is a necessary simplification. Real contrast checks account for opacity stacking, blending modes, layered fills, and image backgrounds. The tool as written will miss some failures — elements with partial opacity over complex backgrounds — and cannot check anything it cannot compute. Flag these limitations in the CI report: complex backgrounds require manual review.

---

## `monitor-brand.mjs`

```javascript
// monitor-brand.mjs
// Usage: node monitor-brand.mjs [--rules=brand-rules.json] [--out=dir] [--baseline=path]
// Requires: FIGMA_TOKEN, FIGMA_FILE_KEY in environment
// Illustrative — adapt brand-rules.json to your design system

import { readFileSync, writeFileSync, mkdirSync } from 'fs';
import { join } from 'path';
import 'dotenv/config';

const TOKEN    = process.env.FIGMA_TOKEN;
const FILE_KEY = process.env.FIGMA_FILE_KEY;
const RULES_PATH = process.argv.find(a => a.startsWith('--rules='))?.split('=')[1] || 'brand-rules.json';
const OUT_DIR    = process.argv.find(a => a.startsWith('--out='))?.split('=')[1]   || 'brand-compliance-output';
const BASELINE   = process.argv.find(a => a.startsWith('--baseline='))?.split('=')[1] || null;

if (!TOKEN || !FILE_KEY) {
  console.error('ERROR: FIGMA_TOKEN and FIGMA_FILE_KEY required.');
  process.exit(1);
}

const rules = JSON.parse(readFileSync(RULES_PATH, 'utf8'));
const BASE  = 'https://api.figma.com/v1'; // [verify — current base URL]

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

function srgbToLinear(c) {
  const s = c / 255;
  return s <= 0.04045 ? s / 12.92 : Math.pow((s + 0.055) / 1.055, 2.4);
}
function relativeLuminance(r, g, b) {
  return 0.2126 * srgbToLinear(r) + 0.7152 * srgbToLinear(g) + 0.0722 * srgbToLinear(b);
}
function contrastRatio(l1, l2) {
  const lighter = Math.max(l1, l2);
  const darker  = Math.min(l1, l2);
  return (lighter + 0.05) / (darker + 0.05);
}
function colorDistance(a, b) {
  return Math.max(Math.abs(a[0]-b[0]), Math.abs(a[1]-b[1]), Math.abs(a[2]-b[2]));
}
function isApprovedColor(rgba) {
  const [r, g, b] = [Math.round(rgba.r*255), Math.round(rgba.g*255), Math.round(rgba.b*255)];
  return rules.approvedColors.some(ac =>
    colorDistance([r,g,b], [ac.rgba[0], ac.rgba[1], ac.rgba[2]]) <= rules.colorTolerance
  );
}
function isOnSpacingScale(value) {
  return rules.spacingScale.includes(Math.round(value));
}
function isLargeText(fontSize, fontWeight) {
  return fontSize >= rules.wcag.largeTextThresholdPt ||
    (fontSize >= rules.wcag.boldLargeTextThresholdPt && fontWeight >= 700);
}

const findings = [];
let objectsChecked = 0;

function addFinding(severity, page, nodeName, nodeId, category, issue, detail) {
  findings.push({ severity, page, nodeName, nodeId, category, issue, detail });
}

function checkNode(node, pageName, parentBg) {
  objectsChecked++;
  const nodeName = node.name || 'Unnamed';
  const nodeId   = node.id;

  // Fills
  if (node.fills && Array.isArray(node.fills)) {
    for (const fill of node.fills) {
      if (fill.type !== 'SOLID' || fill.opacity === 0 || fill.visible === false) continue;
      const hasStyleRef = node.styles?.fill;
      if (!hasStyleRef) {
        if (!isApprovedColor(fill.color)) {
          addFinding('error', pageName, nodeName, nodeId, 'color',
            'hardcoded-unapproved-color',
            `Fill rgba(${Math.round(fill.color.r*255)},${Math.round(fill.color.g*255)},${Math.round(fill.color.b*255)}) not in approved palette and not applied via style`
          );
        } else {
          addFinding('warning', pageName, nodeName, nodeId, 'color',
            'hardcoded-approved-color',
            'Fill is an approved color but not applied via a color style — replace with a style reference'
          );
        }
      }
    }
  }

  // Strokes
  if (node.strokes && Array.isArray(node.strokes)) {
    for (const stroke of node.strokes) {
      if (stroke.type !== 'SOLID' || stroke.opacity === 0 || stroke.visible === false) continue;
      if (!node.styles?.stroke && !isApprovedColor(stroke.color)) {
        addFinding('warning', pageName, nodeName, nodeId, 'color',
          'hardcoded-stroke-color',
          'Stroke color not in approved palette'
        );
      }
    }
  }

  // Typography
  if (node.type === 'TEXT' && node.style) {
    const s = node.style;
    const hasStyleRef = node.styles?.text;

    if (!rules.approvedTypeSizes.includes(s.fontSize)) {
      addFinding(hasStyleRef ? 'info' : 'warning', pageName, nodeName, nodeId, 'typography',
        'off-scale-font-size',
        `Font size ${s.fontSize}px is not in the approved type scale`
      );
    }
    if (s.fontFamily && !rules.approvedFontFamilies.includes(s.fontFamily)) {
      addFinding('error', pageName, nodeName, nodeId, 'typography',
        'unapproved-font-family',
        `Font family "${s.fontFamily}" not in approved list`
      );
    }
    if (s.fontWeight && !rules.approvedFontWeights.includes(s.fontWeight)) {
      addFinding('info', pageName, nodeName, nodeId, 'typography',
        'unapproved-font-weight',
        `Font weight ${s.fontWeight} not in approved weights`
      );
    }

    // Contrast against parent background
    if (parentBg && node.fills?.length > 0) {
      const textFill = node.fills.find(f => f.type === 'SOLID' && f.visible !== false);
      if (textFill) {
        const textLum = relativeLuminance(
          Math.round(textFill.color.r*255),
          Math.round(textFill.color.g*255),
          Math.round(textFill.color.b*255)
        );
        const bgLum = relativeLuminance(
          Math.round(parentBg.r*255),
          Math.round(parentBg.g*255),
          Math.round(parentBg.b*255)
        );
        const ratio     = contrastRatio(textLum, bgLum);
        const large     = isLargeText(s.fontSize, s.fontWeight || 400);
        const threshold = large ? rules.wcag.largeText.aa : rules.wcag.normalText.aa;

        if (ratio < threshold) {
          addFinding('error', pageName, nodeName, nodeId, 'accessibility',
            'contrast-failure',
            `Contrast ratio ${ratio.toFixed(2)}:1 fails WCAG 2.1 AA (required ${threshold}:1 for ${large ? 'large' : 'normal'} text)`
          );
        }
      }
    }
  }

  // Spacing (Auto Layout frames)
  if (node.layoutMode === 'HORIZONTAL' || node.layoutMode === 'VERTICAL') {
    for (const prop of ['paddingTop','paddingBottom','paddingLeft','paddingRight','itemSpacing']) {
      const val = node[prop];
      if (val !== undefined && val !== 0 && !isOnSpacingScale(val)) {
        addFinding('warning', pageName, nodeName, nodeId, 'spacing',
          'off-scale-spacing',
          `${prop}=${val} is not in the approved spacing scale`
        );
      }
    }
  }

  // Touch targets
  if (node.type === 'COMPONENT' || node.type === 'COMPONENT_SET') {
    const w = node.absoluteBoundingBox?.width;
    const h = node.absoluteBoundingBox?.height;
    if (w && h && (w < rules.minTouchTarget || h < rules.minTouchTarget)) {
      addFinding('warning', pageName, nodeName, nodeId, 'accessibility',
        'small-touch-target',
        `Bounding box ${Math.round(w)}×${Math.round(h)}px may be below minimum touch target (${rules.minTouchTarget}px)`
      );
    }
  }

  // Recurse — pass nearest solid fill as background for children
  let nextBg = parentBg;
  if (node.fills?.length > 0) {
    const solid = node.fills.find(f => f.type === 'SOLID' && f.visible !== false && f.opacity !== 0);
    if (solid) nextBg = solid.color;
  }
  if (node.children) {
    for (const child of node.children) checkNode(child, pageName, nextBg);
  }
}

function computeDiff(baseline, current) {
  const key = f => `${f.nodeId}::${f.category}::${f.issue}`;
  const baselineSet = new Set(baseline.findings.map(key));
  const currentSet  = new Set(current.findings.map(key));
  return {
    baselineDate:        baseline.generatedAt,
    currentDate:         current.generatedAt,
    newFindings:         current.findings.filter(f => !baselineSet.has(key(f))).length,
    resolvedFindings:    baseline.findings.filter(f => !currentSet.has(key(f))).length,
    newFindingsList:     current.findings.filter(f => !baselineSet.has(key(f))),
    resolvedFindingsList:baseline.findings.filter(f => !currentSet.has(key(f))),
  };
}

function generateMarkdownReport(report) {
  const lines = [
    '# Brand Compliance Report',
    `\nGenerated: ${report.generatedAt}`,
    `\n## Summary\n`,
    '| Metric | Value |', '|--------|-------|',
    `| Objects checked | ${report.objectsChecked} |`,
    `| Total findings | ${report.totalFindings} |`,
    `| Errors | ${report.bySeverity.error} |`,
    `| Warnings | ${report.bySeverity.warning} |`,
    `| Info | ${report.bySeverity.info} |`,
    `\n## By Category\n`,
    ...Object.entries(report.byCategory).map(([cat, n]) => `- **${cat}**: ${n}`),
  ];
  const errors = report.findings.filter(f => f.severity === 'error');
  if (errors.length > 0) {
    lines.push(`\n## Errors (${errors.length})\n`);
    for (const f of errors) {
      lines.push(`### ${f.nodeName} — ${f.issue}`);
      lines.push(`- **Page:** ${f.page}`);
      lines.push(`- **Node ID:** \`${f.nodeId}\``);
      lines.push(`- **Detail:** ${f.detail}\n`);
    }
  }
  const warnings = report.findings.filter(f => f.severity === 'warning');
  if (warnings.length > 0) {
    lines.push(`\n## Warnings (${warnings.length})\n`);
    for (const f of warnings) {
      lines.push(`- **${f.nodeName}** [${f.page}] \`${f.nodeId}\`: ${f.detail}`);
    }
  }
  return lines.join('\n');
}

async function main() {
  mkdirSync(OUT_DIR, { recursive: true });

  console.log('Fetching file...');
  const fileData = await figmaGet(`/files/${FILE_KEY}`); // [verify — endpoint current]
  const pages    = fileData.document?.children || [];

  for (const page of pages) {
    console.log(`  Checking page: ${page.name}`);
    if (page.children) {
      for (const child of page.children) checkNode(child, page.name, null);
    }
  }

  const errors   = findings.filter(f => f.severity === 'error');
  const warnings = findings.filter(f => f.severity === 'warning');
  const infos    = findings.filter(f => f.severity === 'info');

  const byCategory = {};
  for (const f of findings) byCategory[f.category] = (byCategory[f.category] || 0) + 1;

  const report = {
    generatedAt: new Date().toISOString(),
    fileKey: FILE_KEY,
    objectsChecked,
    totalFindings: findings.length,
    bySeverity: { error: errors.length, warning: warnings.length, info: infos.length },
    byCategory,
    findings,
  };

  writeFileSync(join(OUT_DIR, 'compliance-report.json'), JSON.stringify(report, null, 2));
  writeFileSync(join(OUT_DIR, 'compliance-report.md'),   generateMarkdownReport(report));

  if (BASELINE) {
    try {
      const baselineData = JSON.parse(readFileSync(BASELINE, 'utf8'));
      const diff = computeDiff(baselineData, report);
      writeFileSync(join(OUT_DIR, 'compliance-diff.json'), JSON.stringify(diff, null, 2));
      console.log(`\nDiff vs baseline: ${diff.newFindings} new, ${diff.resolvedFindings} resolved.`);
    } catch (e) {
      console.warn(`Could not load baseline: ${e.message}`);
    }
  }

  console.log(`\nChecked ${objectsChecked} objects. ${findings.length} findings.`);
  console.log(`Errors: ${errors.length} | Warnings: ${warnings.length} | Info: ${infos.length}`);
  console.log(`Output: ${OUT_DIR}/`);

  if (errors.length > 0) {
    console.error(`\n${errors.length} error(s) found — CI failing.`);
    process.exit(1);
  }
}

main().catch(err => {
  console.error('Fatal:', err.message);
  process.exit(1);
});
```

Running it:

```bash
# First run — establish baseline
node monitor-brand.mjs --rules=brand-rules.json --out=compliance-run-1

# Subsequent runs — compare against baseline
node monitor-brand.mjs --rules=brand-rules.json --out=compliance-run-2 \
  --baseline=compliance-run-1/compliance-report.json
```

---

## The Diff Is the Point

A single compliance report shows the current state. A diff between two reports — before and after a design sprint, before and after a library update, before and after a batch fix — shows whether the file is getting better or worse. That directional signal is what makes the tool useful in CI rather than just useful on demand.

The `computeDiff` function identifies findings by a composite key: `nodeId + category + issue`. A finding is resolved when that key disappears between runs. It is new when the key appears without having been present before. This is coarse — a node that is renamed or moved changes its key — but it is stable enough for the purpose. The diff is a measure of progress, not a precise audit trail.

In a GitHub Actions workflow, the diff becomes the PR check:

```yaml
name: Brand Compliance Check
on:
  pull_request:
  schedule:
    - cron: '0 9 * * 1'  # Weekly Monday morning

jobs:
  compliance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - name: Run compliance check
        env:
          FIGMA_TOKEN:    ${{ secrets.FIGMA_TOKEN }}
          FIGMA_FILE_KEY: ${{ secrets.FIGMA_FILE_KEY }}
        run: npm run brand:diff
      - uses: actions/upload-artifact@v4
        with:
          name: compliance-report
          path: compliance-output/
```

![CI gate diagram: a GitHub Actions trigger feeds monitor-brand.mjs, which produces a diamond decision point on whether errors exceed zero. Exit code 1 (CI fails, PR blocked) fires on contrast failures and unapproved font families. Exit code 0 (CI passes, warnings logged) fires on hardcoded approved colors and off-scale spacing. A dashed arrow shows the compliance-output artifact attached to the PR for review.](../images/11-brand-compliance-monitoring-fig-01.png)

*Figure 11.1 — CI gate for brand compliance*

The exit code drives the gate. Contrast failures and unapproved font families produce errors and fail the build. Hardcoded approved colors and off-scale spacing produce warnings and let the build pass while informing the designer. Off-scale font weights and marginal touch targets produce info items. This severity mapping should reflect the team's actual risk model — not the tool author's defaults.

---

## What the Tool Cannot Catch

Understanding the tool's limits prevents false confidence.

Background color propagation is approximate. The `parentBg` color passed down the tree is the nearest ancestor's solid fill. This misses semi-transparent fills, gradient fills, image fills, and layered opacity. Treat contrast findings as conservative: the tool catches obvious failures, not all failures. A dedicated accessibility audit tool that renders the file will catch more.

The node tree walker depends on what the API returns. Very large files may not include all deeply nested nodes in the full file response. [verify — current as of writing] Confirm the tool is seeing the nodes you expect by checking a known-failing instance and verifying the finding appears.

Style references are not verified for correctness. The checker confirms that a fill was applied via a style (`styles.fill` is present), but it does not verify that the referenced style holds the right color. A designer can apply a style with a misleading name if the style library has naming problems. The audit from Chapter 5 should catch style library hygiene issues before the compliance check runs.

The tool cannot read intent. WCAG 1.4.3 exempts purely decorative elements from contrast requirements. The API does not know which elements are decorative. All text nodes are checked. Human review is required to confirm which findings are actionable and which are correctly exempt.

The approved color list must be maintained. Every new approved color requires updating `brand-rules.json`. If the list lags behind the design system, new approved colors generate false-positive warnings and engineers start ignoring the report. Assign ownership of the rules file to the design systems team and treat changes to it as requiring review.

| What it cannot catch | Why | What to do instead |
|---|---|---|
| Semi-transparent, gradient, or image backgrounds | `parentBg` propagation only tracks the nearest solid fill ancestor; opacity stacking and blending modes are not computed | Use a dedicated accessibility tool that renders the file; flag complex backgrounds for manual contrast review |
| Decorative elements exempt from WCAG 1.4.3 | The API does not mark elements as decorative; all text nodes are checked regardless of design intent | Human reviewer marks confirmed decorative findings as acknowledged exceptions in the report |
| Styles applied correctly but misnamed | The checker confirms `styles.fill` is present, not that the style name is accurate; a misleadingly named style passes the check | Run the style library audit from Chapter 5 before the compliance check; treat naming hygiene as a separate gate |
| Deeply nested nodes in very large files | The full file response may truncate deeply nested nodes; the walker only sees what the API returns | Verify detection by checking a known-failing instance and confirming the finding appears; segment large files by page |
| Style library naming errors | A fill applied via a style with a wrong name (e.g., "Dark Blue" applied where "Primary" was intended) passes the style-reference check | Pair compliance monitoring with a style audit that cross-references style names against the approved token hierarchy |

---

## The Lint Report

Long before design files existed in a form that programs could read, code compliance was monitored by linters — static analysis tools that walked source code and reported deviations from a defined style or correctness standard.

The canonical early linter was `lint`, written by Stephen Johnson at Bell Labs in 1978 for C code. Its job was to detect constructs that, while syntactically valid, were likely mistakes: unused variables, type mismatches, pointer errors. It did not fix anything. It reported. The human decided what to do with the report.

The pattern — walk a formal artifact, check against declared rules, emit a structured report — is the pattern `monitor-brand.mjs` implements for Figma files. The inputs are different (a JSON node tree instead of C source), the rules are different (brand guidelines instead of type safety), but the mechanism is identical.

The critical insight from the lint tradition is that a linter's value is proportional to the actionability of its output. A linter that produces five hundred undifferentiated warnings trains engineers to ignore it. ESLint succeeded in part because it distinguished fixable from non-fixable violations and let teams configure severity to match their actual risk model. `monitor-brand.mjs` is built on the same principle: errors block, warnings inform, info items educate. The severity mapping belongs to the team. The tool provides the detection mechanism; the team decides what to do with the signal.

The design compliance space is in the early phase of this evolution — the phase where teams are still deciding which violations are worth failing CI over. The answer for most teams: start with contrast failures and unapproved font families (genuine correctness failures that affect users or indicate the style library is not being used), and treat everything else as configurable until there is evidence of what actually matters.

---

## What Comes Next

Chapter 12 handles the consumer that is not a human at all — structuring the Figma file's data as a machine-readable specification that a CLI or code generator can build from. The compliance report is input to that process: a file with known-passing compliance is a file the code generator can trust.

---

## LLM Exercises

**Exercise 1 — Generate and examine**

Paste the `contrastRatio` and `relativeLuminance` functions into a conversation with an LLM. Ask it to trace through the calculation for two specific colors — say, `#111928` (neutral-900) text on `#FFFFFF` white background — step by step. Then ask it to compute the ratio for the failure case in the opening scenario: `#374151` (neutral-700) on `#6B7280` (neutral-500). Verify the computed ratio against the 4.5:1 AA threshold for normal text. Does it fail? By how much?

**Exercise 2 — Apply to known context**

Describe your team's design system to an LLM: which colors are in your approved palette, what your type scale is, whether you use Auto Layout consistently. Ask it to predict which compliance category — color, typography, spacing, or accessibility — is most likely to produce the most findings on first run, and why. Run the tool. Compare the prediction to the actual output.

**Exercise 3 — Stress-test a specific claim**

The chapter argues that contrast failures should always be errors that fail CI, never warnings. Ask an LLM to argue the opposing position: that contrast failures should be warnings during an active remediation sprint, to avoid blocking work while the team fixes existing issues. Evaluate the argument. Under what conditions is the warning-only approach defensible? Under what conditions does it risk leaving accessibility failures in production indefinitely?

**Exercise 4 — Draft or audit a professional deliverable**

You have just run the compliance tool for the first time and found 43 errors: 12 contrast failures and 31 hardcoded unapproved colors. Write a one-page summary for the design director that covers: what the numbers mean, which findings require immediate action before the next release, and what the remediation plan looks like over the next two sprints. Ask an LLM to draft this document. Then audit the draft: does it accurately convey the severity difference between contrast failures (accessibility risk) and hardcoded colors (process violation)? Does it give the director enough information to prioritize the work?

---

## Chapter 11 Exercises: Brand Compliance Monitoring
**Project:** figma-tools — Your Design System Extraction Toolkit
**This chapter adds:** `monitor-brand.mjs`, which checks every node in the Figma file against an approved color palette, type scale, spacing grid, and WCAG 2.1 AA contrast thresholds, and emits a severity-classified, diffable compliance report.

### Exercise 1 — When to Use AI

`monitor-brand.mjs` produces a structured JSON report. Here is where AI adds genuine value to what the tool generates.

**Task 1: Interpreting a large compliance report.** A file with 2,000 nodes may produce 300 findings across four categories. An LLM can read `compliance-report.json` and produce a plain-language triage: which categories account for the most findings, which page has the highest error density, and which single change — fixing all uses of one hardcoded unapproved color — would close the most findings at once.

*Why AI works here:* Aggregation and synthesis from structured data. The model is not computing contrast ratios — the tool did that. It is reading counts, grouping findings by node name and page, and identifying leverage points. The output can be verified against the JSON directly.

**Task 2: Drafting a remediation plan template.** Given a summary of finding categories (12 contrast failures, 31 hardcoded colors, 8 off-scale spacing values), an LLM can produce a sprint-ready remediation plan template: which findings should be addressed before the next release, which can be tracked as a backlog, and what workflow change prevents each category from recurring.

*Why AI works here:* Template generation from categorical inputs. The chapter's severity model (errors gate CI; warnings inform) provides the rules the model applies. The model is not making a judgment call about your team's capacity — you are.

**Task 3: Writing the `compliance-diff.json` summary for a PR comment.** After a remediation sprint, the diff between two compliance runs shows new findings and resolved findings. An LLM can convert the raw diff JSON into a concise PR comment: "12 contrast failures resolved. 3 new hardcoded-color warnings introduced. Net: −9 findings."

*Why AI works here:* Structured-to-narrative conversion. The diff JSON is machine-readable; the PR comment needs to be human-scannable in thirty seconds.

**The tell:** If the task is reading `compliance-report.json` or `compliance-diff.json` and producing a summary, triage, or plan, AI is appropriate — the inputs are structured and the model's output can be checked against the source. When the task is deciding whether a specific finding represents a real violation or an intentional brand exception, that judgment belongs to the designer who made the choice.

### Exercise 2 — When NOT to Use AI

The following tasks involve the compliance report but require human judgment AI cannot replicate.

**Task 1: Deciding whether a contrast failure is a decorative exception.** WCAG 1.4.3 exempts purely decorative elements from contrast requirements. The API does not know which elements are decorative. An LLM asked to classify findings as "real violation" or "exempt decorative element" will produce plausible-sounding classifications that may be wrong in both directions: flagging real violations as decorative, or marking genuinely decorative elements as violations. The designer who placed the element knows its intent. The model does not.

*Why AI fails here:* Intent inaccessibility. The decorative/non-decorative distinction is a semantic judgment about design purpose, not a computable property of the node. A model that classifies confidently is fabricating.

**Task 2: Updating `brand-rules.json` when the design system adds a new approved color.** The rules file is the contract between the design system and the compliance tool. Adding a new color requires knowing: is this color actually approved by the brand team, or is it a one-off that a designer added unilaterally? That question requires organizational knowledge — who approved what, in which review — that is not in the Figma file or the compliance report.

*Why AI fails here:* Authority confusion. An LLM can write the JSON entry for a new color. It cannot verify that the color is approved. Automating rules file updates creates a path for unapproved colors to enter the approved palette without human sign-off.

**Task 3: Calibrating severity mapping for your team's risk model.** The chapter recommends treating contrast failures as errors and hardcoded-approved-colors as warnings — but notes that this mapping belongs to the team. Deciding which finding categories gate CI for your team requires knowing your release cadence, your accessibility commitments, your legal obligations, and your capacity to remediate findings before ship. These are not computable.

*Why AI fails here:* Organizational context. A model asked to propose a severity configuration will produce a reasonable-sounding answer that may not fit your team's actual situation. Treat any AI-generated severity configuration as a starting draft, not a recommendation.

**The tell:** Any task that requires answering "is this finding a real violation in our context?" requires a human. **Series connection:** Tier 4 (AI as pattern-recognition tool on structured data) operates on what the tool can compute. Tier 7 wisdom here is specific: brand and accessibility values are organizational commitments that require human authority to define and human judgment to apply. The model can tell you the contrast ratio. It cannot tell you what your organization owes to the users that ratio affects.

### Exercise 3 — LLM Exercise

**What you're building:** A compliance report interpreter that converts `compliance-report.json` into an actionable triage memo for the design team.

**Tool:** Claude (standard conversation). Why Claude: reading a structured JSON report and producing an accurate, non-embellished plain-language summary is a task where Claude's tendency to stay close to source data is an advantage. This is not a creative task — it is a precision translation task where fabrication is the primary risk.

**The Prompt:**

```
I am a design systems engineer. The compliance report below was generated by a brand compliance monitoring tool that checks a Figma file against an approved color palette, type scale, spacing grid, and WCAG 2.1 AA contrast thresholds.

The tool uses these exact WCAG 2.1 AA thresholds (do not adjust these values):
- Normal text: 4.5:1 minimum contrast ratio
- Large text (18pt+ regular, or 14pt+ bold): 3:1 minimum contrast ratio

Please produce a triage memo with this structure:

## Compliance Triage — [date from generatedAt field]

### Error Summary
- Total errors: [N]
- Contrast failures: [N] — these are accessibility violations. WCAG 2.1 AA requires 4.5:1 for normal text and 3:1 for large text. List each unique failing node name and its reported contrast ratio.
- Unapproved font families: [N] — list each unique font family name found.

### Warning Summary
- Hardcoded approved colors: [N] — colors that are on-palette but not applied via a style reference. List the top 3 most frequently occurring node names.
- Off-scale spacing: [N] — list the off-scale values found, sorted from most common to least.

### Highest-Leverage Fix
Identify the single change — fixing all instances of one specific issue on one specific page — that would close the most findings. State the page name, the issue type, and the finding count.

### What Requires Human Review
List any finding categories where the tool's detection is known to be approximate (contrast failures on complex backgrounds, decorative elements). Do not recommend exemptions — flag items that need designer review.

Here is the compliance-report.json:

[PASTE compliance-report.json content here]

Important constraints: Use only the contrast ratios as computed in the report. Do not recalculate them. Do not adjust the WCAG thresholds. Do not recommend exempting any finding — that decision belongs to the design team.
```

**What this produces:** A structured triage memo with exact finding counts, the highest-leverage fix identified from the report data, and a clear list of items needing human review. The contrast ratios come from the tool's computation — the model is reading and reporting them, not computing them.

**How to adapt this prompt:**
- *Own project:* Paste your actual `compliance-report.json`. If the file is large (500+ findings), paste only the summary object and the errors array — the model does not need every warning to produce the triage.
- *ChatGPT or Gemini:* Both handle this prompt well. The critical instruction is "Use only the contrast ratios as computed in the report. Do not recalculate them." Include it explicitly — see Exercise 5 for why this matters.
- *Claude Project:* If you run compliance checks regularly, create a Project with `brand-rules.json` as a context file. The model will reference the correct WCAG thresholds and approved palette when explaining findings, without you needing to paste them each time.

**Connection to previous chapters:** The compliance report builds on the audit concepts from Chapter 5 (`figma-audit.js` classified violations by severity; `monitor-brand.mjs` applies the same pattern to brand and accessibility rules). The node tree walker is the same traversal used in `figma-read.mjs` from Chapter 3. If `sync-docs.mjs` from Chapter 10 flagged a component set with thin descriptions, that component set is also a candidate for compliance findings — components without documentation are often the ones whose colors were applied by hand.

**Preview of next chapter:** Chapter 12 builds `build-spec.mjs`, which produces a machine-readable component specification. A file with known-passing brand compliance is a file the spec generator can trust. The compliance report becomes a prerequisite gate for the spec build: run `monitor-brand.mjs` first, then `build-spec.mjs` if the error count is zero.

### Exercise 4 — CLI Exercise

**What you're building:** A `brand:diff` npm script that runs `monitor-brand.mjs` twice — once against a saved baseline and once against the current file — and outputs a pass/fail decision based on whether the new run introduced new errors.

**Tool:** Claude Code
**Skill level:** Intermediate — requires reading two JSON reports, computing a diff, and setting an appropriate exit code.

**Setup:**
- [ ] `monitor-brand.mjs` exists in your `figma-tools` project and produces `compliance-report.json` without errors
- [ ] `brand-rules.json` is present in the project root
- [ ] You have at least one previous run's output saved as `compliance-baseline/compliance-report.json` (run `node monitor-brand.mjs --out=compliance-baseline` to create it, then run again with `--out=compliance-current` to create the current report)
- [ ] `docs-sync-output/component-inventory.json` from Chapter 10 is present (used as scope context — not required for the diff logic, but confirms the prior chapter's output is in place)
- [ ] `FIGMA_TOKEN` and `FIGMA_FILE_KEY` are set in your environment

**The Task:**

```
Read monitor-brand.mjs and the existing compliance output files.

Add a new npm script called "brand:diff" to package.json. When run, it should:

1. Run `node monitor-brand.mjs --rules=brand-rules.json --out=compliance-current --baseline=compliance-baseline/compliance-report.json`
2. Read compliance-current/compliance-diff.json after the run completes.
3. Print a one-line summary: "Brand diff: [N] new findings, [N] resolved. Net: [±N]."
4. Exit with code 1 if newFindings > 0 AND any new finding has severity "error". Exit with code 0 otherwise (new warnings are allowed; new errors are not).

Implement this as a small wrapper script called `brand-diff.mjs` that runs monitor-brand.mjs as a child process using Node's `execSync` or `spawn`, reads the diff output, and applies the exit logic above.

Do not modify monitor-brand.mjs itself. Do not add any Figma API calls. Do not delete or overwrite compliance-baseline/ — treat it as read-only input.

Stop after writing brand-diff.mjs and updating package.json. Do not run the script.

Verification step: run `npm run brand:diff`. If the current file has no new errors compared to the baseline, the command should exit 0. Introduce one known violation (add a hardcoded unapproved color in your Figma file, re-export, and re-run) to confirm exit code 1 fires correctly.
```

**Expected output:** `brand-diff.mjs` in the project root. `package.json` updated with a `"brand:diff"` script. Running `npm run brand:diff` produces a one-line diff summary and exits with the appropriate code.

**What to inspect:** Open `compliance-current/compliance-diff.json` after the run. Confirm `newFindings` and `resolvedFindings` match the one-line summary. Confirm the exit code matches whether new errors were introduced.

**If it goes wrong:** If `compliance-diff.json` is empty or missing, confirm that `--baseline=` points to the correct path and that the baseline file was produced by `monitor-brand.mjs` (not edited by hand). If the exit code is always 0, check that `brand-diff.mjs` is reading the `newFindingsList` array and filtering for severity "error" — not just reading the `newFindings` count.

**CLAUDE.md / AGENTS.md note:** Add to your project's `CLAUDE.md`: "brand:diff compares the current Figma file against compliance-baseline/compliance-report.json. Update the baseline only after a deliberate remediation sprint — not automatically. Overwriting the baseline without human review defeats the purpose of the diff."

### Exercise 5 — AI Validation Exercise

**What you're validating:** The triage memo produced in Exercise 3 — specifically, whether the model accurately reported WCAG contrast ratios from the compliance report or silently recomputed (and potentially mis-stated) them.
**Validation type:** Numerical accuracy audit and failure-mode demonstration.
**Risk level:** High — a contrast ratio reported incorrectly in a triage memo sent to a design director may result in accessibility violations being de-prioritized or exempted on false grounds.

**Setup:** Use the triage memo produced in Exercise 3. Also have `compliance-report.json` open alongside it. If you did not run Exercise 3, generate a representative memo: create a minimal `compliance-report.json` with two or three contrast failures (use the actual values your tool computed), paste it into Claude with the Exercise 3 prompt, and save the output.

**The Validation Task:**

```
Validate the triage memo against the source compliance-report.json using this checklist. For each item, mark Pass, Fail, or N/A and write one sentence.

CORRECTNESS
[ ] For each contrast failure listed in the memo: does the reported contrast ratio exactly match the ratio in the source compliance-report.json findings? Check to two decimal places (e.g., 2.93:1, not "approximately 3:1").
[ ] Does the memo correctly apply the WCAG 2.1 AA thresholds? Normal text threshold is 4.5:1. Large text threshold is 3:1. If the memo states different thresholds, flag it as a failure regardless of how confident the language sounds.
[ ] Are error counts correct? Count the errors in the JSON manually and compare to the memo's "Total errors" line.

COMPLETENESS
[ ] Are all unique failing node names listed? Check that no contrast-failure finding from the JSON was omitted from the memo.
[ ] Is the highest-leverage fix identified from actual data — a real page name and real finding count from the JSON — not a generic recommendation?

SCOPE
[ ] Does the memo recommend exempting any specific finding? If so, flag it — the prompt instructed the model not to recommend exemptions. Exemption recommendations require human authority.
[ ] Does the memo suggest the WCAG thresholds are adjustable or team-configurable? They are not. WCAG 2.1 AA thresholds are fixed standards, not preferences.

CHAPTER-SPECIFIC: AI MIS-COMPUTING WCAG CONTRAST
[ ] This is the key check. Find one contrast failure in the memo. Open a calculator and compute the contrast ratio yourself using the formula from the chapter (linearize each channel with srgbToLinear, compute luminance with the 0.2126/0.7152/0.0722 weights, apply (L_lighter + 0.05) / (L_darker + 0.05)). Does your manual calculation match the ratio in compliance-report.json? Does the memo's stated ratio match the JSON?
[ ] If the memo states a ratio that differs from compliance-report.json — even by 0.1 — this is the "fluent but wrong" failure mode. The model recomputed the ratio rather than reading the tool's output. A recomputed ratio may appear more precise while being less accurate (the tool used the exact sRGB values from the Figma API; the model may have rounded the hex conversion).

CHAPTER-SPECIFIC: BRAND EXCEPTION FLAGGED AS VIOLATION
[ ] Review the "What Requires Human Review" section. Does the model flag any finding as likely to be an intentional brand exception? If it suggests specific findings are "probably decorative" or "likely intentional," that is out-of-scope inference — the model has no access to design intent. Flag any such suggestion.

FAILURE-MODE CHECK
[ ] Fluent but wrong: Identify the finding in the memo that is most likely to be acted on incorrectly if taken at face value. Is the ratio accurately reported? Is the correct WCAG threshold applied to it?
[ ] AI mis-computing WCAG contrast: Did the model use the tool's reported ratios or recompute them? (Hint: look for ratios that differ from the JSON by small amounts, or ratios stated to more decimal places than the tool reported.)

What to do with your findings: A Fail on any CORRECTNESS item — especially a mis-stated contrast ratio — means the memo must not be shared with stakeholders until corrected against the source JSON. The compliance tool computed the ratios; the memo should report them verbatim. Any deviation, however small, represents the model substituting its calculation for the tool's. Correct the memo by hand from the source JSON before forwarding it.

AI Use Disclosure prompt (mandatory — copy this into any document or PR that uses this memo): "This triage memo was drafted by Claude based on compliance-report.json generated by monitor-brand.mjs. Contrast ratios and finding counts were validated against the source JSON by [your name] on [date]. WCAG thresholds used: 4.5:1 normal text AA, 3:1 large text AA."

**Series connection:** The failure mode this exercise demonstrates — AI recomputing a precise numerical value rather than reading the tool's output, and producing a result that sounds more authoritative than it is — is the canonical Tier 4 risk. The tool computed the contrast ratio using the exact sRGB values from the Figma API. The model computed it from a hex string, possibly rounded. Both numbers look like contrast ratios. Only one was computed from the actual source data. Tier 7 wisdom: when a tool produces a number, the validation document must cite the tool's number — not the model's recalculation of it.
