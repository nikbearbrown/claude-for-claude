# Chapter 0 — Introduction: The Line


## TL;DR

- This chapter gives a working overview of Introduction: The Line, focusing on the ideas a reader needs before moving to the next chapter.
- The chapter moves through Learning outcomes, Opening, What this book is — and what it is not, Why Claude Code specifically, and related ideas.
- Read it for the main argument, the vocabulary it introduces, and the practical judgment it asks you to develop.

> There is a line between what Claude executes and what only you can do. This book teaches you to draw it — by building real things, then teaching students to draw it themselves.

---

## Learning outcomes

1. **(Understand)** Explain the difference between prompting Claude and conducting a build with Claude.
2. **(Understand)** Describe what the interleaved structure means for how to read this book.
3. **(Remember)** Identify the three builds that form the book's arc.

---

## Opening

This is Seth, eighteen months after the students book came out.

I was not planning to write again. The students book had gone out into the world. Teachers had ordered it for their classes. The framework had outlived my involvement, which is the standard test for whether a framework is worth anything. I was teaching ninth-grade Earth science and helping with the school's AP CS class, and I had returned to the quiet rhythm of a teacher who has a few good tools and uses them.

Then I sat down to build a small thing for my own classroom.

The thing was a sorting-algorithm simulator. Three sorting algorithms — bubble, merge, quick — visualized as bars on a screen, with sliders to change the array size and speed. The kind of tool that exists, but only in the places where someone has already polished the demo. I wanted the version for *my* class: simple enough that ninth-graders could use it without a tutorial, accurate enough that AP students could trust the runtime annotations, themed in the four colors my class website uses.

I opened my terminal. I typed `claude` and pressed enter.

What happened next is the book.

Not the build itself — the *recognition*. As Claude opened the project folder, read the existing files, proposed a plan, and asked which sort to start with, I understood from the inside what the students book had been pointing at. Every decision I made — *what's the scope, what should the AI never improvise, where does the explanation begin and end* — was the discipline the students book described. I had been teaching it. I had not, until that afternoon, been *living* it on something that mattered to me.

That gap — between teaching the discipline and living it — is what this book closes.

If you are reading this, you are likely the teacher the CSTA's 2025 Landscape Report describes. Ten or more years in a classroom. Three or four years teaching CS. Experienced, committed, undersupported, and tired by Tuesday afternoon. You have used Claude for lesson plans. You have watched students use AI in ways that make you uneasy. You probably have thirty minutes on Monday night and the rest of the week is already booked.

The book meets you there. Not with an exhortation. With a sequence.

![The book's arc as three build tiers on](images/01-introduction-the-line-fig-01.png)
*Figure 1.1 — The book's arc as three build tiers on*

---

## What this book is — and what it is not

This book is not about generating better lesson plans with Claude. That book exists and the Anthropic Education Report tells us most teachers already do that work — saving an average of about six hours a week with AI tools focused on content production. The content-generation use case is real. It is also not what this book teaches.

This book teaches you to **conduct Claude Code through real classroom builds** — a class website, a grading tool, an interactive simulation — and then to **teach that same discipline to the students who use those tools**. The same teacher does both halves. The teacher who has *built* a grading tool with Claude Code knows something the teacher who has only *read about* grading tools does not know: what the discipline costs to skip. That knowledge is the pedagogical content this book exists to develop in you.

The argument is that the teacher-as-builder and the teacher-as-instructor are the same discipline applied twice. The book is structured to make that explicit. Every chapter has a teacher-build component and a classroom-activity component, side by side, applying the same concept simultaneously. You finish each chapter already knowing how to teach it — because you just did both.

---

## Why Claude Code specifically

Claude Code is not Claude.ai. The difference matters from the first session.

Claude.ai is a conversation with a model. You type, it answers, you read, you decide. The model has no file system access, runs no commands, makes no changes outside the chat window. Whatever you decide to do with the output is your work, performed by your hands.

Claude Code runs in your terminal. It reads your project files. It runs commands. It edits files. It chains operations across multiple steps without asking permission at each step (unless you require it to). It is *agentic* — designed to take action toward goals, not just produce text in response to questions.

That autonomy is the feature. It is also the reason you need a discipline before you start. A model that only produces text cannot delete the wrong file, modify the grade book, or overwrite the lesson you spent three hours writing. An agentic system can do all of these by accident, in less time than it takes to undo them. The discipline this book teaches is what makes agentic capability safe and useful in a classroom context.

The discipline is tool-agnostic at the conceptual level. The framework — explicit context files, the verify-before-trust gate, supervisory capacities, handoff conditions, the three-file system for creative builds — applies to Codex, to GitHub Copilot CLI, to whatever agentic surface comes next. The chapters use Claude Code's specific affordances (CLAUDE.md, Skills, Hooks, Subagents, /init, /rewind, plan mode) because those are the tools in front of you now. The framework outlives the tools.

---

## The three builds

The book is built around three deployable artifacts. By the end of Chapter 14, you will have all three running and a post-build document for the third.

**Act One — Class website (Chapters 1–4).** Low-stakes. The website is your course landing page: syllabus, schedule, links, contact. If it breaks, you fix it. The build introduces the four foundations: your first terminal session, CLAUDE.md as your project's persistent context, specification prompts, and handoff conditions. Gate 1 closes Act One when you have one deployable page and one designed classroom activity per concept.

**Act Two — Grading tool (Chapters 5–9).** Higher stakes. The tool touches student submissions and produces feedback drafts. The build introduces the five supervisory capacities, Claude Code's Skills system (build once, reuse forever), Hooks (the enforcement layer that turns "Claude should not do X" into "Claude cannot do X"), and Subagents (delegated context-clean work). The dangerous middle hits in Chapter 8: feedback that is technically correct and pedagogically wrong. Gate 2 closes Act Two when you have a working grading tool with hooks and subagents, and you have lived through the dangerous middle.

**Act Three — Interactive simulation (Chapters 10–14).** Highest stakes — the build runs in front of your students. The Brutalist three-file system (CLAUDE.md + DESIGN.md + PROJECT.md) governs the simulation's technical, visual, and intent layers. Chapter 11 executes the build with the full framework. Chapter 12 deploys in your actual classroom. Chapter 13 maps the students book to what you now know how to teach. Chapter 14 is the post-build document — your record of what you built, what you delegated, what you kept, what you would do differently.

The three builds escalate not in technical complexity but in *who they serve*. Acts One and Two serve you (faster website, better grading). Act Three serves the student (an experience that did not exist before you built it). The discipline is the same across all three; the stakes escalate; the framework holds.

---

## How to read this book

Cover to cover once. Chapter by chapter during builds.

The first read takes 8–12 hours. You will not be at a terminal for most of it. You are getting the shape of the framework and the vocabulary the rest of the book uses. Skim if you must; do not skip.

The second read is non-linear. You will open Chapter 2 when you are writing your first CLAUDE.md. You will open Chapter 7 when you decide your grading tool needs a hook. You will open Chapter 12 the night before you deploy the simulation in class. The book is structured to be useful in both directions.

The students book — *Claude Code for Students: A Practitioner's Guide* — is the companion. Your students may have it (it is a $1 Kindle title). Many will not. Chapter 13 maps the students book chapter by chapter to your build experience; you can teach from this book whether your students have read theirs or not. The strongest classroom outcomes come when both books are in play; the book is designed not to require it.

---

## A practical note on dictation

You have thirty minutes on Monday night. The specifications you write to Claude are sometimes long. Typing them is the slow part.

On macOS, System Settings → Accessibility → Dictation enables Voice Control. The default shortcut to start dictating is to press the Globe key (or Fn key) twice. On Windows, press Win+H. On Linux, the cleanest path is currently Speech Note or whisper-ctranslate2 piped into a hotkey; the standard varies by distribution.

Speaking your specifications is faster than typing them and — for many teachers — produces better specifications because the spoken form is more natural than the written one. You hear yourself name the constraint; you hear yourself notice the ambiguity; the specification gets stronger.

This is a small detail. It compounds across the 200 specifications you will write across the book. Mention it in the first chapter for a reason.

---

## A note on Seth's voice

I open this book. I open the bridge between Acts Two and Three. I appear at three other moments where the chapter calls for the voice of someone who has done the build. Outside those four points, the book is in the second person: *you*. The teacher is the primary actor from Chapter 1 forward. The book is yours.

I never give instructions in this book. I describe what I experienced. The discipline of the chapters is the book's instruction. The narration is the texture.

---

## What you will have built by Chapter 14

A class website that deploys with one command. A grading tool that produces draft feedback you review and approve. An interactive simulation your students used in class. A CLAUDE.md that has grown across all three builds. A post-build document that captures the decisions you made.

A classroom that has watched you conduct, not just code. Students who have written CLAUDE.md sections of their own, audited a build log, run the gate on their own builds. Lesson plans for the unit you are teaching from the inside out.

A discipline you can describe because you have lived it.

That is the book.

---

## Assessable exercises

1. **(Remember)** Name the three builds in this book's arc. For each, name the conducting concept the chapter introduces (CLAUDE.md, supervisory capacities, three-file system — etc.).

2. **(Understand)** In one paragraph: what is the difference between a teacher who uses Claude to generate lesson plans and a teacher who conducts Claude through a classroom build? The paragraph should pass the test of being readable by a department colleague who has not seen the book.

3. **(Understand)** Read the first chapter of the students book (*Claude Code for Students*). Name one concept Seth introduces that you will teach using your own build experience rather than the students book's example.

---

## What would change my mind

The chapter's strong claim is that **the teacher-as-builder and the teacher-as-instructor are the same discipline applied twice** — and that the teacher who has lived the discipline can teach it in ways the teacher who has only read about it cannot. If a controlled comparison found no measurable difference in student outcomes between teachers who completed the book's builds and teachers who used the framework descriptions without doing the builds, the book's structural premise weakens. The interleaved chapters become a writer's affectation rather than a load-bearing pedagogical choice.

I expect the difference to be substantial because the pedagogical content knowledge research (Shulman 1986, Mishra & Koehler 2006) consistently finds that *knowing how to teach a subject* requires *having practiced the subject* in a way that surfaces the specific points where students get stuck. The build is what surfaces those points.

---

## Still puzzling

- **The right amount of Seth's voice.** Too much: the book feels like a memoir. Too little: the practitioner's witness disappears. The book's working answer: four scenes (this chapter, the bridge, two chapter openings) where the voice carries weight that the second-person *you* cannot.

- **The terminal barrier for teachers without prior terminal use.** Chapter 1 is the on-ramp. Some readers will need an extra walkthrough; the book points to *code.claude.com* for the installation steps in unusual environments. The framework holds regardless of which path to the first session you take.

- **What the second build looks like.** The book ends with one full simulation. Most teachers who finish the book will build a second within the next semester. That build is faster, the CLAUDE.md is reusable, the framework is reflex. The book does not narrate the second build because the second build is yours.

---

## Links

- [code.claude.com](https://code.claude.com) — official Claude Code documentation.
- [CSTA 2025 Landscape Report](https://landscape.csteachers.org) — the reader portrait this chapter draws on.
- [Anthropic Education Report (August 2025)](https://www.anthropic.com/news/anthropic-education-report-how-educators-use-claude) — the teacher-as-builder validation.

---

##  AI Wayback Machine
 **John Dewey** (1859–1952) — American philosopher of education whose *Experience and Education* (1938) argued that **learning is the transformation of the learner through purposeful experience**, not the deposit of information into the learner.[^1] Dewey's most consequential contribution to teacher education was the concept of *reflective practice* — the teacher who learns by teaching, who deepens their own understanding through the act of making the discipline explicit for students, who treats the classroom as a site of inquiry for both parties.

![John Dewey](../images/john-dewey-7x5.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

The interleaved structure of this book — teacher build and classroom activity in every chapter — is Dewey applied to AI-assisted classroom builds. The teacher who builds a grading tool with the framework and immediately designs a classroom activity using the same concept is doing reflective practice in real time. The cognitive structures that constitute the teacher's understanding of conducting (Shulman's pedagogical content knowledge) are built through the *engagement* with both halves, not through the reading-about-engagement Dewey warned was insufficient.

Dewey wrote in 1938 about teachers and laboratories. He could not have imagined Claude Code. The principle scales without translation. The teacher who builds is the teacher who can teach building. The teacher who has only read about building can describe the framework but cannot, when a student is stuck mid-build at 2:14 PM on a Tuesday, name what the framework would say to do — because the framework's specifics live in practice, not in description.

The book's premise is Dewey's premise: experience is the mechanism. The chapters that follow are the structured experience.

---

## Bridge

The arc is named. Chapter 1 opens the terminal.

---

[^1]: Dewey, J. *Experience and Education*. Macmillan, 1938. The Kappa Delta Pi reprint (1998) is the standard recent edition.
