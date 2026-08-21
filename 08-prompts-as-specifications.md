# Chapter 8 — Writing Claude Prompts That Are Specifications

*"Write me a login function" is not a prompt. A prompt names the thing, the invariants, the output format, and what not to touch.*

---

Here are two things Seth typed to Claude, one minute apart, on a Tuesday evening. He is filling out the Common App. The "Academic Record" section wants his weighted GPA broken out by year, across AP, honors, and regular tracks, and the only file he has is the messy CSV export from his school's student information system — every row a course, every column a column his guidance counselor never explains.

The first:

> *Help me write a CSV parser for my grades file.*

The second:

> *Write `parse_grades(path: str) -> list[Grade]` in `parsers/grades.py`. Invariants: standard library only — no pandas; quoted fields containing commas must parse correctly; UTF-8 BOM must be tolerated; empty cells become `None`, not the empty string. Context: the `Grade` dataclass is defined in `models/grade.py`; SDD §1.4 specifies the file format; a sample input is at `data/grades_sample.csv`. Output format: one Python file; then run `python -m pytest tests/test_parser.py -q` and report the exit code. Do not modify `models/grade.py`. Do not add new dependencies. Do not touch any file outside `parsers/`.*

The first prompt produced eighty lines importing pandas, returning rows as dictionaries when Seth's SDD required `Grade` dataclasses, and silently stripping the UTF-8 byte-order mark — which meant the first column header came back as `"﻿Student ID"` and broke every downstream lookup.

The second produced forty lines using the `csv` module, opening the file with `encoding="utf-8-sig"` (the standard-library idiom for tolerating a BOM), returning `None` for empty cells, passing all tests, and leaving `models/grade.py` untouched.

The model did not change between those two prompts. Seth's project did not change. His SDD did not change. Only the precision of the instruction changed.

This chapter is about what changed.

| Element | Weak prompt | Specification |
|---|---|---|
| Artifact name | "Help me with auth." | "Implement `authenticate(username, password) → Optional[User]` in `auth/auth.py`." |
| Invariants | (none stated) | "The `User` class is unchanged. The `users` table schema is unchanged. No new dependency added." |
| Context | (none stated) | "Use the existing `bcrypt` pattern from `auth/hashing.py`. Reference the AGENTS.md never-rules." |
| Output format | "Make it work." | "A complete file plus a unit test in `tests/auth/test_auth.py` covering: success, missing-user, wrong-password." |
| Negative constraints | (none stated) | "Do not modify `auth/hashing.py`. Do not return raw User passwords. Do not add OAuth or session storage." |

---

## Two Different Artifacts

The easy explanation is that the second prompt was "more detailed." That is true but it misses the structural point. The second prompt is not a more detailed request. It is a different kind of thing. A request names a topic and trusts Claude to fill in the rest. A specification names the artifact, the constraints, the context, the success criterion, and the scope boundary — and leaves Claude nothing to fill in that wasn't authorized.

There is a formal tradition behind this. IEEE 830-1998, the canonical reference for software requirements specifications, lists eight properties a good specification must have: correct, unambiguous, complete, consistent, verifiable, modifiable, traceable, and ranked for importance. These properties were written for systems a hundred engineers would build over years. The student working in Claude Code needs a subset of them, sized for one prompt at a time. That subset is five elements, and the rest of this chapter is about what each one does and what failure it prevents.

The thesis of this book is that Claude Code is instrument, not shortcut. The line between those two things is drawn in the prompt. Every prompt is a decision about what Claude decides and what you decide. Specify five elements and you have decided everything that matters. Specify zero and Claude makes five guesses, and the guesses are drawn from training-data distributions that have nothing in particular to do with your project.

---

## The Five Elements

**The specific task** is the one thing you are asking Claude to produce. Not a topic. Not an area. The artifact: the function name, the file path, the return type. *"Write `parse_grades(path: str) -> list[Grade]` in `parsers/grades.py`."* That is one sentence with a definite answer. Either Claude produces that artifact or it doesn't, and either outcome is detectable in one look.

Topic phrasing — "help me with the parser," "work on the CSV logic," "add grade loading" — is an invitation for Claude to interpret, and interpretation is a sampling process from whatever the training data says is the most popular way to handle that topic. Popular is pandas. Popular is a class with a `parse` method. Popular is whatever the top Stack Overflow answers look like. Popular is, emphatically, not your project. White and colleagues, in their 2023 *Prompt Pattern Catalog*, identified this move — phrasing the request as a named function with explicit inputs and outputs rather than as a topic — as one of the highest-leverage single changes in their ablation studies. Freeform topic prompts produced suggestions and explanations even when the user wanted executable code. The artifact-naming move is not stylistic. It changes what the model produces.

**The invariants** are what must not change. They are the load-bearing decisions of the project — the ones already made, the ones whose violation would require a rebuild — stated in the prompt so Claude inherits them rather than re-deriving them. Without invariants, Claude makes fresh guesses every turn, weighted by training data. Training data says pandas. Training data says add a new field to the dataclass if the CSV has an extra column. Training data says return an empty list on malformed input. None of those guesses are wrong in general. All of them might be wrong in your project.

A strong invariant is specific and defensive. It names the decision and, ideally, the reason. *"Standard library only — no pandas. Quoted fields containing commas must parse correctly. UTF-8 BOM must be tolerated. Empty cells become `None`, not the empty string."* Four sentences. Each one names something Seth's project has already decided. Each one, if violated, breaks something Seth has already built or specified. The cognitive move that produces them is enumeration of what could drift: across ten runs of this same prompt, on ten different days, with ten different moods, what would I want to be *the same*? Everything on that list is an invariant.

There is an honesty gap worth flagging. The published literature on prompt-pattern ablations — White et al. (2023), Khattab et al. (2023) on DSPy — measures the contribution of structural moves like output formatting and chain-of-thought, but clean studies that isolate the specific contribution of explicit invariants against an otherwise-identical prompt are thin. DSPy's signature-based specifications produced large improvements over ad-hoc prompts, but signatures bundle invariants with output format and task naming, so the isolated effect is not measured. The case for invariants is strong in mechanism and strong in practice; the precise ablation is not yet on the table.

**The context** is the set of facts the model needs to do the task correctly that the model does not already have. The SDD sections that govern this step. The file paths of related code. The sample input. The schema the output must conform to. Claude Code, in agent mode, can read files from the filesystem — but only if the prompt tells it where to look. The model has no memory of yesterday's session, no awareness of the SDD you wrote last week, no knowledge of the dataclass you defined in the module next door, unless this turn's prompt points at them. If you want the SDD in context, name it. If you want the dataclass in context, name the file.

Anthropic's long-context guidance recommends a specific move when the context is large: have the model quote the relevant parts before producing output, because the attention mechanism may not be attending to context that appeared early and hasn't been re-triggered. At student scale the concern is usually minor, but the underlying principle is real — *naming what is relevant is not optional when the model has to decide what to attend to*. A context pointer is an attention directive.

**The output format** is what done looks like. The artifact, the destination, the verification step. Not "write the parser." *"One Python file; then run `python -m pytest tests/test_parser.py -q` and report the exit code."* The output format names the deliverable and the success criterion in the same breath, and the success criterion is machine-checkable: a pass is a pass, a fail is a fail, and "looks good" is not in the vocabulary.

This is the element with the deepest leverage, and it deserves its own argument. Whenever the task is code, include in the output format a test, a linter, or a shell command Claude can run to check the output before reporting done. The reason is structural. Given a verifier — a test that fails, a lint error, an exit code — Claude can detect that its first attempt was wrong, revise, and try again within the same turn. Without a verifier, the model's signal for "done" is its own judgment about whether the output looks reasonable. That judgment has the same architecture as the output itself: same weights, same training distribution, same confident-but-possibly-wrong quality. The verifier is an independent check. It is not perfect — a bad test suite is not a good verifier — but it is independent in a way the model's self-evaluation is not.

The same move appears across the literature under different names. Anthropic calls it tool-verifiable success criteria. DSPy calls it metric-driven optimization. Promptbreeder uses task-specific verifiers to score evolved prompts; without the verifier there is nothing to optimize toward. The unifying observation: **language models work better when they can grade themselves with something other than themselves**. The one-line cost of including a test command in the output format is among the best trades in this book.

**The negative constraint** is what Claude must not do. Files not to touch. Dependencies not to add. Related code not to refactor. *"Do not modify `models/grade.py`. Do not add new dependencies. Do not touch any file outside `parsers/`."* Three sentences. Each one closes a door Claude might otherwise wander through.

The negative constraint is the element students most often skip, because it feels like over-specification — why tell Claude not to do something it has no reason to do? The answer is that Claude has reason to do those things. The model has a training distribution full of CSV parsers that import pandas, projects where engineers update related models when interfaces change, and refactors that touch adjacent files because that is what conscientious engineering looks like. Without an explicit "do not," Claude will sometimes do the conscientious thing. The negative constraint is scope discipline made explicit. Every "do not" in the prompt is a decision you made rather than one Claude would have made, and the decision is cheaper to make in advance than to undo after the fact.

A useful diagnostic for writing them: every negative constraint should correspond to a thing Claude plausibly might do if you don't prohibit it. "Do not delete the database" is useless because Claude has no reason to delete the database in a parser task. "Do not modify `models/grade.py`" is necessary because Claude has reason to do exactly that — the parser's return type lives there, and a conscientious model might decide the dataclass needs a new field. Only write "do not" for doors that are actually open.

---

## Why Memory Doesn't Exist

The five-element format rests on one fact about Claude that, once understood, reorganizes everything else.

Claude has no memory between prompts. Every prompt is read fresh. The conversation scrolling up your screen is, mechanically, the context window the model re-reads from scratch every time you press Enter. A fact that dropped out of the context window in a previous turn does not exist for the model in this turn. The invariant you stated three sessions ago is not in this session's context unless you put it there again. The SDD you wrote last week is not visible to the model this week unless this week's prompt points at it.

This is not a deficiency. It is the mechanical nature of transformer-based language models. The state *is* the context window. The context window is what you pass in. Nothing else exists in the model's awareness.

The operational consequence is that invariants must be re-supplied every prompt, or they must live in a file that gets included in context automatically. The two working strategies are: restate in every prompt, or move the durable invariants to `CLAUDE.md` (or `.cursorrules`, or whatever your tool uses as the per-session project context file), which Claude Code reads at the start of each session. Project-wide invariants — standard library only, Python 3.11 minimum, tests in `tests/` — go in `CLAUDE.md`. Per-task invariants — UTF-8 BOM handling, `None` for empty cells — go in the prompt. The union of both is the operative specification for the turn.

The seam between project-level context files and per-prompt specifications is where craftsmanship still lives in 2026. A well-written `CLAUDE.md` makes every prompt shorter, because the durable decisions are already in context. A poorly written one lets drift accumulate, because the decisions are technically present but buried under irrelevant prose. Neither replaces the per-prompt specification, and neither makes the five-element format obsolete. They work together.

It is worth being concrete about what belongs in that file, because the same five-element discipline that shapes a prompt also tells you what to write down once. A `CLAUDE.md` is the persistent half of your specification — the part that is true on every turn — and it earns its place by holding exactly the decisions you would otherwise retype into every prompt. Four kinds of thing belong there. *Conventions*: the naming, formatting, and language decisions that hold across the whole project — snake_case identifiers, ISO-8601 dates, Python 3.11 minimum, type hints required. *Build and test commands*: the exact incantations for running and checking the project, so that when a prompt's output format says "run the tests," Claude already knows that means `python -m pytest -q` and not some guess. *Guardrails*: the project-wide negative constraints — the directories Claude must never touch, the dependencies it must never add, the files it must never delete. These are the "do not" rules that are true regardless of the task, lifted out of the per-prompt scope and made permanent. *File pointers*: where the important things live — the SDD at `SDD.md`, the data layer in `data/`, the schema in `models/` — so the model can find context without you naming the path every time.

The file is also hierarchical, and the hierarchy maps cleanly onto the difference between what is true for a project, what is true for part of it, and what is true for you. A `CLAUDE.md` at the project root carries the standing specification for the whole repository. A nested `CLAUDE.md` inside a subdirectory adds rules that apply only within that folder — useful when, say, the `tests/` directory has conventions the rest of the project does not. And a user-level `~/.claude/CLAUDE.md` in your home directory holds *your* preferences across every project you open: how you like explanations phrased, your default to standard-library-first, your habit of asking for the verifier command in every output. Project rules are shared with anyone who clones the repository; user rules are yours alone and follow you between projects. When the same decision could live in either place, the test is ownership: does this belong to the project, or to me?

A few lines of a student project's root `CLAUDE.md` make the shape clear:

```markdown
# Project: grade-parser

## Conventions
- Python 3.11+, standard library only unless a dependency is justified in the SDD.
- snake_case for functions and variables; dataclasses for record types.
- Dates are ISO-8601 strings; empty CSV cells parse to `None`, never `""`.

## Commands
- Run tests: `python -m pytest -q`
- Type-check: `python -m mypy .`

## Guardrails
- Never modify files in `data/`; the sample inputs are fixtures.
- Never add a dependency without updating `SDD.md` first.

## Where things are
- Data model: `models/grade.py`   - Parser: `parsers/grades.py`
- Spec: `SDD.md` (the file format lives in §1.4)
```

Notice that every line in that file is a decision that would otherwise have to ride along in each prompt. With it in place, Prompt 2 from the parser sequence below no longer has to say "standard library only" or "empty cells become `None`" — those are already in context, true on every turn. The per-prompt specification then carries only what is specific to *this* task: the artifact, the per-task invariants, the pointers and verifier for this step. That is the division of labor. The `CLAUDE.md` specifies the project; the prompt specifies the move. Both are specifications; they differ only in how long they stay true.

---

## The Dangerous Middle

The failure mode worth most of your attention is not the prompt with zero specification. A zero-specification prompt fails loudly — Claude produces something obviously wrong, the student knows immediately to re-prompt, and the cost is ten minutes. The dangerous failure is the prompt with three or four of the five elements. It feels complete. It compiles. It runs. It does most of the task. The defect is one layer in.

Three of five is enough to produce output that looks right. The student commits, moves to the next step, and discovers the defect two hours later when the next function can't find the data type it expected — because the context element was missing and Claude chose a different return type. Or when the CI pipeline fails on a file that shouldn't have changed — because the negative constraint was missing and Claude "helpfully" refactored an adjacent module.

The partial specification is the expensive failure because it is invisible at the point of output. The complete failure announces itself. The partial failure hides.

The countermove is a five-second self-check before pressing Enter. Have I named one specific artifact? Have I listed the invariants? Have I pointed at the context? Have I said what done looks like? Have I said what Claude must not do? Five questions. If any answer is no, the prompt is incomplete. The asymmetry is unreasonable in your favor: five seconds of friction at the prompt-writing stage trades against two hours of debugging. Run the check.

A subtler version, once the five questions are reflex: ask whether the prompt would be unambiguous to a different student who has not been in your head all week. If the answer is no, the prompt depends on context that exists in your head but not in words. The model is in the position of that different student. It has not been in your head all week. It will make a guess. The guess will be drawn from training data. Training data is not your project.

---

## The Same Format, Five Prompts Deep

Here is what the five-element format looks like sustained across a complete build sequence. Seth is building the grade parser from scratch: data model, parser, tests, a bug fix, then documentation. Five prompts. Each one is a complete specification. Each one builds on the previous without assuming Claude remembers the previous — the relevant facts are re-supplied or pointed at in each turn.

*Prompt 1 — Define the data model.*

> Write the `Grade` dataclass in `models/grade.py`. Fields: `student_id: str`, `assignment: str`, `score: float`, `out_of: float`, `submitted_at: datetime | None`. Invariants: `score` is non-negative; `out_of` is positive; `score <= out_of` enforced in `__post_init__`. Context: SDD §1.4 names these five fields. Output format: one Python file; then run `python -c "from models.grade import Grade; print(Grade.__annotations__)"` and report the output. Do not add fields not in SDD §1.4. Do not import non-stdlib packages. Do not create other files.

*Prompt 2 — Write the parser.*

> Write `parse_grades(path: str) -> list[Grade]` in `parsers/grades.py`. Invariants: standard library only; quoted commas parse correctly; UTF-8 BOM tolerated via `encoding="utf-8-sig"`; empty cells become `None`. Context: `Grade` in `models/grade.py`; SDD §1.4; sample at `data/grades_sample.csv`. Output format: one file; then run `python -c "from parsers.grades import parse_grades; print(len(parse_grades('data/grades_sample.csv')))"` and report the integer. Do not modify `models/grade.py`. Do not add dependencies. Do not touch files outside `parsers/`.

*Prompt 3 — Write the tests.*

> Write `tests/test_parser.py` with pytest functions covering: a 3-row sample parses to 3 `Grade` instances; a quoted comma in an assignment field parses correctly; a UTF-8 BOM is tolerated; an empty `submitted_at` cell becomes `None`; the `score <= out_of` invariant is enforced. Invariants: pytest only; one assert per test; use temp files in fixtures, do not edit `data/grades_sample.csv`. Context: `parse_grades` in `parsers/grades.py`; `Grade` in `models/grade.py`. Output format: one file; then run `pytest tests/test_parser.py -q` and report exit code and tests collected. Do not modify the parser or model. Do not add mocks for I/O.

*Prompt 4 — Fix the bug revealed by tests.*

> In `parsers/grades.py`, fix the bug in `test_score_with_trailing_whitespace` (currently failing). The cause is inconsistent stripping before float conversion. Invariants: passing tests must continue to pass; function signature unchanged; standard library only. Context: failing test in `tests/test_parser.py`; bug is localized to `parsers/grades.py`. Output format: one modified file; then run `pytest tests/test_parser.py -q`; all tests must pass. Do not modify the test. Do not modify `models/grade.py`. Do not refactor unrelated code paths.

*Prompt 5 — Document the interface.*

> Add subsection `§1.4.1 — Grade parser interface` to `SDD.md`. Contents: function signature, file path, CSV format rules supported, CSV format rules not supported. Invariants: document actual behavior, not aspirational behavior. Context: parser in `parsers/grades.py`; tests in `tests/test_parser.py`; existing SDD at `SDD.md`. Output format: one diff against `SDD.md`; then re-read the subsection and confirm it accurately describes what the code does. Do not modify code. Do not renumber existing SDD sections.

Five prompts. Five artifacts. Each one has all five elements. Each one has a verifier Claude can run before reporting done. And notice what is absent: there is no "please," no "if you could," no "I would like." This is not curtness — it is the tone that falls out naturally when a prompt is fully specified. Once the precision is there, the hedging becomes noise. The SDD doesn't say "please"; the prompt doesn't need to either.

---

## 🕰️ AI Wayback Machine

**Norbert Wiener** (1815–1852) wrote what we would today recognize as the first computer program — and, more pointedly for this chapter, the first specification. Her Note G to Menabrea's *Sketch of the Analytical Engine* (Lovelace, 1843) contains a precise indexed table of operations for computing the Bernoulli numbers on a machine that did not yet exist. The table names the variables, the operations on them, the order of operations, the dependencies between steps, and the expected outputs. It is a five-element specification written 183 years ago: the artifact (Bernoulli number computation), the invariants (the variable indices, the operation ordering), the context (the engine's architecture described in Notes A through F), the output (a column of results), and — implicit in the table's rigor — the negative constraint that the machine must perform *these* operations in *this* order, no others. The five-element format in this chapter descends in a straight line from Lovelace's table. The machine has changed. The discipline has not.

**

![Norbert Wiener, 1940s. AI-generated portrait based on a public domain photograph.](../images/norbert-wiener.jpg)
*Norbert Wiener, 1940s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:**

> *Specific task: write `bernoulli(n: int) -> Fraction` in `math/bernoulli.py` returning the nth Bernoulli number. Invariants: input is a non-negative integer; return type is `fractions.Fraction`; B(1) = +1/2 (the convention used in Lovelace's Note G); standard library only. Context: SDD §2 specifies the math module's interface; reference values for n=0..8 are in `data/bernoulli_reference.json`. Output format: one Python file; then run `python -c "from math.bernoulli import bernoulli; [print(n, bernoulli(n)) for n in range(9)]"` and compare against `data/bernoulli_reference.json`; report any mismatches. Negative constraint: do not import `sympy` or any non-stdlib package; do not modify the reference data; do not implement only the first few cases and stop.*

---

## Bridge

The reader can write specifications. Chapter 9 addresses what happens when the specification is right and the output is still wrong.
