# Chapter 10 — Brutalist: When the Build Is Creative

*The technical barrier is now low enough that any student can produce ambitious creative work. The question is whether the creative judgment stays theirs.*

---

Nicholas works on the creative side of this book. He is the contributor who watches what happens in the studios and the design labs and the music classes that the AP-CS frame of the previous chapters doesn't quite reach. His observation is the one this chapter exists to honor.

His classmates, he says, let AI generate the prose. Then the graphics. Then the music. They paste a prompt; they read what comes back; they accept it; they ship. The output is polished — uniformly, glossily, *more* polished than what most of them could have made by hand. The fonts pair correctly. The chord progressions resolve. The cover essays open with a striking sentence and close with a satisfying one. Underneath the polish, Nicholas can feel a void. The work has no soul. There is no person inside it. When you ask the maker why the song is in D minor rather than A minor, or why the palette is cool blue rather than warm orange, the maker shrugs. They didn't decide. The model decided. They liked what came back. They moved on.

The void is the chapter. It is the same void this book has been circling since the introduction — the gap between the student who built a thing and the student who *received* a thing — but in creative work the void is harder to see, because the surface is more attractive. A broken function announces itself: the program crashes. A soulless essay reads beautifully. It just doesn't belong to anyone.

The argument of this chapter is that the same discipline that solved the code problem also solves the creative one. The discipline has a name. It comes from architecture by way of conceptual art and lands, in 2026, on a framework Nicholas calls **Brutalist**.[^brutalist-site] Brutalist is what you write down before Claude touches the file, so that when Claude touches the file Claude does the technical work and you keep the creative judgment. The previous chapters drew the line for code. This one draws it for everything else.

[^brutalist-site]: The live framework documentation is at [brutalist.art](https://brutalist.art). The site is the canonical reference for the exact file schemas, the refusal-behavior spec, and the running list of student creative builds in the wild. The framework name borrows deliberately from architectural Brutalism, on which see the footnote on Banham below.

![Three nested rectangles in the Brutalist constitution. Outer: CLAUDE.md, the technical constitution — what the work is made of. Middle: DESIGN.md, the visual constitution — what the work looks like. Inner, filled and bordered in red: PROJECT.md, the Intent Layer — what the work is for, written by the human.](images/10-brutalist-creative-builds-fig-01.png)
*Figure 10.1 — The Brutalist three-file system*

---

The fluency trap was named for code in Chapter 4. The same trap operates, more dangerously, in creative work, because the signal of correctness is no longer "the tests pass." The signal is "this looks good," and "this looks good" is exactly the signal Claude is trained to produce.

The empirical record is now strong enough to settle the question. The cleanest single data point is a 2025 synthesis in *Frontiers in Education* on AI in academic writing: across post-ChatGPT college admissions essay corpora, lexical diversity *rose* — students used more varied vocabulary — while thematic distinctiveness across essays *fell*.[^fie-2025] That is the polish-and-void pattern in one number. The essays got smoother and the essays got more identical. A reader picking up any one of them would have said *this is well written*. A reader picking up a hundred would have said *I have read this exact essay before*.

The mechanism is the same one this book named for code: Claude has defaults. Claude's training distribution produces a particular register, a particular palette, a particular rhythm. When the student does not contest the defaults, the defaults appear in the work. When the student does not *know* the defaults exist, the student cannot contest them. The classmates in Nicholas's observation are not lazy. They are operating inside a tool whose aesthetic gravity they cannot see, because the tool answers them politely every time they ask.

There is a federal record of where this leads. Jason Allen submitted *Théâtre D'opéra Spatial* — a Midjourney-generated image refined across roughly 624 prompt iterations, post-processed in Photoshop, upscaled with Gigapixel, with about eighty hours of work — to the Colorado State Fair's digitally-manipulated-photography category in August 2022. It won first prize.[^allen-2022] He then filed for U.S. copyright registration. The Copyright Office refused. It refused again on review. In September 2023 the Review Board issued its final decision: the human authorship was **de minimis**.[^uscocb-2023] The eighty hours of prompt iteration, by the Office's analysis, did not constitute authorship of the resulting image. Allen filed an appeal in U.S. District Court in September 2024; as of this writing the case is unresolved.[^allen-appeal]

The Copyright Office is not making a moral claim. It is making a documentary one: in the prevailing federal standard, iterating on prompts is not authorship of what the model produces. You can disagree with the standard. The Brutalist framework, in fact, partly disagrees with it. But you cannot pretend the standard does not exist. The Brutalist response is not to argue with the Office — it is to change the underlying procedure so that what the student produces is, in fact and in document, theirs.

In code, the void announces itself at the test suite. In creative work, the void looks like *Théâtre D'opéra Spatial* — beautiful, technically masterful, awarded a blue ribbon, and the federal record says no one made it.

[^fie-2025]: The synthesis is *Frontiers in Education* (2025), "The impact of generative AI on academic reading and writing: a synthesis of recent evidence (2023–2025)." It aggregates results from admissions-essay corpora, classroom writing studies, and stylometric analyses. The lexical-up / thematic-down pattern was reported in admissions-essay studies summarized in *Technology in Society* (2025), "Creative scar without generative AI: Individual creativity fails to sustain while homogeneity keeps climbing." Both are open-access via DOAJ.

[^allen-2022]: The Colorado State Fair announcement is in the Fair's August 2022 press materials; the prompt-iteration count and Photoshop/Gigapixel pipeline are Allen's own disclosure to the U.S. Copyright Office during the registration process.

[^uscocb-2023]: U.S. Copyright Office Review Board (September 5, 2023). *Re: Second Request for Reconsideration for Refusal to Register "Théâtre D'opéra Spatial."* The decision is public on the Copyright Office website. The operative phrase — *de minimis* — appears in the Board's analysis of the contribution of human-authored editing relative to the Midjourney-generated base image.

[^allen-appeal]: *Allen v. Perlmutter*, U.S. District Court for the District of Colorado, filed September 26, 2024. The appeal challenges the Copyright Office's interpretation of human-authorship requirements under §102(a) of the Copyright Act.

---

The architectural movement called Brutalism was named by Reyner Banham in *Architectural Review* in December 1955 and expanded into a book — *The New Brutalism: Ethic or Aesthetic?* — in 1966.[^banham] Banham identified three guiding principles in the work he was describing: memorability of the image; clear exhibition of structure; valuation of materials "as found." The buildings were Brutalist because nothing was hidden. The concrete was concrete. The bolts were bolts. The structural members showed. The visible decisions were the only decisions.

The name itself comes from Le Corbusier's coinage *béton brut* — "raw concrete" — for the surfaces of the Unité d'Habitation in Marseille, completed in 1952. *Brut* does not mean *brutal*. It means *unfinished in the way that lets you see what it is*. A Brutalist building tells you, by looking at it, how it stands up.

The Brutalist framework for creative builds borrows the architectural ethic in a precise way. The governing principle is one sentence:

> Claude is **maximally informed** and **minimally autonomous**, by design.

*Maximally informed* means Claude has read everything that matters before it produces anything. The technical constitution. The visual constitution. The project state. The student's intent. Not as background context but as constraints that bind every decision. A Brutalist Claude knows the palette, the type scale, the voice rules, the negative constraints, the escalation triggers, the refusal conditions — before the first token of the output exists.

*Minimally autonomous* means Claude does not decide what is not specified. If the DESIGN.md does not say what color the second header is, Claude does not pick a color. Claude escalates — it asks the student. The refusal to improvise is not a failure mode. It is the feature. The architectural analog is exact: a Brutalist building does not pretend to be made of marble. If the budget is concrete, the building is concrete, and the concrete is visible. A Brutalist Claude does not pretend to be making creative decisions. If the spec is silent, Claude is silent. The silence is what protects the work from being inflected by Claude's defaults.

The contrast is with the prevailing alternative — maximally autonomous and minimally informed — the Midjourney-style workflow where the user offers a sentence and the model invents the rest. That workflow optimizes for impressiveness per prompt. Brutalist optimizes for authorship retention per build. They are different goals. The chapter is not arguing that Midjourney is wrong; it is arguing that if you want the work to be yours, you cannot use a maximally autonomous tool the way it is intended to be used. You have to constrain it back.

Brutalist is the constraint.

[^banham]: Banham, Reyner (December 1955). "The New Brutalism." *Architectural Review.* Expanded as *The New Brutalism: Ethic or Aesthetic?* (London: Architectural Press, 1966). The three principles — memorability of the image, clear exhibition of structure, valuation of materials as found — are quoted from the 1955 essay, pp. 354–361. Le Corbusier's *béton brut* coinage is documented in his 1952 publications on Unité d'Habitation, Marseille.

---

The Brutalist framework is operationally three files. They are written before the build starts, they live alongside the project, and Claude reads them every session. Each file protects a different thing.

**CLAUDE.md — the technical constitution.** The file you have already seen in earlier chapters. It names the stack, the dependencies, the directory layout, the lint and format rules, the testing conventions, and the things Claude must never improvise about. For a creative project it is shorter than for an enterprise codebase, but not empty: the project uses Vega-Lite for charts and not D3 unless specifically requested; data lives in `data/` and is never modified by Claude; outputs land in `out/`; all fonts are referenced from `assets/fonts/` and never pulled from Google Fonts at runtime. CLAUDE.md is the contract about what the work is **made of**.

**DESIGN.md — the visual constitution.** This is the file the prevailing workflow does not have, and the file that makes Brutalist Brutalist. Every aesthetic decision is here, by name, with a value or with an explicit escalation. Primary color: `#1A1A1A`. Secondary: `#FAFAFA`. Accent: `#E63946` — used only for emphasis, never for chrome. Type scale: 14 / 18 / 24 / 36, monospace for body, sans-serif for headers, EB Garamond for pull-quotes. Line height: 1.45 for body, 1.15 for headers. Margins: 32 / 48 / 64 — escalate before deviating. And the escalation list: Claude must escalate before choosing any color not in this file, any font weight not in this scale, any spacing value outside the listed margins, any animation or transition. DESIGN.md is the contract about what the work **looks like**. The principle is not that the student decides everything in advance — it is that the student decides everything *or names what is escalated*. Ambiguity is forbidden. If the file is silent, Claude does not improvise; Claude asks.

The shape of the file stays the same across wildly different aesthetics; only the values change. That is the point — the file is a contract, not a style. A brutalist build's DESIGN.md is loud and oppositional:

```markdown
# DESIGN.md — raw
PALETTE:    #000000 ink · #FFFFFF paper · #FF3B00 alarm (max one per view)
TYPE:       Helvetica Neue Bold, 16/20/48 only — no italics, no light weights
SPACING:    8px grid, hard 0 radius, 1px borders, no shadows
LAYOUT:     visible grid lines, left-aligned, no centering, no max-width
EXCLUDE:    gradients, rounded corners, drop shadows, hover animations
```

That produces a page that looks like a printed worksheet: hard edges, exposed structure, a single screaming accent. Swap the values and keep the shape, and you get a clean minimal build:

```markdown
# DESIGN.md — quiet
PALETTE:    #2E2E2E text · #FFFFFF bg · #6B7280 muted · #3B82F6 link only
TYPE:       Inter, 15/17/28, line-height 1.6, weight 400/600 only
SPACING:    8px base, 12px radius, generous whitespace (min 64px section gaps)
LAYOUT:     single column, 680px max-width, centered, lots of air
EXCLUDE:    more than one accent color, all-caps, borders heavier than 1px
```

That produces the calm reading surface of a well-made documentation site. Change the values once more and you get a playful retro build:

```markdown
# DESIGN.md — arcade
PALETTE:    #1A0B2E bg · #FF2E97 hot pink · #00F0FF cyan · #FFE600 coin gold
TYPE:       "Press Start 2P" headers, VT323 body, chunky 24/32 sizes
SPACING:    4px pixel grid, 0 radius, 4px solid borders, hard offset shadows
LAYOUT:     centered cards, scanline overlay, blinking accents allowed here
EXCLUDE:    subtle gradients, thin type, muted colors, anything "tasteful"
```

That produces an 8-bit poster — neon on near-black, pixel type, the deliberate kitsch the minimal file forbids. Three files, one schema, three irreconcilable looks. The student is not choosing a Claude preset; the student is writing down a look and binding Claude to it, whatever the look happens to be.

**PROJECT.md — the project state, with the Intent Layer at its center.** This is the file that holds the human. PROJECT.md has the standard project-management fields — current phase, open tasks, blockers, decision log — but it begins with a section labeled **INTENT LAYER** that the student writes in their own words, before any prompt, before Claude sees anything. The Intent Layer answers, in plain English: Who is this for? What is it for? What should the reader / viewer / listener feel? What argument does it make? What does it explicitly refuse to do? Those five questions are non-negotiable. They are not generated. They are not paraphrased from a prompt the student gave Claude. They are written by the human, in the human's voice, before the build starts. The Intent Layer is, by construction, the part that cannot be delegated.

The three files together form what architectural Brutalism calls the clear exhibition of structure. CLAUDE.md is the structural members. DESIGN.md is the surface treatment. PROJECT.md is the program — the human purpose the building serves. None is hidden. None is optional. None can be re-derived from the others. This is what *brut* means in *béton brut*. The decisions show.

---

Brutalist makes six commitments. Each one names a specific thing the framework refuses to let Claude do.

**One: Specification over improvisation.** Every aesthetic decision is either specified in DESIGN.md or escalated to the student. Claude never picks because the file is silent. Silence is escalation.

**Two: Refusal over guessing.** When the spec is ambiguous, Claude refuses to proceed and surfaces the ambiguity. The refusal is not a bug. It is the entire point. A model that guesses smoothly is a model that erodes authorship one micro-decision at a time. A model that refuses to guess is a model that forces the human to remain in the decision.

**Three: Materials as found.** No hidden dependencies. No "I'll pull a quick font from the web." No "I'll regenerate the data because the version on disk looked stale." The materials the project uses are the materials in the project's directories, named in CLAUDE.md, version-pinned. If the build runs, the build runs on what is actually there.

**Four: Structure visible.** The three-file system is not a private convention. It is documented in the repository, readable by anyone who picks the project up. A classmate, a teacher, a reviewer, a future-self should be able to look at the project and see how the work stands up. If they can't, the structure is not Brutalist — it is just files.

**Five: Intent retained.** The Intent Layer of PROJECT.md is human, always. Claude can copy-edit it; Claude cannot generate it. If the Intent Layer drifts during the project — and it will, because building changes what you understand — the student updates it by hand, in plain language, with a date stamp. The Intent Layer is what the Copyright Office calls the non-de-minimis human contribution. It is also what Nicholas means when he says he can hear whether the work has a person in it.

**Six: Output is auditable.** Every artifact Claude produces traces back to a line in one of the three files. If a color appears in a chart, the color is in DESIGN.md. If a sentence appears in an essay, the voice rules it follows are in CLAUDE.md. If the second paragraph argues a particular thing, the thing it argues is in the Intent Layer. The work is, in the strict sense, defensible — the student can show, line by line, where they decided.

Six commitments. Together they form the *ethic* part of Banham's *ethic or aesthetic?* Brutalist is an ethic. The aesthetic — the look of the chart, the sound of the essay, the feel of the piece — is what the ethic produces.

---

The single most counterintuitive feature of Brutalist, and the one students reject first, is that Claude is configured to **refuse**. The refusal happens in three places, and naming them ahead of time prevents the experience of refusal from being read as failure.

Claude refuses when the DESIGN.md is silent on a decision the build requires. The student asks for a chart; the chart requires a color choice; the color is not in DESIGN.md; Claude does not pick a color. Claude returns a message that names the missing decision and waits. This is the feature. The alternative — Claude picking viridis, or Claude picking its house warm-gray palette, or Claude picking whatever the prompt's ambient emotional valence suggests — is exactly the silent inflection that produces work without authorship. The refusal is what protects the student from shipping a chart whose palette was decided by a training distribution they cannot see.

Claude refuses when the Intent Layer is empty or missing. PROJECT.md without the five questions answered is a project Claude will not begin. This is unfamiliar. It is closer to a co-author saying *I cannot write this until you tell me what it is for* than to a tool saying *processing your request*. Students conditioned by tools that produce something on every prompt will, on first encounter, read the refusal as a tool malfunction. It is not. It is the tool doing the one thing the prevailing tools refuse to do: insist that the human be present before the work begins.

Claude refuses when its proposed output would change a file outside the declared scope. Scope is named per session — *this session may modify `charts/`; everything else is read-only*. If Claude proposes an edit to a file outside the scope, Claude does not execute. It surfaces the proposal and waits. This is the creative-build analog of the negative constraint from Chapter 8 and the scope statement from Chapter 9. Agentic drift is a creative-work failure mode as much as a code one; the chart-rendering session that "while it was at it" rewrote the dataset is the same class of failure as the parser fix that "while it was at it" renamed a model field.

The three refusal modes share a single underlying claim: the model's friendliness is not free. A model that produces something on every prompt is producing on prompts where producing is exactly the wrong move. The Brutalist refusal is the friction that holds authorship in place against a model whose default is to dissolve it.

| Domain | AI handles | Human keeps |
|---|---|---|
| Visual / design | Tile a fixed six-color palette across an HTML page; lay out a card grid; apply named typography rules | Choose the six colors. Choose the typefaces. Decide which interactions are allowed and which are refused. |
| Writing / longform | Apply consistent formatting, expand a structured outline into prose, fix mechanical errors against a style guide | Set the register and the stance. Write the central claim. Choose what the piece refuses to say. |
| Music / audio | Generate stems against a specified tempo, key, and instrumentation list | Choose tempo, key, instrumentation, song structure. Decide the emotional arc. Cut what does not serve the arc. |
| Web / interaction | Implement specified components against a documented design system | Decide which components exist. Decide what the system does not do (no auto-play, no infinite scroll, etc.). |
| Documentation | Render structured content (tables, diagrams) from a specified outline | Write the SDD. Define the User Needs. Decide what each section refuses to anticipate. |

---

Seth had an agent to build — a GDD consultant he was calling Zelda, intended to live alongside Walker on humanitarians.ai/tools and serve student game designers writing their first design documents. The thing to build was the agent itself: its system prompt, its command library, its pushback rules. The point of the agent was that, like a senior designer reading a junior's GDD over coffee, it should push back when the design was hand-wavy, refuse to advance when the pillars were tagline-shaped, and surface the seven specific failure modes Seth had watched himself fall into across *Bubble Pop* and *Haunt & Harvest*. Same Claude. Same notes file. Same technical request: produce a system prompt and a command library for a GDD consultant agent. What differed was the framework around the request.

**Build one: prompt only.** Seth opened Claude and typed: *write me a system prompt for a senior game designer agent that helps students write a Game Design Document; include some commands for reviewing pillars, mechanics, and scope; make it feel like a real consultant.* Claude produced a draft in about ninety seconds. Friendly tone, encouraging phrasing, three commands — `/review_pillars`, `/review_mechanics`, `/review_scope` — each ending with bullet points and a *"great work! Here are some ideas to consider"* close. The persona was warm. The commands were polite. The agent's refusal behavior was nonexistent. Seth read it. It read fine. He almost shipped it.

Two days later, he had Nicholas read it. Nicholas asked when the agent ever pushed back. Seth pointed at the *"things to consider"* bullets. Nicholas said those were suggestions, not pushback — suggestions are what you give a peer; pushback is what you give a designer who's about to ship a scope-shaped problem as a feature. He asked why the agent never refused. Seth said that's how it came back. Nicholas asked why there were three commands and not the thirty-some Seth had been muttering about for weeks. Seth said three felt like enough. Nicholas told him, gently, that he had not designed an agent. He had *received* an agent.

Claude's defaults had decided it — encouraging tone, three-command minimum-viable shape, suggestions instead of refusals, no phase gates, no path-forward rule when pushing back. Seth's accumulated taste for what a senior designer actually does — the seven failure modes he could list off the top of his head, the specific moment when a "fun" mechanic is actually a scope vampire, the Silent vs Interactive mode he had wanted from the start — was nowhere in the output. The agent the prompt produced was a polite assistant, not a senior designer. Claude's defaults had inflected the persona.

**Build two: Brutalist.** Seth wrote three files first.

CLAUDE.md: *Agent is a Claude Project. Reference docs in `kb/` (the seven-failure-mode audit, the five-phase model, the Walker companion-agent spec). System prompt in `prompts/zelda_system.md`. Command definitions in `commands/`, one Markdown file per command. No external API calls. No web browsing in the deployed agent. Silent mode and Interactive mode are first-class.*

DESIGN.md: *Persona voice: senior game designer with twelve shipped titles, addressing a junior who is allowed to be wrong but not allowed to be vague. Refusal style: surface the specific tagline word, quote it back, ask what mechanic it cashes out as. Pushback layer always closes with a path-forward sentence — never "this is bad," always "this is bad because X; the closest version that works is Y, want to try?" Command library size: 34 commands across 5 phases (Discovery, Pillars, Mechanics, Scope, Audit). Phase gates: an Audit-phase command refuses to run until the Pillars phase has at least one named pillar with a falsifiable mechanic. Mode: Silent (no commentary, only command outputs) vs Interactive (running margin notes). Failure-mode audit list: seven items, each named in a separate `commands/audit_failure_*.md` file. Escalation list: any new command not in the list of 34; any tone shift away from the senior-designer voice; any output longer than the 800-word per-turn cap.*

PROJECT.md INTENT LAYER: *This agent is for student game designers writing their first real GDD — the moment between "I have a game idea" and "I have a design someone else could build." The user is most likely a high-schooler or first-year university student, alone with a Google Doc at 1 a.m. The agent's job is to refuse on their behalf — to refuse the vagueness, the scope creep, the unfalsifiable pillar — because there is no senior designer in the room to do it for them. The user should leave a Zelda session with a sharper document, not a praised one. The agent explicitly refuses to be a cheerleader. The agent explicitly refuses to validate a tagline before its mechanic exists. The agent explicitly refuses to expand scope when the user is in Audit phase.*

Then Seth ran the same prompt: *produce the system prompt and command stubs per CLAUDE.md and DESIGN.md, honoring the PROJECT.md intent.*

Claude refused once: the DESIGN.md said the pushback layer always closes with a path-forward sentence, and Claude flagged that the failure-mode audit commands sometimes have no clean path forward — should those commands skip the rule, or should the path-forward sentence become "this one has no clean fix; here's the smallest thing you could ship and revisit later"? Seth answered: the second. Claude proceeded. The draft rendered in about ninety seconds, same as before. The persona refused to praise. The first audit command quoted back a pillar that read *"the game is about tension"* and asked what mechanic produced the tension. The path-forward rule held. The 34 commands were stubbed, phase-gated, mode-aware. The agent the build produced was the agent the Intent Layer said it should be.

In review, Nicholas asked Seth why the persona refused to praise. He said: *because the user is at 1 a.m. with a Google Doc and praise doesn't sharpen anything*. Nicholas asked why there were 34 commands and not three. He said: *because a senior designer has a vocabulary; the vocabulary is the work*. Nicholas asked why the path-forward rule existed. He said: *because pushback without a path forward is just discouragement, and the user already has plenty of that*. He answered three questions. He had a reason for each one. He had not written the system prompt with his hands — Claude had — but every decision in the agent was traceable to a decision he had made, in writing, before Claude saw the request.

Same Claude. Same notes. Same render time. Different agent. Different authorship. The Zelda that ships at humanitarians.ai/tools — the one documented in the appendix to this book — is, in the documentary sense the Copyright Office cares about, Seth's. The agent in build one was Claude's with Seth's name attached. Brutalist is the procedure that produces the first kind of agent instead of the second.

---

The chapter's argument, finally, is one sentence — the one Nicholas's observation produces:

> **AI handles technical execution. You keep creative judgment.**

The sentence sounds obvious. It is not, because the line between *technical* and *creative* moves with the technology. Five years ago, "render the chart" was technical and "choose the chart type" was creative. Today both are technical — Claude can pick chart types competently, judging by the rules that good charts follow. So the line moves inward. Today, "choose the palette" is technical — Claude can pick palettes — but *"choose the palette this project will use"* is creative, because it is a question about what the project is for, and the project is for whatever the Intent Layer says it is for. The technical / creative line does not stay fixed. It retreats, year by year, toward the irreducibly human core.

The Brutalist commitment is to keep retreating with it. As Claude gets better at picking palettes, the student does not abdicate palette choice; the student specifies the palette in DESIGN.md and lets Claude render against it. As Claude gets better at writing prose, the student does not abdicate voice; the student codifies voice rules in CLAUDE.md and lets Claude copy-edit against them. The line moves; the discipline moves with it. What does not move is the core: the Intent Layer, the answer to *what is this for and who is it for and what should the reader feel*. That, currently, is irreducible. The day it becomes reducible is the day a different chapter has to be written. This is not that day.

Walter Benjamin wrote in 1935 about the loss of *aura* under mechanical reproduction.[^benjamin] His claim, simplified, was that the unique presence of an artwork eroded once the artwork could be perfectly copied. Benjamin was writing about photographs of paintings, not generative AI. But the question his essay asks is the question Brutalist asks: what is the residue of authorship that survives reproduction? For Brutalist, the residue is operational. The aura that survives is the Intent Layer. It is what the work was *for*. It is what cannot be reproduced because it was never an image to begin with. It was a decision a person made. The decision is recoverable from the document. The document is the proof.

This is why Brutalist insists, against the legal current of the Allen ruling, that intent is authorship. The Copyright Office sided with execution. Conceptual art sided with intent — for sixty years it sided with intent. Brutalist sides with conceptual art. The chapter is making the side-take explicit because the empirical record runs both ways and the student should know they are choosing.

[^benjamin]: Benjamin, Walter (1935). "The Work of Art in the Age of Mechanical Reproduction." First published in French in *Zeitschrift für Sozialforschung* V (1936); English translation by Harry Zohn in *Illuminations*, ed. Hannah Arendt (New York: Schocken Books, 1969). The *aura* passages appear in §II–IV of the standard 1969 translation.

---

## 🕰️ AI Wayback Machine

**Claude Shannon**

LeWitt was an American conceptual artist whose work, beginning in the late 1960s, took a form that looked, at first, like a clerical error: a written instruction, sometimes a single sentence, sometimes a page, that *other people* were to execute. *Wall Drawing #260* (1975) reads in part: *On a black wall, all two-part combinations of white arcs from corners and sides, and white straight, not straight, and broken lines.* That is the work. The drafting was done by gallery assistants. The piece exists wherever a competent executor has the certificate of authenticity and the instructions; when the exhibition ends, the wall is painted over. The next exhibition, somewhere else, draws it again. The piece is the instruction. The execution is what LeWitt called a perfunctory affair.[^lewitt-1967]

LeWitt's "Paragraphs on Conceptual Art" in *Artforum* (June 1967) and "Sentences on Conceptual Art" in *0-9* magazine (January 1969) lay out the argument: *In conceptual art the idea or concept is the most important aspect of the work. When an artist uses a conceptual form of art, it means that all of the planning and decisions are made beforehand and the execution is a perfunctory affair.*[^lewitt-1969] And: *Once the idea of the piece is established in the artist's mind and the final form is decided, the process is carried out blindly.*

*The process is carried out blindly.* That is what Claude does inside a Brutalist constitution. The Intent Layer is established; the DESIGN.md is decided; the execution is perfunctory. The Brutalist principle was stated, in full, fifty-nine years before Claude Code existed to enact it. LeWitt was not anticipating AI. He was making a claim about authorship that the AI moment has rendered urgent: the author is the person who holds the intent and writes the instruction, regardless of who or what executes. The instruction tradition is broader and older than LeWitt — Yoko Ono's *Grapefruit* (1964) is on the page sooner, and Adrian Piper's *Catalysis* (1970–73) is the philosophically sharpest articulation — but LeWitt's two short essays are where the operational form was first made.[^lewitt-tradition]

**

![Claude Shannon, 1950s. AI-generated portrait based on a public domain photograph.](../images/claude-shannon.jpg)
*Claude Shannon, 1950s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:**

> Pick the most recent piece of creative work you produced with Claude's help — an essay, a chart, a slide, a song, a piece of code that you would call expressive. Write the *instruction* for it, after the fact: the LeWitt-style spec that would let a competent executor reproduce the work. Try to be exhaustive. Now look at the spec and mark the parts that say *what the work is for* in red, the parts that say *how the work looks* in blue, and the parts that say *what the work is made of* in black. If the red is missing, you did not retain the Intent Layer. If the blue is missing, your DESIGN.md is implicit. If the black is missing, your CLAUDE.md is implicit. Whichever color is missing is the file you need to write before your next build.

[^lewitt-1967]: LeWitt, Sol (June 1967). "Paragraphs on Conceptual Art." *Artforum* 5 (10): 79–83. The essay's load-bearing sentence on *planning and decisions made beforehand* is on the first page of the original *Artforum* layout.

[^lewitt-1969]: LeWitt, Sol (January 1969). "Sentences on Conceptual Art." *0-9* (5). Reprinted in *Art-Language* 1 (1) (May 1969). Aphorisms #10 (*Ideas alone can be works of art*) and #28 (*the process is carried out blindly*) are the load-bearing ones for the Brutalist analogy. Monoskop hosts a verified digital scan of the original 0-9 publication.

[^lewitt-tradition]: Yoko Ono's *Grapefruit* (Tokyo: Wunternaum Press, 1964) published instruction-art three years before LeWitt's *Artforum* essay. Adrian Piper, who studied with LeWitt and later became a Harvard analytic philosopher (Ph.D. 1981), articulated the intent-as-art position in explicitly philosophical terms beginning with *Catalysis* (1970–73). Hanne Darboven's numerical tables — "writing without describing" — are the closest art-historical analog to a CLAUDE.md. The instruction tradition is older, broader, and more diverse than the canonical LeWitt-centered story; the framework here is downstream of all of them.

---

**Links:** [brutalist.art](https://brutalist.art) · [boondoggling.ai](https://boondoggling.ai) · [irreducibly.xyz](https://irreducibly.xyz)

**References (in-chapter citations):**

- Allen v. Perlmutter, U.S. District Court for the District of Colorado, filed September 26, 2024.
- Banham, R. (1955, December). The new Brutalism. *Architectural Review.* Expanded as *The New Brutalism: Ethic or Aesthetic?* London: Architectural Press, 1966.
- Benjamin, W. (1935/1969). The work of art in the age of mechanical reproduction. In H. Arendt (Ed.), *Illuminations* (H. Zohn, Trans.). New York: Schocken Books.
- Frontiers in Education (2025). The impact of generative AI on academic reading and writing: a synthesis of recent evidence (2023–2025). Open-access via DOAJ.
- LeWitt, S. (1967, June). Paragraphs on conceptual art. *Artforum*, 5(10), 79–83.
- LeWitt, S. (1969, January). Sentences on conceptual art. *0-9*, (5). Reprinted in *Art-Language*, 1(1), May 1969.
- Technology in Society (2025). Creative scar without generative AI: Individual creativity fails to sustain while homogeneity keeps climbing. Open-access via DOAJ.
- U.S. Copyright Office Review Board (September 5, 2023). *Re: Second Request for Reconsideration for Refusal to Register "Théâtre D'opéra Spatial."*

---
