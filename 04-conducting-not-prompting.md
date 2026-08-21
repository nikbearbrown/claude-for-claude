# Chapter 4 — Conducting, Not Prompting: The Core Idea

*Programming as conducting. Claude does what it's superhuman at. You do what only you can.*

---

I want to start by describing a room I have never been in.

It is a concert hall — pick the one in your head — and there is a conductor on a small raised box at the front and there are about ninety people behind him with instruments. The people behind him are extraordinary. They have spent more hours on their instruments than I have spent alive. They can sight-read music I cannot hum. They can play, on demand, exactly the note on exactly the beat with exactly the dynamic the score asks for. If I walked onto that stage and tried to play the cello part, I would be removed from the building before I finished tuning.

The conductor cannot play the cello part either. He is not better than the cellist at the cello. He is not better than the violinist at the violin. He is not, in any local sense, a better musician than any of the ninety people behind him. What he is doing — the entire reason there is a person on a box in the first place — is holding the *piece*. He has the score in his head as a whole. He knows what bar 47 is supposed to feel like against bar 12. He knows that the brass needs to come in *quieter than they want to come in* because the strings are doing something underneath that has to land first. The orchestra is excellent. They will play exactly what they understood him to mean. The gap between what he meant and what they understood is where everything breaks.

That is the chapter, in one paragraph. You are the conductor. Claude is the orchestra. The orchestra is excellent — better than you at the instruments, faster than you at the parts, capable of playing things you could not play in a lifetime of practice. The orchestra cannot hold the piece. You can. The gap between what you meant and what Claude understood is the entire reason this book exists.

![Three-panel editorial diagram. Left panel: Human as conductor — holds intent, sequences the work, writes the specification, names the boundary, verifies, decides if it ships. Right panel: Claude as orchestra — executes the contract, writes the implementation, edits named files, runs tests, reports, holds no piece across sessions. Middle panel: the interface — Specification (invariants, scope, contract) and Handoff condition (verb, target, success criterion). Arrows show writes/executes flowing right, returns/verifies flowing back left.](images/04-conducting-not-prompting-fig-01.png)
*Figure 4.1 — Who holds the piece. Who plays the parts.*

---

The cleanest way to see what has to change about how you work is to look at the same task expressed two different ways.

A **prompt** is a wish. It is a sentence in natural language addressed to a language model, formulated the way you would address a friend who is good at a thing. *Write me a function that logs users in. Get me the top customers. Sort this list.* It assumes the listener will fill in the rest. It works fine when the listener is a friend — a friend knows your codebase, knows your intent, knows that "users" means the `users` table in `db.sql` and not the `User` class in `auth.py` and not the JSON file in `seed/`. The friend resolves ambiguity by knowing you.

A **specification** is a contract. It is a structured artifact that names the invariants the output must satisfy, the fields the code may touch, the format the output must take, and the boundary the code may not cross. It does not assume the listener knows you. It assumes the listener is competent and amnesiac — capable of executing anything, remembering nothing about your intent that isn't on the page. A specification works because the listener doesn't need to know you. It knows the contract.

This distinction is not new and it is not mine. Frederick Brooks made it in 1975, in *The Mythical Man-Month*, arguing that the single most important property of a software system is what he called **conceptual integrity**: the system must come from one mind holding the user's mental model whole. Brooks's claim, blunt and famous, was that integrity arrives from a small number of people coordinating intent — not from many people consulting each other. The chief architect writes the contract. The implementers execute against it. The contract is what makes the orchestra play one piece instead of ninety competent solos in adjacent keys (Brooks, 1975).

You are the chief architect. You will always be the chief architect — Claude doesn't currently hold your mental model of what you're building, because that model lives in your head and hasn't been written down. The specification is how it gets written down.

The recent prompt-engineering literature has, independently and without much reference to Brooks, rediscovered the same thing. White and colleagues at Vanderbilt cataloged sixteen reusable **prompt patterns** for working with large language models — and the catalog is, structurally, a software-design-pattern book, each pattern with an intent, a context, a structural template, and trade-offs (White et al., 2023). The argument the paper makes by its existence is that effective prompts are not natural-language requests. They are specifications with documented invariants. Liang and colleagues at Carnegie Mellon put it in a title: *Prompts Are Programs Too* (Liang et al., 2024). The Liang paper is worth one more sentence because it documents the actual failure mode in the wild: the Carnegie Mellon team watched working software engineers build prompt-powered systems, and the dominant pattern was trial-and-error prompt construction followed by silent acceptance of plausible output. No documented invariants, no explicit output format, no testable success criterion. Does this look about right. That is the empirical core of what this chapter is trying to fix.

Here is the operational difference, smallest possible example.

**Prompt:** *write me a function that sorts a list of students by GPA.*

**Specification:**

> Write a function `top_students(records, n)` that returns the top `n` records from `records`, a list of dicts with keys `gpa: float`, `last_name: str`, `first_name: str`. Sort by `gpa` descending, then by `last_name` ascending (case-insensitive), then by `first_name` ascending. Sort must be stable. The input list must not be mutated. Return a new list. Do not import sort libraries beyond the stdlib. Do not modify any other file. Handoff condition: `pytest tests/sort.py` passes all five cases including the tie case at GPA 3.92 and the unicode last-name case.

Same task. Same Claude. The first is one sentence; the second is six. The second rules out, by construction, the bug that nearly shipped in Chapter 2 — the stable-sort-via-input-order failure that came from having no explicit secondary key. The first will produce *a* sort function. The second will produce *the* sort function. Same model. Different artifact.

A working definition you can carry: a prompt is what you say to Claude before you have decided what you want; a specification is what you say to Claude after. The decision is the work. The typing is the easy part.

---

The hardest single move in this chapter — the one most people skip the longest — is the handoff condition.

A **handoff condition** is a specific, testable predicate that must be true before the next step begins. It answers exactly one question: how will you and Claude both know that the work is done.

"Looks good" is not a handoff condition. "It compiles" is not a handoff condition. "I read the code and it seemed right" is not a handoff condition. These are vibes about completion, and vibes about completion are how the boondoggle ships. A handoff condition is a sentence with a verb you can run.

The handoff condition for the sort spec above is: `pytest tests/sort.py` passes all five cases including the tie case at GPA 3.92 and the unicode last-name case. That sentence has a verb (`pytest`), a target (`tests/sort.py`), a success criterion (all five pass), and two specific edge cases named so that "all pass" can't quietly mean "all pass except those." You can run that sentence. Claude can run that sentence. If it returns zero, the handoff is real. If it doesn't, you haven't completed the step, no matter how good the code looks.

The reason this matters more than any other piece of mechanics here is that the handoff condition is what makes the conductor metaphor work at all. The conductor in the real orchestra has the same problem — he cannot play all the instruments and check them, he has to know, as each bar passes, whether the horns landed where he meant them to. He has a handoff condition. It is the sound in the hall against the sound in his head. It is fast, specific, and falsifiable in real time. If the trombones are flat he stops the rehearsal. He does not decide that the trombones are flat by looking at the page they were given and judging that the page seems right. He listens to the room.

You will listen to the room with a test runner, a type checker, a linter, a one-line script that hits a real endpoint, a manually-written fixture file, a property test, a snapshot diff, or — at minimum — a small reproducible script that runs the new code on real input and prints what it did. None of those is "I read the code." All of those are "the room confirms what I asked for."

A small taxonomy. **Test-based:** `pytest tests/foo.py` returns zero, specific test cases named so the condition can't be satisfied by an empty file. **Type-based:** `mypy --strict src/foo.py` returns zero. **Behavior-based:** running the script on a named fixture produces a specified output with zero unhandled exceptions. **Boundary-based:** `git diff --stat` shows changes to exactly the named files, nothing else. **Performance-based:** `EXPLAIN` on the new query shows index use; the query returns under 50ms on the seed DB.

Each of these has the same shape: a verb, a target, a success criterion, a falsifiable edge. The before/after is what makes it real. If you write the handoff condition after seeing the output, you are not verifying. You are rationalizing.

Edwin Hutchins, in *Cognition in the Wild*, studied the bridge team of a US Navy ship navigating through fog and pier traffic (Hutchins, 1995). The team didn't solve the navigation problem in any one person's head. They solved it by passing structured, verified artifacts between people and tools — a position fix from the bearing-taker to the plotter to the navigator, each handoff with an explicit format and an explicit check. Hutchins's word for what makes this work is **coordination**: distributed cognition only succeeds when the artifacts passed between agents are specific, structured, and verifiable at the boundary. The handoff condition is the chapter's name for one of Hutchins's coordination primitives. Without it, you are not coordinating with Claude. You are gambling with Claude.

---

Some Claude tasks cannot run until a human task is complete. This is not a productivity tip. It is a structural property of the work.

The orchestra cannot start playing a piece that has not been chosen. The conductor cannot choose the piece during the downbeat. There is an order. Some things have to happen in human time, in human silence, before any orchestral time is allowed to begin.

In a Claude Code build, four human tasks have to precede the code. They are typical, not exhaustive.

First: decide what you are actually building. Not a feature list. A one-paragraph description of the artifact, the user, and the success condition. If you can't write that paragraph, Claude can't help you write the code, because the code will be coherent against a paragraph that isn't yours — it will be Claude's. This is Brooks's conceptual integrity: the integrity of the artifact lives in the description of the artifact, and the description is yours.

Second: read what is already there. If there is a codebase, you read it before Claude touches it. Not all of it. Enough that you know the shape, the conventions, the names, the dependencies. If you skip this step, Claude will assume conventions, and you won't know whether the assumptions are right.

Third: name the boundary. What files are in scope. What files are explicitly out of scope. What invariants must hold across the change. What tables and schemas are frozen. The boundary is what makes the work bounded; without it, Claude has the entire codebase as ambient scope, and the entire codebase is too much scope for any single task.

Fourth: write the handoff condition. Before any code is written. If you write it after the fact, you have written a justification, not a criterion.

Only after these four does Claude get to play. Claude can read for you, summarize for you, sometimes help you write the description by asking questions — but the decisions in those four bullets are *yours*, and they precede the Claude work because Claude has no basis to make them on your behalf. The artifact you're building lives in your head. It has to come out of your head and onto the page before Claude can execute against it.

This is why, in the rest of the book, you'll see a phrase repeated almost annoyingly: **explore → plan → implement → commit.** That is the dependency order, named. Exploring and planning are the human lane. Implementing and committing are where Claude joins the work. Mixing the lanes — and it will be tempting, because typing into Claude feels productive and reading a codebase quietly does not — is the most common source of failure in student builds. The feeling is wrong. Reading the codebase is the work. Typing into Claude before reading the codebase is the boondoggle.

---

There is a word for the failure mode this chapter is built to prevent, and the word is worth knowing because the etymology is the lesson.

The word *boondoggle* entered American English in the 1920s out of the Boy Scout movement — a boondoggle was a braided leather cord that scouts made at camp. It was a craft. By the standards of the camp, it was real work: you made the thing with your hands, you had a thing at the end. By the 1930s the word had been borrowed into political use and picked up a very specific extra meaning: a boondoggle was now a project that *looked* productive — that had hours logged against it, deliverables, photographs, official reports — and that accomplished nothing real. The shape of work, the surface of work, with nothing underneath.

The contemporary application is exact. A boondoggled Claude session is one where the surface is right — Claude responded, code appeared, the terminal scrolled, you spent two hours, you have an artifact — and the underneath is wrong. The code doesn't do what you needed. Or it does what you asked but you asked the wrong thing. Or it works on your sample and silently fails on real data. Or it works completely and you don't know how, and the next change will break it because you can't reason about it. The boondoggle is fluent activity with no verified output.

Naming it matters for the same reason naming "code smell" mattered when Kent Beck first used the phrase, and the same reason naming "tech debt" mattered when Ward Cunningham coined it. Programmers had felt these things for years and couldn't point at them. The name makes the thing visible, and visible things can be fixed. Boondoggling is the name for the strange exhausted feeling at the end of a session that produced a lot of output and no progress. After the name, you can see the thing. Before the name, you were inside it.

---

The work has two lanes, sequenced separately. This book has a name for them, and the name comes from a movie everyone reading this has seen.

The **Minion Part** is what Claude is for. It is the typing, the boilerplate, the syntactic resolution, the idiomatic translation, the pattern-completion, the running of the tests, the searching of the codebase, the drafting of the function once the spec is on the page. The Minion Part is high-volume, fast, error-prone in specific ways — hallucinated imports, confident wrong APIs, plausibly named functions that don't exist — and superhuman in most ways. It is named after the Minions in the *Despicable Me* movies because the analogy is exact: competent, fast, plural, eager, and exactly as good as their instructions. Without instructions they wreck things at speed. With instructions they execute things faster than the boss could alone.

The **Gru Part** is what only you can do. It is the deciding. It is holding intent across the build. It is writing the specification. It is writing the handoff condition. It is reading the result against the world. It is noticing that the test set was missing a tie case. It is the choice that one option is more interesting than another. It is the moral and aesthetic weight that the work has to carry, which Claude has no access to because Claude has no stake in the outcome. The Gru Part is what makes the work *yours.* It is the part that, if you delegate it, you haven't built the thing — you've approximately watched the thing get built.

| Gru Part (you) | Minion Part (Claude) |
|---|---|
| Decide what is being built | Generate the boilerplate |
| Read the existing code | Search for occurrences of a pattern |
| Name the boundary (files in/out of scope) | Edit the named files |
| Write the specification | Write the implementation against the spec |
| Write the handoff condition | Run the handoff condition |
| Verify against the world | Report the result |
| Decide if the result is acceptable | Apply the fix once you decide |
| Hold the piece across sessions | Hold no piece across any session |

Notice which lane has *deciding*. Notice which lane has *typing*. The Gru lane is lighter in keystrokes and heavier in judgment. That is the trade. The conductor is on a small box, not running between desks with sheet music. Your wrists will be less tired than your head, and that is the correct distribution.

What this looks like at industry scale: Seth has published a structural analysis of realism strategy in AAA game development that operates as a study of Gru-lane judgment in commercial production. The argument is that targeted realism investments reliably convert production cost into specific psychological states — presence, flow, agency, narrative transportation — and that each kind of realism (perceptual, systemic, social) supports a different monetization model. Mismatching the realism layer to the model is a structural error that no production budget can fix. *Red Dead Redemption 2* sold 82 million copies on roughly the same development cost as games that sold 10 million; the difference was not budget or talent. The difference was whether the studio held the piece across thousands of decisions. That is the Gru work scaled up: deciding which kind of investment serves which outcome, *before* any of the typing begins. The studios that get this right do not ship more polished games. They ship games whose specific polish was chosen in advance and held across the production. Conducting is the discipline that protects the decision from the typing — at student scale on a login function, and at $220-million scale on a flagship release.

---

Seth is here to walk through one build step done twice. Same Claude, same model, same minute. Two completely different artifacts.

The step: he is adding a login function to a Common App essay drafting tool he's been building for himself — somewhere to write supplemental drafts offline, sync them later, and keep a session token tied to the .edu account he's about to have. The Common App lets you save drafts but he wants his own copy he controls. He's never done auth before. Domain-shallow on authentication, which is exactly the kind of domain where the chapter's warnings apply hardest: fluent-sounding wrong output, no internal basis to evaluate it.

**Attempt one: the prompt.**

He opens Claude Code. He types: *write me a login function.*

Claude responds in about six seconds. It writes a function called `login(username, password)`. It hashes the password with MD5. It compares against a global dict called `USERS`. It returns `True` on match, `False` otherwise. It does not invalidate sessions because there are no sessions. It does not handle empty input — `login("", "")` returns `False` only if `""` is not a key in `USERS`, which is a reassuring coincidence and not a guarantee. The code is twelve lines, clean, syntactically perfect, and approximately the worst possible login function a real codebase has ever shipped.

Seth is a high school senior who hasn't done auth before. He reads the function. It is short. It uses `hashlib`, which he's heard of. It returns the right type. He runs it on a test username, it returns `True`. He is about to commit.

If he does, he ships a function that hashes with MD5 — an algorithm considered cryptographically broken for password storage for over a decade — stores users in a global dict that doesn't persist across restarts, accepts empty strings as a username, has no rate-limiting, and has no session token, which makes the function useless for the demo he intended. Every one of those failures is invisible from inside the code. Seth doesn't know that bcrypt or argon2 are the current acceptable hashes. He doesn't know that `hashlib.md5` *works correctly in Python*, which makes its presence feel like evidence of correctness. The output passes the only audit he is currently equipped to perform, which is: does this read like a login function. It reads like one. It is not one. It is a boondoggle wearing a login function's clothes.

**Attempt two: the specification.**

He closes the first attempt without committing. Opens a new conversation. Takes ten minutes to do the Gru part first.

What is he actually building? A login function for a single-user essay drafting tool, authenticating against SQLite. One user — him. Demo use only, not production — but he wants the function to be correct under the assumption that it might be reused, because Claude will use his code as context for the next thing, and bad code is hereditary.

He reads the existing code. Three files: `app.py`, `db.py`, `schema.sql`. The schema defines a `users` table with columns `username TEXT UNIQUE`, `password_hash TEXT`, `last_login TEXT`. He notices — the kind of thing he would have missed without reading — that there is already a column named `password_hash`, which means past-Seth had a plan about how passwords would be stored. He notices that `db.py` already has a parameterized query helper and he should use it, not write a new one.

He names the boundary: the change touches `app.py` and `tests/auth.py`. It does not touch `schema.sql` (frozen) and does not touch `db.py` (helpers exist; use them).

He writes the handoff condition before writing the spec: `pytest tests/auth.py` passes all five named cases — valid credentials, wrong password, nonexistent user, empty-string username, and a SQL-injection-style payload in the username field. The condition is concrete enough that both he and Claude can run it and know what "done" means.

Now the specification:

> In `app.py`, add a function `login(username: str, password: str) -> dict`. The function authenticates against the `users` table in the existing SQLite DB using the helpers in `db.py`. Password verification uses `bcrypt.checkpw` against the `password_hash` column. Empty or null username/password returns `{"ok": False, "error": "empty_credentials"}` without touching the DB. Nonexistent or wrong-password returns `{"ok": False, "error": "invalid_credentials"}` — the two cases return the same error string to avoid leaking which one failed. On success, return `{"ok": True, "token": <hex>, "exp": <iso8601 timestamp 24h from now>}` where the token is `secrets.token_hex(32)` and the timestamp is generated with `datetime.now(timezone.utc) + timedelta(hours=24)`. Do not modify `schema.sql` or `db.py`. Do not add new dependencies beyond `bcrypt`. All SQL uses the parameterized helper in `db.py`. Handoff condition: `pytest tests/auth.py` passes all five named cases. Tests must confirm that the two failure cases produce identical error strings.

That is eight sentences. It took about four minutes to write, after the reading.

Claude responds in about twelve seconds. The function is roughly thirty lines. It imports `bcrypt`, `secrets`, `datetime`, `timezone`, `timedelta`. It uses the helper in `db.py`. It handles all five cases. It uses identical error strings for the two failure branches. And — crucially — it asks one clarifying question before generating: *should the token be stored anywhere, or just returned to the caller?* Seth would not have thought to ask that in the first attempt, because the first attempt had no concept of token state.

He runs the tests. Four pass. One fails — the injection test passes the wrong value through because he had written the test slightly wrong. He fixes the test. Five pass. Handoff condition satisfied. He commits.

The second attempt didn't produce magic code. It produced correct code, which is harder than magic. The difference is not that Claude got smarter between the two attempts. Same Claude, same minute, same model. The difference is that Seth — the conductor — held the piece, named the boundary, wrote the contract, supplied the handoff condition. Claude played the parts.

The second attempt took longer. Maybe twenty minutes start to finish, against six seconds for the first. The first was faster and produced a boondoggle. The second was slower and produced something he'd put his name on. That ratio — slow at the start, real at the end, versus fast at the start, hollow at the end — is the trade this book is built to make visible.

The prompt is what you wish for. The specification is what you contract for. The handoff condition is how you know the contract was kept.

One limit, stated honestly: this works for builds where you can write down what success looks like. For exploration — figuring out what you think — the prompt-as-conversation frame is fine, and trying to specify too early is its own kind of failure. Tell the difference and pick the right frame.

---

## 🕰️ AI Wayback Machine

**James Clerk Maxwell** American polymath; Nobel laureate in economics (1978), Turing Award (1975). Simon's most consequential idea is **bounded rationality**: the human decision-maker does not optimize across all possibilities because optimization across all possibilities exceeds cognitive capacity. Instead, the human *satisfices* — chooses a path that is good enough against criteria the human can hold in mind at once (Simon, 1957, *Models of Man*; Simon, *Administrative Behavior*, 1947). Simon's claim was not that humans are stupid. His claim was that humans are *finite*, and the way finite agents do important work is by designing *systems that extend the bound* — checklists, organizations, division of labor, written procedures, tools. The bound doesn't go away; the system absorbs the part of the work the human cannot hold.

The conducting framework is Simon applied to a new instrument. You are bounded. You cannot hold an entire build in your head, evaluate every line of generated code, and remember every assumption across a four-hour session. Claude is bounded differently — limitless on the typing, blind on the deciding. The specification and handoff condition are the system that absorbs the part of the work neither of you can hold alone. Boondoggling is what happens when the system is absent and the two bounded agents pretend they aren't. Simon would have called this the failure to design for the bound — and he would have been right.

**

![James Clerk Maxwell, 1870s. AI-generated portrait based on a public domain photograph.](../images/james-clerk-maxwell.jpg)
*James Clerk Maxwell, 1870s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:** *Apply Simon's bounded rationality to a 16-year-old using Claude Code: where is the bound, what does satisficing look like in practice, and how does a specification + handoff condition encode that compromise?*

---

**Links:** [boondoggling.ai](https://boondoggling.ai) · [irreducibly.xyz](https://irreducibly.xyz)

**References (in-chapter citations):**

- Brooks, F. P. (1975). *The Mythical Man-Month: Essays on Software Engineering*. Addison-Wesley.
- Hutchins, E. (1995). *Cognition in the Wild*. MIT Press.
- Liang, P., et al. (2024). Prompts are programs too. *arXiv preprint*, Carnegie Mellon University.
- Simon, H. A. (1947). *Administrative Behavior*. Macmillan.
- Simon, H. A. (1957). *Models of Man: Social and Rational*. Wiley.
- White, J., et al. (2023). A prompt pattern catalog to enhance prompt engineering with ChatGPT. *arXiv:2302.11382*, Vanderbilt University.

---
