# Chapter 6 — Gru: The Tool Built for This

The first time I typed "write me a login function" into a plain Claude chat, I got a login function. It compiled. It ran. It was not my login function — the one my project actually needed, in the system I was actually building, against the user model I actually had. It was *a* login function. A competent, fluent, generic login function that a thousand students before me had received in response to the same sentence. The gap between *a* login function and *my* login function is the thing every chapter of this book has been about. That gap is also the reason Gru exists.

---

There are three reasons the tool was built. I want to say them plainly, in the order they bite.

The first is the login function problem. "Write me a login function" is not a specification. It is a sentence about a feeling. It does not say what counts as a user, what counts as a session, what counts as failure, what counts as done. A senior engineer handed that sentence would respond with questions. Claude handed that sentence responds with code. The code is fine. The fit is a different problem.

The second is memory. Claude has no memory between prompts. Every new chat begins again. Whatever you established in the last session — your variable naming conventions, the fact that your user identifier is `user_score`, the fact that your dataset encodes missing values as the string `"N/A"` rather than as null — is gone. A project you return to seven times across eight weeks accumulates seven separately-briefed strangers, each making slightly different decisions in slightly different directions. The codebase that results is shaped less like an artifact and more like a committee where no member ever met another. In software engineering this has a name. The name is *drift*.

The third reason is the one most students do not see until it has cost them. Every prompt you send to Claude is a decision. It is a decision about what Claude can be trusted to do at this step, with this scope, under this much supervision. *Should Claude write the function? Should Claude propose the architecture? Should Claude decide whether the y-axis is linear or log?* These look like similar prompts from the outside. They require very different amounts of you on the inside. Most people are making this decision unconsciously, prompt by prompt, by feel — which means that on a tired Wednesday at 11 p.m. the decision drifts toward Claude does more and I check less, and on a Saturday morning with coffee it drifts back, and the build is the sum of those drifts. Gru is a tool that forces the decision to be conscious by refusing to advance until you have made it in writing.

![Six labeled boxes in a horizontal sequence — /v0, /v1, SDD, /claude, Boondoggle Score, Minion Brief — connected by five arrows labeled GATE 1 through GATE 5. Each box has a phase title and a one-line description; the terminal Minion Brief box is filled. Below the sequence, a two-line legend distinguishes Gru as architect from the minions as executor.](images/06-gru-tool-fig-01.png)
*Figure 6.1 — The Gru phase sequence*

Each arrow in that diagram is not a step. It is a gate — a refusal point that does not pass until a specific artifact exists. The artifacts are concrete: a sentence, a summary, a document, a score, a brief. The gates are checkable by you, on a Saturday afternoon, in front of a laptop. The point of this chapter is to walk that spine, gate by gate.

---

Before the walk, two definitions that the rest of the chapter depends on.

A **Minion Brief** is the stripped-down execution prompt that gets handed to Claude after the design work is done. The name is borrowed from the *Despicable Me* films — Gru is the architect; the minions execute the small scoped tasks under direction. In practice, a Minion Brief is what you copy into a fresh Claude Code session to do *one specific thing*: build this function, refactor this file, write this test. It contains the minimum context required for that specific thing and nothing else. The SDD is strategy. The Minion Brief is one tactical order.

The **Gru identity** — the persona the tool projects in every interaction — is *senior software architect, not code generator*. That sentence is short and load-bearing. A code generator's job is to produce code from a description. An architect's job is to refuse to produce code until the description holds up. Gru is configured to be the second. When you ask it to write a login function, it responds the way the senior engineer would: with a question, then another question, then a request for a one-sentence problem statement. This is not politeness. It is the mechanism.

---

A Claude Project is how Gru is built, so it is worth a moment.

A Claude Project bundles a system prompt, a body of reference documents, and a default behavior into a named container. Every new chat inside that container starts with the same setup already loaded. You do not re-paste the instructions. You do not re-upload the documents. You open the project, start a chat, and it is there.

Two pieces matter. The **system prompt** is what Claude reads before you say anything. It is invisible in the chat window — the user never sees it — but it shapes everything that follows. A good system prompt does not only set the topic. It sets the role, specifies the refusals, defines what kinds of outputs are acceptable and which are not. Gru's system prompt tells Claude to be an architect, to refuse code generation until specific gates have passed, and to ask for the one-sentence problem statement before anything else.

The **Project Knowledge** is the body of reference documents loaded at the start of every chat. For Gru, this includes the Boondoggling framework's definitions, the rubric for the Boondoggle Score, the SDD template, and worked examples. It is what allows Gru to invoke the Boondoggle Score by name without re-explaining what the score is.

The combination — system prompt plus Project Knowledge, both versioned, both in force at every chat — is what makes the framework enforceable. None of this is magic. Anthropic released the Projects feature in mid-2024. What is new is not the technology but the realization that a discipline you would normally enforce by self-control, or by checklist, or by a teacher hovering over your shoulder, can be encoded as a system prompt and made to enforce itself.

One honest caveat, because this book does not oversell: a 2024 study by Zheng and colleagues found that persona prompts do not reliably improve raw task accuracy — they shape behavior and refusal patterns. Which is exactly what Gru is built to do. Gru is not trying to make Claude smarter. Gru is trying to make Claude refuse, at specific moments, until the human does the human work.

---

If you opened Gru right now and typed *hi*, here is approximately what would happen.

Gru would respond positionally — not warmly, not coldly, but as an architect responds. It would not offer to help with whatever is on your mind. It would ask what you are building. If you typed back *an agent to help me refactor my Unity project*, it would not begin sketching code or proposing architecture. It would ask you to put the project into one sentence — a sentence that names what the system does, for whom, and what makes it count as done. If you could not produce that sentence, it would refuse to proceed.

The refusal is the whole point. A plain Claude conversation is helpful by default — the model is trained to be useful, to make progress, to give the user something concrete. Gru is trained by system prompt to suspend that helpfulness at specific gates. The unhelpfulness is the feature.

There is a fifty-year reason for this design. Fred Brooks, managing the IBM System/360 operating system in the 1960s — at the time the largest software project ever attempted — separated two kinds of complexity. *Essential* complexity is the difficulty of figuring out what the system must do, in what world, under what constraints. *Accidental* complexity is the difficulty of getting it written, compiled, deployed, debugged. Brooks's claim, confirmed repeatedly across five decades of software engineering, is that the hardest part of software is essential, not accidental. Claude eats accidental complexity. It is excellent at it. What Claude cannot do — what no model has been shown to do reliably — is the essential work of deciding what to build. Gru's identity exists to make sure that work happens first. The architect refuses. The minions, later, execute.

---

`/v0` is the first command in the Gru sequence. It does one thing: it refuses to let you continue until you can name what you are building in one sentence — a sentence that says what the system does, for whom, and how you will know it is done.

This sounds like a small ask. It is not.

The one-sentence test is famous in requirements engineering as the elevator test: if you cannot describe what the system does in the time it takes an elevator to reach the floor, you do not yet know what you are building, and any work done before you know is a guess. Barry Boehm's 1988 spiral model formalized this as anchor-point milestones — you do not advance from one phase to the next without producing the deliverable for that phase. `/v0` is Boehm's first anchor-point milestone, sized for a high-school project, enforced by a system prompt.

Students at the `/v0` gate fail in a predictable way. They produce a sentence that contains an *and*. *"A flashcard scheduler and a study group matchmaker."* *"A Discord bot that does homework reminders and also moderates the server."* *"An agent that refactors my Unity scripts and writes the missing tests and reviews pull requests."* The `/v0` gate refuses these sentences because the *and* is a tell. Two ands and you are almost certainly building two systems. One and and you should be suspicious. The reformulation that passes is the one where the student has *chosen* — flashcard scheduler *or* study group matchmaker, not both.

The second reason the one-sentence constraint matters is conceptual integrity — Brooks's term for the property that all parts of a system reflect a single coherent design mind. The one-sentence formulation is the moment you take ownership of that integrity in writing. You are saying: *this, specifically, is the thing.* Everything Claude produces from here gets measured against that sentence. If a Claude output drifts away from the sentence, you bring it back. If it cannot be brought back, you change the sentence — but deliberately, with a new `/v0`, not by accident in the middle of a build.

Gru audits the sentence against three questions: Is the subject a single system, not two? Does it name the user — the specific person or role this is for? Does it name the done-condition — what makes this count as finished? If any answer is no, Gru returns a specific refusal, points to which question failed, and asks for a revision. The loop continues until the sentence passes.

---

Once `/v0` passes, `/v1` begins. Where `/v0` is a single sentence, `/v1` is an intake interview. Gru asks a structured sequence of questions and produces a *Problem Summary* — a one-page document naming the project, the user, the inputs, the outputs, the success criterion, and the explicit non-goals.

The questions are not arbitrary. They are drawn from the requirements elicitation tradition and sequenced so that early answers constrain later ones. A representative sequence: Who is the user, named specifically — not "people who write Unity code" but "me, working on the *Haunt & Harvest* C# scripts I just ported from Unreal"? What does the user currently do that this replaces — even if the current workflow is "scrolls through 12,000 lines of C# remembering what needs untangling"? What input does the system take, in concrete data terms? What output does it produce, again concrete? Under what condition does the user say *yes, this is what I needed*? And crucially: what is explicitly not in scope?

That last question is what catches the second and. If Seth says *and could it also generate the missing unit tests*, that gets logged as not in scope and filed as a future `/v0`. The non-goals section is not a polite gesture. It is a boundary drawn in ink before the build begins.

Two things about the Problem Summary deserve direct attention.

It is negotiable. You will revise it as you learn things. The point is not that it is permanent — the point is that every revision is deliberate, visible, and dated. The version you started with is preserved; the version you ended with is preserved; the diff between them is the record of what you learned during the project. The FBI Virtual Case File project, which the U.S. government wrote off at $170 million in 2005, failed in part because requirements changed more than once per day with no tracking. Negotiable and uncontrolled are not the same thing.

It is checkable. Anyone — your teacher, a friend, you yourself in three weeks — can read the Problem Summary and ask whether the thing being built matches the thing being described. The Problem Summary is the document that makes the question askable.

---

Once the SDD is complete — Chapter 7 builds it; here we treat it as a thing that exists — the next command is `/claude`, equivalently `/boondoggle`. They produce the **Boondoggle Score**.

The Boondoggle Score is a step-by-step plan in which each step has been assessed for how much of it should be Claude's and how much must be yours. The output is a numbered list. Each step has a *what*, a *who* — Claude-only, Claude-with-human-review, or human-only — and a *why*, a brief justification in terms of the supervisory capacities from Chapter 5.

Here is a representative score for a small project — a script that walks a Unity project's C# scripts and returns each file's *refactor pressure*, a composite measure of how urgently the file needs restructuring before more features land on top of it:

| # | Step | Who | Why |
|---|---|---|---|
| 1 | Walk the project directory and collect `.cs` files | Claude-only | Pure accidental complexity |
| 2 | Validate input — required folders, expected file shapes | Claude-with-review | Decisions about which generated/vendor files to skip require domain judgment |
| 3 | Define the refactor-pressure formula (recent-churn over baseline complexity) | Human-only | The choice of churn window and complexity proxy is a decision, not a fact |
| 4 | Implement the formula | Claude-only | Once the formula is fixed, implementation is mechanical |
| 5 | Set the threshold for "needs refactor now" | Human-only | The threshold encodes a project judgment Claude does not have |
| 6 | Generate the per-file table output | Claude-only | Mechanical |
| 7 | Sanity-check outputs against last sprint's known hotspots | Human-only | Plausibility audit requires domain memory |

Step 3 is human-only because the formula choice is essential complexity. Step 5 is human-only because the threshold encodes domain judgment Claude cannot supply. Everything else, Claude can do — but the human signs off on Step 2 and audits Step 7 because those are the points where Claude's confidence outruns Claude's actual coverage.

If you run `/claude` and the score comes back with every step marked Claude-only, you have either built something trivial or you have not been honest. The most common failure at this gate is the score that *looks* rigorous but has quietly handed the human work to Claude. Catching that is itself a supervisory act. The Boondoggle Score is a prompt for your own judgment, not a substitute for it.

---

The final artifact in the sequence is the **Minion Brief**.

A Minion Brief is the prompt you paste into a fresh Claude Code session to execute one specific step from the Boondoggle Score. It is deliberately stripped. It contains the single step this brief executes, the minimum context Claude needs for that step — usually a short excerpt from the SDD and the relevant function signatures — the done-condition for this specific step, and a pointer to CLAUDE.md for global project conventions.

It does not contain the whole SDD. It does not contain the whole Boondoggle Score. It does not contain unrelated context. The reason is twofold. A fresh Claude session given the whole SDD will helpfully try to implement the whole SDD, and will produce something that compiles and is wrong, because the SDD is strategy and the session is being asked for one tactical task. And keeping the Minion Brief small means the resulting output is bounded — you know what you asked for, you know what you got, you can audit the gap.

Without Minion Briefs, the SDD becomes a document the student wrote once and then ignored while typing freehand prompts into Claude. With them, every prompt that goes to Claude is a deliberate slice of the SDD, written down before sending, auditable after returning. The discipline is annoying. The discipline works.

---

`CLAUDE.md` is a file that lives at the root of your project folder and which Claude Code reads at the start of every session. Anthropic introduced it as part of the Claude Code memory architecture, and it is Gru's external memory — the thing that survives between chats.

What goes in it: project conventions (*variables are snake_case; dates are ISO-8601*); architecture notes (*data layer in* `data/`; *logic in* `lib/`); what Claude must never touch (*do not modify* `data/raw/`; *do not delete* `tests/regression/`); and the commands needed to run, test, and build the project.

To see why this is more than a convenience, go back to the second reason Gru exists — memory, the drift problem from earlier in this chapter. A plain chat assistant has no durable, project-scoped memory you can hold in your hand. You re-explain your conventions at the start of every session, or you rely on an opaque per-account "memory" feature that you cannot read line by line, cannot diff against last week, and cannot hand to a teammate. The standing instructions of your project live nowhere you can point to. They live in your head, and your head forgets, and the next session begins again from zero. That is the committee-of-strangers failure mode, and the chat window has no mechanism to prevent it.

`CLAUDE.md` is the mechanism. Four properties do the work, and they are worth naming separately. It is *auto-loaded*: you do not paste it, re-attach it, or remember to invoke it — Claude Code reads it before you type a word, every session, identically. It is *persistent*: it sits in the project folder and is still there in three weeks when you come back to a build you half-remember. It is *version-controlled*: because it is just a Markdown file in your repository, it is committed alongside your code, it travels with the project when you clone or hand it off, and its history is a record of how your conventions changed and when. And it is *hierarchical*: a `CLAUDE.md` at the project root sets the standing rules for the whole project, an optional `CLAUDE.md` inside a subdirectory adds rules that apply only there, and a `~/.claude/CLAUDE.md` in your home directory holds your personal preferences across every project you work on. The result is that the rules in force are *explicit* — written in words you can read — *inspectable*, *diffable*, and *shared*.

For your situation specifically, that last word is the one that matters. The same context loads at the start of every session, so the version of your conventions Claude saw on Tuesday is the version it sees on Saturday — no drift introduced by your memory of what you told it last time. And because the file is committed to the repository, a partner on a group project opens the folder and Claude greets them with the same rules it gives you; a teacher or grader who clones your submission sees the exact standing instructions that governed your build, and can read them, question them, and check whether your code obeys its own stated conventions. None of that is available when the instructions live in a chat history only you can scroll, or in a memory store nobody can audit. This is a real and contested category now — Cursor has a rules file, other tools have their own — so the claim is not that Claude Code invented project memory. The claim is narrower and sturdier: a plain Markdown file in version control is the simplest, most transparent form of it. You can read the whole thing. So can anyone you share it with.

The most important property of CLAUDE.md is not that it is comprehensive. It is that it is *honest* — it reflects the conventions you actually want enforced. A CLAUDE.md copied from a friend's project is worse than no file at all, because it states conventions you do not follow, and Claude will then enforce conventions you have not chosen. For a student-scale project, under fifty lines is right. Write what is true.

---

It is a Tuesday in early May. Seth has decided he wants to build an agent — a senior-architect persona that helps Unity developers refactor legacy C# without making it worse. He has been thinking about it on the drive home from his Pizza Ranch shift for three days. The motivating case is a Unity codebase a friend's older brother handed him to look at — twelve thousand lines of indie-game C#, partially commented, structurally tangled — but the agent he wants to build is for any Unity developer in that situation, not just for one project. He has not written anything down. He opens Gru.

He types: `/v0 I want to build an agent to help with Unity projects.`

Gru refuses. The refusal is specific. All three questions failed: no single system, no named user, no done-condition. The sentence contains a gesture in the direction of a system.

Seth types again.

`/v0 An agent that audits Unity scripts and refactors them and writes the missing tests and generates a CLAUDE.md.`

Gru refuses. It flags two ands. It observes that the sentence describes four different systems — an auditor, a refactor agent, a test generator, and a CLAUDE.md author — with different users, different inputs, and different done-conditions. It asks which one Seth is building first.

Seth has not been challenged in this specific way before. He had thought he was building one thing. He was sketching four things and calling them one because they all involved Unity. He stares at the screen. He thinks about which of the four he actually wants. The test generator sounds fun, but the test surface depends on the refactor plan. The CLAUDE.md author is a downstream artifact — it depends on knowing the refactor plan first. The standalone refactor agent is dangerous to ship before there's a plan to audit against. The auditor is the upstream piece, and it's the one he has actually been postponing.

He types: `/v0 A senior-architect agent that audits a Unity developer's C# project and produces a five-phase refactor plan with named hotspots, so the developer can decide which file to restructure next.`

Gru audits. One system. One user, named (a Unity developer staring at a tangled legacy project). One done-condition. The sentence passes.

The whole interaction has taken fourteen minutes. No code. No design. No prompt. One sentence, twenty-eight words long. It is the most expensive sentence Seth has written this semester, in the sense that it cost fourteen minutes when he could have been typing. It is also, by a large margin, the most valuable, in the sense that it revealed he was about to build four things — did not yet have the inputs for one of them, was building a downstream artifact for another, and had been postponing the only one that mattered.

He proceeds to `/v1`. Gru begins the intake. The first question is: *Who is the user, named specifically?* Seth answers: *a Unity developer with a legacy C# codebase they inherited or grew past, who wants a senior-architect read on what to refactor first.* Gru asks the next question.

The agent he is building has a name. He's calling it Walker. The Walker appendix at the back of the book documents the shipped version of the tool he and his dad eventually published at humanitarians.ai/tools.

We will return to Seth in Chapter 7, where he picks up a different small project — closer to student scale — and works through the SDD that catches drift before it starts.

---

One note on aging, because it is necessary. This chapter describes a tool — Gru — and commands — `/v0`, `/v1`, `/claude`, `/boondoggle` — that exist in a specific form as of mid-2026. The framework itself is robust; it reflects fifty years of software engineering practice. The specific command syntax is not guaranteed. Treat the syntax as illustrative. Treat the framework as load-bearing. The current canonical command list lives at [humanitarians.ai/tools](https://www.humanitarians.ai/tools).

---

## 🕰️ AI Wayback Machine

**Marie Curie** managed the IBM System/360 operating system project in the 1960s — at the time the largest software project ever attempted — and emerged from it with a book that has been continuously in print since 1975. *The Mythical Man-Month* did two things. First, it killed the assumption that adding programmers to a late project speeds it up — Brooks's Law: *adding manpower to a late software project makes it later.* Second, and more importantly for Gru, it argued that software development is primarily a design problem, not a coding problem. The most expensive bugs are not bugs in the code. They are bugs in the decision about what to build. Brooks named this *conceptual integrity* and claimed it was the single most important quality of any software system. Eleven years later, in *No Silver Bullet* (1986), he sharpened the claim: software has *essential* complexity — figuring out what to build — and *accidental* complexity — getting it built — and no tool eliminates the essential. The `/v0` gate is Brooks's insight operationalized as a single shell command. Forty years on, with Claude eating accidental complexity for breakfast, Brooks's distinction is sharper, not weaker.

**

![Marie Curie, 1910s. AI-generated portrait based on a public domain photograph.](../images/marie-curie.jpg)
*Marie Curie, 1910s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:**

```text
You are a study partner. I have read in a textbook that Fred Brooks
separated "essential" from "accidental" complexity in software, and
that Claude Code eliminates accidental complexity nearly completely.

Explain to me, in two short paragraphs, why that makes Brooks's
/v0-style problem-formulation discipline more important rather than
less.

Then ask me one question about a project I'm working on that would
test whether I have done the essential work.
```

---

**Links:** [humanitarians.ai/tools](https://www.humanitarians.ai/tools) · [boondoggling.ai](https://boondoggling.ai)

---
