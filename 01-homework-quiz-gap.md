# Chapter 1 — The Homework/Quiz Gap: What's Actually Happening

*Students who use AI freely during practice score dramatically lower on unassisted tests — and feel like they learned more, not less.*

---

Here is a fact that should bother you.

A thousand Turkish high school students were split into groups. One group did their math practice the normal way — on paper, by hand, no help. Another group had unrestricted access to GPT-4. They could ask it anything, including "just give me the answer," and it would. At the end of each practice session, both groups had their work recorded.

Then the laptops went away.

Everyone took the same unassisted exam on the same material.

The group that had done 48% better during practice — the AI group, the group whose homework scores looked spectacular — scored 17 percentage points *lower* on the exam than the kids who had done it on paper with no help at all. Not 17% lower in some relative sense. Seventeen full points on the test. The effect size was *Cohen's d* ≈ 0.738, which is what statisticians call moderate-to-large — the kind of number that, in a drug trial, would get the drug approved. The probability it was a fluke was about one in a hundred (Bastani et al., 2025).

| Measure | AI-assisted group | Hand-coding group |
|---|---|---|
| Practice score (with AI access) | 48% above control | At control baseline |
| Unassisted exam score | 17 points below control | At control baseline |
| Score gap (practice → exam) | −65 points | ~0 |
| Cohen's *d* (effect size) | 0.738 | — |
| *p* value | 0.01 | — |

Same students. Same material. Same week. The tool that made their practice look better made them measurably worse when the tool was gone.

That is the thing I want to explain.

---

Before I explain it, let me make sure the experiment is clear in your mind, because the details matter.

Hamsa Bastani and her colleagues at Wharton ran this as an RCT — a randomized controlled trial (Bastani et al., 2025). That means the students weren't self-selecting into groups based on their own habits or confidence. They were randomly assigned. Random assignment is what makes the comparison honest: on average, the students in one group are identical to the students in the other, except for what the researchers did to them. When you see a big difference in outcomes between randomly assigned groups, you can't explain it away by saying "well, the weaker students ended up in one group." They didn't. The coin flip saw to that.

There was also a third group — a GPT Tutor arm, where the same GPT-4 model had been prompted to behave like a tutor instead of an answer machine: it would scaffold, ask leading questions, refuse to hand over solutions outright. That group scored 127% better during practice — even more spectacular-looking than the unrestricted group — and ended up roughly *equal* to the paper kids on the exam. Not better than control. But not worse. Just level.

Let me put that side by side. Two groups with access to the same model. One group: 48% better on practice, 17 points worse on the exam. Other group: 127% better on practice, no difference on the exam. The only thing that varied was whether the model would hand over the answer.

The machine was the same machine. The discipline imposed on the machine was different. That single design decision — would the model require the student to do some of the work, or not — was the entire difference between learning and not learning.

---

Now, why?

When you sit with a problem you don't know how to solve, something specific happens. Your brain pulls up fragments of related things it already knows — a technique that might apply, an example that's similar in shape, the general architecture of how this class of problem tends to go — and tries to combine them. Most attempts fail. You notice they fail, discard them, try something else. Each cycle of *try → fail → adjust* is a small event: your brain is generating candidate answers, checking them against what it knows, revising the model.

That cycle is uncomfortable. It is also the event that does the work.

Cognitive psychologists have a name for the counterintuitive version of this: **desirable difficulties** (Bjork & Bjork, 2011). A desirable difficulty is a challenge that feels like it's slowing you down while actually speeding up retention. The two best-documented examples are the **testing effect** — retrieving a fact from memory strengthens the memory more than rereading it — and the **generation effect** — producing an answer yourself consolidates it more than reading the correct answer written by someone else. Both effects feel worse in the moment and pay off later. Both depend on the learner doing the work that, with an AI in the room, can be skipped.

Robert and Elizabeth Bjork put the general principle plainly: short-term performance and long-term learning are not the same thing, and often they are negatively correlated (Bjork & Bjork, 2011). Make practice easier, and you usually make learning worse. You remove friction that the brain was using to encode. The struggle is not an obstacle to the encoding event. The struggle *is* the encoding event.

This was not invented by cognitive scientists. William James said it in 1890, in plain English, in *The Principles of Psychology*. He wrote that the nervous system is a plastic material — one that physically reshapes itself through repeated effort. What you do twenty times with attention becomes what you can do once without it. The mechanism James described in 1890 is the mechanism Bastani measured in 2025. The novelty is only the tool that lets you skip the struggle and still produce the artifact.

---

So here is what happens when you paste the problem into Claude.

Claude produces a clean solution. You read it. It makes sense — it really does. You follow the logic step by step. You nod. You paste it in. You move on.

What your brain is reporting to you in that moment is *comprehension* — the warm sensation of following a well-explained argument. And that report is honest: you did follow it. The logic was sound, you tracked it, you understood it in the moment the way you understand a YouTube explanation while it's playing.

But the sensation of following is not the act of producing. Your brain encodes the two events differently. Following correct code is an experience of comprehension. Writing wrong code and fixing it is an encoding event. The first gives you the feeling of having the skill. The second builds the skill. The outputs on the screen are nearly identical — two versions of working code. The invisible output, the version of you that exists six weeks later, is not identical at all.

I'll call this **the fluency trap**. The trap is that the fluent path — the path where Claude gives you a clean solution — genuinely feels better than the effortful path. It is not just faster. It is more pleasant. The brain reports "I am learning" while the cognitive events that constitute learning are not happening. Bjork and Bjork call this the **illusion of competence** (Bjork & Bjork, 2011). You feel competent because the output in front of you is competent. The output's competence belongs to the model. Your competence will be measured when the screen is closed.

![Two parallel paths. Path A moves from struggle to consolidation to durable capability. Path B moves from delegation through fluent output and no consolidation to atrophy.](images/01-homework-quiz-gap-fig-01.png)
*Figure 1.1 — The fluency trap*

---

Nataliya Kosmyna's team at the MIT Media Lab ran 54 college students through essay-writing sessions with EEG caps — 32-channel sensors measuring the electrical activity of the brain through the scalp (Kosmyna et al., 2025). Three groups: writing from their own heads, writing with a search engine, writing with ChatGPT.

EEG can't tell you what someone is thinking. It can tell you, in real time, how synchronized different brain regions are with each other — what neuroscientists call **functional connectivity**, roughly the degree to which different parts of the brain are communicating while a task is being performed. More connectivity in the frequency bands associated with effortful cognition is, very approximately, the EEG signature of "this brain is working on the problem."

The ChatGPT-assisted writers showed up to a 55% reduction in neural connectivity compared to the brain-only writers. That's the maximum reduction across regions, not the average, but the pattern is consistent. The brain doing the writing with ChatGPT was substantially less internally coordinated than the brain doing the writing without it.

Two other findings land harder than the connectivity number. First: 83% of ChatGPT users could not accurately quote a sentence from their own essay minutes after submitting it. Their hands had typed those words, more or less. The encoding event didn't happen. The sentence wasn't theirs in the way memory normally makes a thing yours. Second: when the ChatGPT users wrote a fourth essay *without* the assistant, the reduced connectivity persisted. The brain didn't immediately revert to its unassisted patterns. Whatever engagement pattern they had learned to use with the tool in the loop, they kept doing without it.

A necessary caution: this is a preprint, not yet peer-reviewed (Kosmyna et al., 2025). The mechanism is suggestive, not proven. Kosmyna's team has not demonstrated that the connectivity drop *causes* the kind of exam gap Bastani measured. What they've shown is that, on a brain-activity measure correlated with cognitive engagement, the AI-assisted brain is doing measurably less work. That is a plausible mechanism for the Bastani result. It is not a confirmed one. The study that would confirm it has not yet been run. I'm putting both papers here because they point the same direction, not because one explains the other.

---

In January 2026, Jane Hsieh Shen and Alex Tamkin published an RCT with 52 junior Python engineers, learning a library called Trio — an asynchronous library none of them had used before (Shen & Tamkin, 2026). Some engineers used Claude during the learning tasks. Some did not. Afterwards, everyone took a comprehension assessment with no AI. The headline: AI-assisted engineers scored about 17% lower on comprehension than the unaided coders, even though they'd finished tasks slightly faster. Same pattern as Bastani. Different domain. Different population. Convergent result.

This is also a preprint, not yet peer-reviewed, which I'm flagging for the same reason as the Kosmyna paper.

What makes the Anthropic paper useful beyond replication is that the researchers transcribed the interactions and identified patterns. The engineers who scored worst — average comprehension below 40% — clustered into three distinct behaviors. The engineers who scored best — above 65% — did not.

**AI Delegation.** The engineer hands the whole task to Claude, reads the output, and moves on. No interrogation. No "why did you do it this way?" Just paste and ship.

**Progressive AI Reliance.** The engineer starts on their own, hits difficulty, asks for help, and from that point the assistant is doing most of the cognitive work. The reliance grows — each subsequent problem begins with "let me just ask Claude" — without the engineer noticing the accumulation.

**Iterative AI Debugging.** The engineer writes code, doesn't know why it's broken, pastes the error into Claude, accepts the fix, runs it again, pastes the next error. The loop eventually produces working code. It does not produce a person who can debug. The engineer forms a model of which error messages Claude can handle. They never form a model of why the code was broken.

All three patterns share a structure: the engineer never has to build an internal model of the system. Claude builds it for them. Or rather — and this is the important thing — Claude produces outputs that make an internal model *unnecessary in the moment*, which means no internal model gets built.

The contrast group — the engineers who scored above 65% — used the assistant differently. The Anthropic researchers called the pattern **conceptual inquiry**. Those engineers asked questions about the library *before* asking it to write code in the library. They used the model as a tutor, or a colleague who had read the documentation. They wrote their own implementation first and then asked Claude to critique it. The window looked the same. The moves were different.

---

The largest performance gap between the AI-assisted engineers and the unaided ones was not on production tasks — "write code that does X." It was on **diagnostic** tasks: why is this code behaving this way? What would change if we removed this line?

This is the thing that should make you stop.

Production tasks, you can fake. Ask Claude to build the thing; Claude builds the thing. The output is indistinguishable from the output of someone who built it themselves. Diagnostic tasks, you cannot fake, because diagnostic tasks require an internal model — a picture of what the system is supposed to be doing and where that picture diverges from what the system is actually doing — and that internal model is exactly what delegation doesn't build. You can ask Claude to write a function. You cannot ask Claude to have your mental model of the function, because Claude doesn't have your mental model. It only has the code.

Borrowed capability is invisible until the tool is gone. Diagnostic tasks make it visible. They are the test that distinguishes the version of you that knows the thing from the version of you that has seen the thing.

---

Two students. Same AP CS class. Same database project: design a schema for a small library inventory system. Books, authors, checkouts, due dates. Both submit working schemas. Both get an A.

*This is a composite, drawn from what I've observed and what the Bastani and Anthropic papers describe. The pattern is documented.*

Student A starts with a notebook. She writes down entities: books, authors, checkouts. She draws arrows. She gets it wrong — authors as a column on the books table, which makes co-authored books impossible. She notices the problem when she tries to imagine adding a Pratchett-Gaiman collaboration and realizes she'd need two rows for one book. She rewrites it. She types the SQL by hand. The first migration fails because she forgot a foreign key. She finds the missing key, fixes it, runs it again. She submits.

Student B opens Claude. He pastes the assignment. Claude produces a clean schema — books table, authors table, join table, foreign keys, indexes on the columns you'd expect. Student B reads it. It looks reasonable. He pastes it into his SQL editor. The migration runs first try because Claude already accounted for the foreign keys. He submits.

Both get an A. Claude's schema is probably slightly better, actually — it includes a soft-delete column Student A didn't think of.

Six weeks later, the quiz. Design the schema for a small social-media-style application.

Student A reaches for the habit. She writes down entities. Users, posts, comments. She thinks about whether a comment is a post — for now she'll keep them separate. She thinks about likes. A like is a relationship between a user and a post, so it's a join table. She is doing, on a different domain, the same *moves* she did six weeks ago. The library project didn't give her the schema she needed for the quiz. It gave her the habit of producing schemas.

Student B reads the question. He knows what a schema looks like — a bunch of tables with foreign keys between them. He writes "Users table, Posts table" and stops. He doesn't know whether comments should be a separate table or a column. He doesn't have a model of *when you make something a table* versus *when you make it a column*. He saw the output of that decision six weeks ago. He never made the decision. The quiz ends with two words on his page.

Same final grade six weeks prior. Different person now. The Anthropic researchers would call what Student B did AI Delegation. Bjork would call the missing thing the generation effect. James would say the pathway was never traversed, so it was never grooved. Bastani would say: this is what the exam measures, and we measured it.

---

Here is the practical move, and I am putting it at the end because the rest of the chapter earned it.

Before you ask Claude to *build* something in a domain, ask Claude *questions* about the domain. Ten minutes — by the clock. Ask: what does this class do? What does this function expect? What are three ways someone could mess this up? What would change if I removed this dependency? You are not asking Claude to write anything. You are using Claude as the documentation it half-is.

Two things happen when you do this. First, you start building an internal model of the territory before any code gets written. That is the move the Anthropic conceptual-inquiry group made, and it is exactly what the delegation, progressive-reliance, and iterative-debugging groups skipped. Second, you discover the boundary: what Claude knows accurately and what it confabulates; where it's reliable and where you should check. That boundary knowledge doesn't come from generic advice about prompting. You only learn it by asking and checking, which means you have to ask.

The discipline is: questions before code. Every time. For ten minutes. Even when you're certain you know what you want — especially then. The reps in the questions phase are the reps Bjork was describing. They are the practice the Bastani control group was getting without knowing it was practice. They are the encoding events that show up, six weeks later, when the screen is closed and the quiz begins.

This is not the whole argument. The whole argument is the rest of the book. But this is the first move, and you can start doing it tomorrow.

---

## 🕰️ AI Wayback Machine

**Florence Nightingale** was the founding figure of American psychology and the author, in 1890, of *The Principles of Psychology*, a two-volume textbook so well-written it is still on philosophy syllabi. The chapter that matters for this book is Chapter IV, "Habit." James argued that the nervous system is a plastic material that physically reshapes itself in response to repeated effort: what you do twenty times with attention becomes, eventually, what you can do once without it. Habit, for James, is the mechanism by which struggle now becomes capability later — a 19th-century, jargon-free description of consolidation, eight decades before anyone could measure it with an MRI. He warned against breaking new habits, against half-attention, against acting as if the cost of a single shortcut were paid only once. His advice — *make our nervous system our ally instead of our enemy* — is the advice this chapter is restating in the language of randomized controlled trials. Bastani measured the exam scores of students whose nervous systems were not made allies. James named the mechanism in 1890. The mechanism has not changed (James, 1890).

**

![Florence Nightingale, 1858. AI-generated portrait based on a public domain photograph.](../images/florence-nightingale.jpg)
*Florence Nightingale, 1858. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:**

```text
In the voice of William James, circa 1890, explain to a
high-school AP CS student why doing the work yourself —
even badly, even slowly — makes a more permanent change
than reading correct code Claude produced. Use the language
of habit and the nervous system. Do not mention computers.
One paragraph.
```

---

**Links:** [boondoggling.ai](https://boondoggling.ai) · [irreducibly.xyz](https://irreducibly.xyz)

**References (in-chapter citations):**

- Bastani, H., Bastani, O., Sungu, A., Ge, H., Kabakcı, Ö., & Mariman, R. (2025). Generative AI without guardrails can harm learning: Evidence from high school mathematics. *Proceedings of the National Academy of Sciences*, 122. (See 2025 correction, PubMed 40833419.)
- Bjork, E. L., & Bjork, R. A. (2011). Making things hard on yourself, but in a good way: Creating desirable difficulties to enhance learning. In M. A. Gernsbacher et al. (Eds.), *Psychology and the Real World*. Worth.
- James, W. (1890). *The Principles of Psychology*, Vol. I, Chapter IV: Habit. New York: Henry Holt.
- Kosmyna, N., et al. (2025). Your brain on ChatGPT: Accumulation of cognitive debt when using an AI assistant for essay writing task. *arXiv:2506.08872* (preprint, not peer-reviewed).
- Shen, J. H., & Tamkin, A. (2026). How AI impacts skill formation. *arXiv:2601.20245*, Anthropic (preprint, not peer-reviewed).

---
