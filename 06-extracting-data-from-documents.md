# Chapter 6 — Extracting Data from Documents

## TL;DR

- Extraction turns documents into structured data; the structure can make errors look official.
- Define your schema before you ask Cowork to extract anything.
- Source traceability — every row tied to its source file — is not optional.
- Verify row counts, spot-check numeric fields, and log ambiguous cases rather than guessing.
- Clean-looking output is not the same as correct output.

---

## The Scene

You have forty-seven receipts from the department's summer field work. Some are PDFs from vendors with clean digital layouts. Several are photos taken on a phone in bad light. Two have handwritten notes scrawled over the total line. One is a crumpled scan that came through the mail.

You need a spreadsheet: vendor, date, total, tax, category, and source file for each one.

Cowork, given access to the folder, produces a clean table in about three minutes. Forty-seven rows. Column headers look right. Numbers appear in the right columns. The formatting is professional. Nothing on the surface announces a problem.

Then you check row 23. The total reads $342.00. You open the source receipt. The total is $34.20. The decimal point moved. Row 31 has a vendor listed as "Unknown Merchant" — reasonable for one smudged photo, but Cowork quietly copied that name to three other rows without flagging that it guessed. Row 6 is missing a date. Cowork used the date it pulled from the file's metadata, which was the day someone scanned it — not the day of the purchase.

Forty-seven rows. Three errors found in a few minutes of checking. You don't know yet how many you haven't found.

---

## What This Chapter Lets You Do

By the end of this chapter you will be able to:

- Define an extraction schema that tells Cowork what to collect and how to handle uncertainty.
- Write a task brief that produces source-traceable extracted data, not just a table.
- Read an extraction output critically: row counts, numeric fields, exception logs, and ambiguous values.
- Decide when spot-checking is enough and when you need to verify every row.

---

## Extraction vs. Summarization

These two operations look similar but create different risks.

**Summarization** produces a condensed account of what a document says. The reader understands they are getting an interpretation, not a transcription. Some detail is expected to disappear.

**Extraction** produces specific values — a number, a date, a name, a category — claimed to represent what the source document actually contains. The reader of an extracted spreadsheet often treats those values as facts. If the extraction is wrong, the table looks exactly the same as if it were right.

This is the core danger of extraction: **structure confers credibility**. A well-formatted table with column headers looks authoritative whether the values came from a clean digital invoice or from a model's confident guess about a blurry photo. The source documents were the evidence. The spreadsheet is a claim about what those documents said. The two are not the same thing.

---

## The Schema Comes First

The most common extraction mistake is asking Cowork to extract data before deciding what the data is.

A schema defines:

- **Fields**: what columns exist, by name
- **Types**: text, date, currency, number, category
- **Source location**: where in the document this field typically lives
- **Validation rules**: what a valid value looks like
- **Exception rules**: what to do when the field is missing, ambiguous, unreadable, or uncertain

Without a schema, Cowork will invent one. It may be reasonable. It will not match your reporting requirements, your accounting categories, or your workflow conventions. Worse, it will be internally inconsistent: one vendor named "CVS" in some rows and "CVS Pharmacy" in others, depending on which label appeared on each receipt.

**A minimal schema for receipt extraction:**

| Field | Type | Source location | Validation rule | Exception rule |
|---|---|---|---|---|
| Vendor | Text | Receipt header | Match source text | Mark "unclear" if ambiguous |
| Date | Date | Header or footer | Valid calendar date | Mark "ambiguous" — do not use metadata |
| Total | Currency | Total line | Match printed total | Mark "uncertain" — do not infer |
| Tax | Currency | Tax line | Match printed tax | Blank if absent, not zero |
| Category | Text | User-defined list | Must match allowed values | Mark "uncategorized" |
| Source file | Text | Filename | Required — every row | Required — no exceptions |

The exception rules matter as much as the validation rules. When Cowork cannot read a field clearly, you want a flag in the output — not a confident guess. "Mark uncertain" and "do not infer" are instructions, not suggestions. Put them in your task brief explicitly.

---

## A Worked Walkthrough

**The task:** Extract expense data from forty-seven receipt files in a folder called `field-receipts-summer-2026/`. Output a spreadsheet with one row per receipt.

**Step 1: Prepare your workspace.**

Copy the receipt folder into your Cowork working directory. Do not work on originals. Restrict Cowork's access to the working copy only (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

**Step 2: Write the task brief.**

```
Task: Extract expense data from receipts
Source: field-receipts-summer-2026/ (working copy — 47 files)
Output: extracted-expenses.csv (new file, do not overwrite source)

Schema:
- Vendor: text from receipt header; mark "unclear" if unreadable
- Date: date of purchase from receipt; do not use file metadata as substitute; mark "ambiguous" if uncertain
- Total: total amount from printed total line; currency in USD; do not infer from line items; mark "uncertain" if unreadable
- Tax: tax amount if shown; leave blank if absent; do not substitute zero
- Category: one of [Travel, Meals, Supplies, Equipment, Other]; mark "uncategorized" if unknown
- Source_file: filename of source receipt — required for every row

Exception rules:
- If a field cannot be read with confidence, enter the field name followed by "_UNCERTAIN" in the value, e.g. "Total_UNCERTAIN"
- Do not invent values for missing or illegible fields
- If an entire receipt cannot be processed, add a row with Source_file and all other fields marked "_UNREADABLE"

Before extracting: show me a plan listing how you will handle PDFs vs. images, and flag any files that look problematic.

Do not begin extraction until I approve the plan.
```

**Step 3: Review the plan.**

Cowork will describe its approach: which files it can read clearly, which are images that require visual interpretation, which look problematic. Pay attention to anything it flags. If it identifies five image files as low-confidence, that tells you those five rows need full manual verification even if everything else passes spot-check.

**Step 4: Approve and extract.**

Once you approve, Cowork extracts and produces the spreadsheet. The output is a claim, not a ground truth.

**Step 5: Verify.**

This is the work that cannot be skipped.

---

## The Verification Checklist

**Row count first.** If you had forty-seven receipts, you should have forty-seven rows — or fewer rows plus exception entries that explain the difference. A row count mismatch means a receipt was silently dropped.

**Source file column.** Every row must have a source file name. Any row missing this field means you cannot go back to the original. That row is unverifiable and should be flagged before the data is used.

**Exception log review.** Read every row that contains an "_UNCERTAIN" or "_UNREADABLE" flag. These are the rows Cowork itself identified as uncertain. They require manual lookup against the source receipt.

**Numeric spot-check.** Pick a sample of rows — at minimum 10% or five rows, whichever is larger — and open the source receipt for each one. Verify the total, the date, and the vendor against what the receipt actually shows. The decimal point error is common. Date format confusion (day/month vs. month/day) is common. Vendor name truncation is common.

**Totals reconciliation.** If you have an independent total — a credit card statement, a prior budget line, a sum you already know — compare the sum of the extracted totals against it. Discrepancies are not always extraction errors, but they are always worth investigating.

**Ambiguous fields.** Fields marked "unclear" or "uncategorized" need a decision: go back to the source, or leave them flagged. Do not quietly substitute a guess after the fact — that removes the flag and makes the row look verified when it isn't.

Research on LLM-based extraction confirms that errors are not only hallucinations. Field granularity, recall failures, and specification gaps matter ("Not Hallucination but Granularity," SSRN 2026). In other words, Cowork can read the document correctly but still extract the wrong field because the extraction rule was underspecified. That is a schema problem, not a model problem — and the fix is in your brief, not in asking again.

---

## How Much to Verify

The verification question is not "do I check or not." It is "how much checking fits the stakes."

**Full row-by-row verification** is appropriate when: the data feeds financial records, the source documents are high-risk (invoices, legal documents, regulated forms), the extraction involved many image files, or any verification sample reveals errors.

**Risk-tiered sampling** is appropriate when: source documents are clean digital PDFs, the task is low-stakes, and a full check sample (say 20%) shows no errors. The size of the sample should reflect the tolerance for error in the final use.

**Never skip verification entirely.** Research on human-in-the-loop extraction consistently finds that human validation remains important for high-precision work, even when the automated extraction looks clean (Megagon Labs, "Characterizing Practices, Limitations, and Opportunities Related to Text Information Extraction Workflows"). The clean-looking table is not evidence of correct extraction. It is evidence that the formatting succeeded.

The document quality problem is well-established. OCR and vision-model accuracy depends heavily on document type, language, noise, and image quality (Tahmasebi et al. 2021/2022). Even document AI tools specifically designed for invoices and receipts — using layout features alongside text — encounter problems with poor images, creases, missing fields, and layout variation (DocExtractNet, Information Processing & Management 2024). Cowork is working with the same underlying reality.

---

## The Human Gate

Before you use the extracted data for anything — sending it to accounting, loading it into a report, sharing it with a supervisor — confirm:

1. Row count matches source count (or exceptions explain the difference).
2. Every row has a source file reference.
3. You have opened and verified a meaningful sample against originals.
4. All uncertain fields are either resolved or clearly marked.
5. Numeric totals are consistent with any independent cross-check.
6. The output is a new file; the source documents are untouched.

Extraction is useful because it turns documents into structured data. It is risky for exactly the same reason. The structure makes errors look like facts. Your verification work is what separates a trusted dataset from a well-formatted claim.

---

## Common Mistakes

**Skipping the schema.** Without defined fields and exception rules, Cowork invents them. The resulting table may look consistent while hiding significant variation in how fields were interpreted.

**Treating clean formatting as accuracy.** A professionally formatted spreadsheet with column headers is not evidence of correct extraction. It is evidence that Cowork could produce a table. The values may still be wrong.

**Using file metadata as a date.** The date a file was scanned, saved, emailed, or downloaded is not the date of the underlying transaction. This is a systematic error if Cowork defaults to metadata for missing dates.

**Not requiring an exception log.** If Cowork does not flag uncertain fields, it will often fill them with a plausible value. That value is indistinguishable in the table from a value that was clearly readable. Exceptions must be required explicitly.

**Spot-checking only easy rows.** If you check only rows that look clean, you are confirming the extraction works on easy cases. Check a random sample, including image-based sources.

**Letting Cowork delete or modify source documents.** The source files are the only evidence for the extraction. They should remain untouched throughout (Anthropic, "Use Claude Cowork safely" [verify — current as of writing]).

---

## Try This

**Exercise 1 — Schema before brief.** Take any folder of documents you need to extract data from — receipts, invoices, forms, anything. Before opening Cowork, write your schema on paper: every field, its type, where it comes from in the document, what counts as a valid value, and what to do if it is missing. Time how long it takes. Then compare the schema to the output you get when you give Cowork that brief versus a brief with no schema. What changes?

**Exercise 2 — Verification gap test.** Run an extraction on a small set of source documents (five to ten). Before checking the output, predict how many errors you expect to find. Then do full row-by-row verification against the source files. Record the error count, error type, and which fields were affected. Compare your prediction to the result. Repeat this three times with different document types. What patterns emerge?

**Exercise 3 — Exception log required.** Repeat any extraction task but change the brief to require an explicit exception log: a separate file listing every field that could not be extracted with confidence, the reason, and the source file. How does the exception log change what you need to verify in the main table?

---

## What Would Change My Mind

The verification discipline taught in this chapter assumes that extraction errors are common enough to matter. If future extraction tools produce reliable, auditable source traceability — showing exactly which text span in the original document produced each extracted value — then full row-by-row verification could reasonably be replaced by targeted exception review. What would need to be true: every field in the output is linked to a specific, human-readable source location, and that link can be spot-checked in seconds. Until that is standard and reliable [verify — current as of writing], the verification checklist stands.

---

## Still Puzzling

The literature on LLM extraction distinguishes errors by type: hallucination (inventing a value), granularity failure (wrong level of specificity), recall failure (missing a field that existed), and specification gap (correctly following a bad schema). Most practical guidance collapses these into a single "check your output" warning. A better framework for nontechnical practitioners — one that helps them identify which type of error they are looking at and how to fix it — does not yet exist in an accessible form.

---

## Bridge to Chapter 7

You have extracted the data. Now someone needs to read it.

The next chapter covers the other direction of Cowork document work: not taking structured data out of documents, but assembling source materials into reports and presentations. The risks shift. You will not be checking decimal points. You will be checking whether polished prose accurately represents evidence — and whether a clean-looking executive summary is saying something true.

---

## AI Wayback Machine

**Katherine Johnson** spent years at NASA's Langley Research Center doing what the agency called "computing" — checking and independently verifying the orbital calculations that engineers and early computers produced. Her verification was not redundant; it was the mechanism by which the calculations became trustworthy enough to stake lives on. She did not distrust the systems she checked. She understood that trust is built through checking, not assumed from clean output.

**Run this:**

```
Who was Katherine Johnson, and what was her role at NASA before and after computers were introduced? How does the concept of independent verification connect to trusting automated outputs today? Keep it to three paragraphs.
```

→ Search **"Katherine Johnson mathematician"** on Wikipedia.

**Now make the prompt better.** Try one of these:

- Ask about the specific calculations she verified for John Glenn's orbital mission and why he requested her specifically.
- Ask how the transition from human computers to electronic computers changed verification workflows at NASA.

What changes? What gets better? What gets worse?

---

## Sources Used

- Anthropic, "Get started with Claude Cowork," Claude Help Center. https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork [verify — current as of writing]
- Anthropic, "Use Claude Cowork safely," Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely [verify — current as of writing]
- Tahmasebi et al., "OCR with Tesseract, Amazon Textract, and Google Document AI: a benchmarking experiment," Journal of Computational Social Science, 2021/2022. https://link.springer.com/article/10.1007/s42001-021-00149-1
- "Information extraction from scanned invoice images using text analysis and layout features," 2021. https://www.sciencedirect.com/science/article/pii/S0923596521003015
- "DocExtractNet: A novel framework for enhanced information extraction from business documents," Information Processing & Management, 2024. https://www.sciencedirect.com/science/article/pii/S0306457324004059
- Megagon Labs, "Characterizing Practices, Limitations, and Opportunities Related to Text Information Extraction Workflows: A Human-in-the-loop Perspective." https://megagon.ai/jp/publications/characterizing-practices-limitations-and-opportunities-related-to-text-information-extraction-workflows-a-human-in-the-loop/
- "Optimising Human-Machine Collaboration for Efficient High-Precision Information Extraction from Text Documents," arXiv, 2023. https://arxiv.org/abs/2302.09324
- "Human-In-The-Loop Document Layout Analysis," arXiv, 2021. https://arxiv.org/abs/2108.02095
- NIST, "Human-in-the-loop Technical Document Annotation," Technical Note 2287, 2024. https://nvlpubs.nist.gov/nistpubs/TechnicalNotes/NIST.TN.2287.pdf
- "Not Hallucination but Granularity: Error Taxonomy and Quality Audit of LLM-Based Legal Information Extraction," SSRN, 2026. https://papers.ssrn.com/sol3/Delivery.cfm/6496861.pdf?abstractid=6496861&mirid=1&type=2
