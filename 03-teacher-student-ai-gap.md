# Chapter 3 — The Teacher-Student AI Gap: Why You're On Your Own

My AP Chemistry teacher does not know what Claude Code is.

I mean the literal sentence. In May 2026, the teacher of record for the AP class I am sitting in does not know that the tool the kid in the back row is using to write a lab report is an agent that opens a terminal, runs scripts, edits files, and pings the internet between answers. She thinks it is, broadly, a chatbot. She thinks "AI" is one thing. She has heard about ChatGPT and the school has a policy that says don't use it on graded work. She does not know that the kid in the back row is running a multi-file build inside the agent during her lecture, asking it questions about the rate law she is writing on the board.

I am not bragging about this. I am describing the configuration, because the configuration is the danger.

---

In February 2025, the RAND Corporation published results from a panel survey of U.S. K–12 teachers and principals. Generative-AI use among teachers had roughly doubled in a year — from about 25% in 2023–24 to about 53% in 2024–25. That sounds like catch-up. But the same RAND brief found that only about 35% of district leaders reported providing students with any training on AI use, and only about 34% of teachers said their district had an academic-integrity policy that specifically addressed AI. Pew's January 2025 survey of about 1,400 U.S. teens found the share using ChatGPT for schoolwork had doubled from 13% to 26% in a year, with 11th and 12th graders at 31%. And Pew was only asking about one tool — ChatGPT — and only capturing self-reported use, which underestimates everything. The number that includes Claude and Gemini and Copilot and the agentic tools nobody is even asking about yet is higher. Considerably higher.

Here is what those numbers add up to. Your teachers are, on average, behind you on the tools. The curriculum is three years behind the tools. The school's policy is either nonexistent or "don't." You are not going to be rescued by the institution. The institution has not caught up.

That is the bad news. The worse news is this: being ahead of your teacher on the tool is exactly the configuration in which AI is most dangerous to you. Not least dangerous. Most.

---

There is a distinction I need to draw carefully, because the whole chapter turns on it.

**Technical fluency** is the ability to operate the tool. It looks like this: you know which model to use for which task, you know how to write a system prompt, you know what a context window is, you know how to recognize when Claude is confabulating in a domain you already understand, you know how to escalate from a single prompt to a multi-turn agentic build. This fluency is real and it is valuable and you genuinely have it. More of it than your teacher does, probably.

**Domain depth** is the ability to evaluate the output against the world. It looks like this: in chemistry, you know that adding an inert gas at constant volume does not shift an equilibrium because the partial pressures of the reactive species are unchanged, and the inert gas does not appear in the equilibrium expression at all. In history, you can tell when a citation exists and when a plausible-sounding citation has been invented. In Python, you know that `dict.fromkeys(list, [])` shares the *same* list object across every key — the mutable-default trap — and that the fix requires a dict comprehension. Depth is the ability to read a confident output and say: *that one specific clause is wrong.*

These are different skills. Fluency transfers across domains because the interface is one interface. Depth does not transfer at all. Depth in chemistry gives you nothing in history. Depth nowhere gives you depth everywhere. It is built one subject at a time, one cycle of confused-then-corrected at a time, and the only way to get it is the slow way.

Now here is the problem stated plainly. Claude is technically fluent in every domain you will ever ask it about. You have depth in a few. The gap between those two things — the space where Claude sounds authoritative and you cannot tell whether it is right — is where the harm lives. The kid running Claude during AP Chem has more fluency with the tool than the teacher. He has less depth in the subject than the teacher. He receives output and cannot evaluate it. The teacher could evaluate it and is not in the loop. That configuration has a name in the history of technology, and we will get to it, but first I want to show you what it looks like from the inside.

---

Last semester I was studying for a unit test on chemical equilibrium. Le Chatelier's principle — the principle that an equilibrium shifts to oppose a change — was going to be on it, and I had one specific case I was fuzzy on. I asked Claude.

The question: what happens to a gas-phase equilibrium when you add an inert gas at constant volume? The system was the standard one — nitrogen plus hydrogen yielding ammonia. I already knew the shape of the right answer. Nothing happens, because the partial pressures of the reactive gases are unchanged. I wanted to see the explanation written cleanly to lock in my reasoning.

Claude wrote a fluent paragraph. The first half was correct: partial pressures of the reactive gases unchanged, so the equilibrium constant is unchanged, so no shift. The second half explained the related problem — inert gas added at constant *pressure* — where volume increases and the reactive partial pressures do change, which does shift the equilibrium. Also correct. And somewhere in the middle, a sentence appeared that explained the constant-pressure case using the language of the constant-volume case, in a way that, on a careless read, made the two situations sound like the same thing for the same reason. The sentence was not, by itself, false. It was a true sentence about a real mechanism. It was sitting in the wrong paragraph.

I did not catch it. I was reading at midnight before a test, scanning for shape rather than auditing clause by clause. The shape matched what I expected. I nodded and closed the tab.

On the test, a free-response question asked about adding argon to the ammonia system at constant volume. I wrote the right answer — no shift — and then, because the misplaced sentence had consolidated overnight into how I *remembered* the topic, I wrote a justification that smeared the two cases together. I wrote that the equilibrium does not shift because the volume does not change and therefore the partial pressures do not change, which is true but circular, and which missed the load-bearing point: the inert gas does not appear in the equilibrium expression. I lost most of the points on the explanation.

What I want to notice about that is not that I got the answer wrong. I want to notice that the error did not feel like an error while I was making it. It felt like writing a confident answer on a topic I had studied. The studying had been reading Claude's fluent paragraph and nodding. The nodding had encoded the misplaced sentence into my memory as if it were the subject. The test caught it. Nothing before the test did. My teacher, if I had shown her the paragraph, would have caught it immediately — she has depth. I had just enough depth to think I could audit Claude's explanation and not quite enough to actually do it.

That is the most dangerous part of the gap. Not the case where you know nothing and trust everything. The case where you know just enough to feel confident, and the error is exactly one level deeper than your fluency.

---

The chemistry example is mine. The one from code is documented and precise.

Spracklen and colleagues, at USENIX Security 2025, tested sixteen code-generating models across 2.23 million code samples and found that 19.7% of those samples referenced packages that do not exist. Non-existent imports. Fictional libraries. Plausibly named modules that were never published. Across commercial GPT-class models, the rate ran as high as 24%. The hallucinated packages had names like `pandas-extras`, `quickstats`, `numpy-utils` — names that *should* exist, names that *could* exist, names a working developer might half-remember from somewhere. They just don't.

The follow-on harm is the part to hold onto. In 2024, a security researcher uploaded a real dummy package to PyPI matching a name LLMs had been hallucinating. Within weeks it received a large number of downloads. Students running `pip install` did not know that the package they were installing existed only because someone had taken the model's wrong answer and made it true after the fact. The import worked. The download worked. The check that would have caught the error — *this package didn't exist last week; why does Claude reference it?* — required depth in the Python packaging ecosystem that most students do not have. Surface plausibility passed. The audit that goes beneath the surface never ran.

That is the precise version of the hallucination problem, and it is different from the loose version most people carry around. The loose version: *Claude makes things up sometimes*. True and almost useless. You already know it. You have a story about catching Claude in a lie, and the story makes you feel appropriately skeptical. But the story is about a domain where you had enough depth to catch the lie. The dangerous case is the one where you don't, and you don't know you don't, and the output passes every check you are capable of running because your checks are checks for surface plausibility, which is exactly what Claude is engineered to produce.

Claude is not failing when it does this. It is doing what it is built to do: producing the most statistically plausible next token given the prompt. In domains where you have depth, plausible usually matches true. In domains where you don't, plausible looks identical to true, and you have no instrument to measure the distance between them.

---

Nicholas — who is working on the creative side of this book — has been making the same observation about writing and art for about a year, and I kept half-ignoring it until I realized it was structurally identical to the package-hallucination problem.

His version: a piece of work made through unsupervised AI delegation has a specific quality. It is competent. It is grammatical. It hits the rubric. By the metric of *does this look like the thing it is supposed to look like*, it passes. And it is almost always, underneath the competence, *empty* — no aesthetic stance, no point of view, no decision that someone made because they thought one option was more interesting than another. The average of a million similar pieces, smoothed into a presentable middle.

Nicholas calls the missing thing soul, and I am not going to argue with the word, because the people who find it imprecise are usually the same people who can't tell the difference. In operational language, the missing thing is *the trace of a human choosing*. The comma you kept against the rule. The brushstroke you left because you liked it more than the corrected one. The sentence structure that is technically awkward and exactly right. Those small load-bearing decisions are what distinguish a piece somebody *made* from a piece somebody *received*.

The reason this belongs in the same chapter as the chemistry error and the hallucinated import is that they are all the same failure, one level deeper than the surface. The polished essay with no aesthetic stance passes the rubric the same way the fluent code with the hallucinated import passes the test runner. Both are competent on the surface and empty underneath. In both cases, the student is the only person with the standing to notice — the teacher is reading two hundred of these a week and the bar for "is this fine" has quietly moved down. The failure mode is not that the AI is bad. The failure mode is that the human stopped showing up to the specific work that makes a piece somebody's, and the piece is now nobody's, and nobody in the room can tell from the outside.

---

There is a sermon adjacent to this chapter that I want to get out of the way, because it is wrong.

The sermon: *Therefore, use less AI. Put the tool down. Do the work by hand the way it was done before, rebuild your depth, and the gap closes.* That is not the move. The kid who tries to compete on unassisted output against a roomful of agent-augmented peers loses on the homework, and losing on the homework is real, and I am not asking you to do it. The research from Chapter 1 is in the room. The tool is too good to abandon on principle. That is not the answer.

The answer is: build the supervisory capacity that makes AI use safe. Supervision rests on depth, and depth is built by doing the work the slow way enough times that you have a feel for when something is off. You cannot supervise Claude's chemistry answer if you have not been specifically wrong in chemistry on your own paper and then specifically corrected. You cannot supervise its code if you have never had an error message on your own screen and sat with it until you understood it. The encoding event is you, attempting, wrongly, correcting — and there is no way to produce that event without spending the calories it costs.

This is not a moral claim. It is a working claim. The student who has the depth catches the hallucination. The student who doesn't, doesn't. The answer is not less AI. The answer is AI plus the discipline of building depth alongside it — choosing, deliberately, which domains you will encode the slow way and which you will run on Claude's fluency with your eyes open about the trade-off.

The chemistry student who is going to be a chemist needs depth in chemistry. She can run Claude on the English essay. The writer who is going to be a writer needs depth in writing. He can run Claude on the chemistry homework. Neither gets to have depth in everything. Neither gets to skip having depth in something. The choice is real and it is yours, and the point of this chapter is to ask you to make it on purpose rather than letting Claude make it for you by default.

---

There is a way to know operationally whether you have depth in a subject, and it does not require a philosophy of knowledge.

You have depth when you can read a Claude output in that domain and feel, without rereading, that one specific clause is off. You don't need to know immediately what's wrong. You just need to feel the *off*. That sense — the unease before the explanation — is the operational signature of encoded understanding. It is what I did not have the night before the equilibrium test. It is what my teacher would have had reading the same paragraph.

If you read Claude output in a subject and feel only fluent agreement, you do not have depth there yet. That is not a judgment. It is a diagnostic. The domains that feel like fluent agreement are the domains where the gap is widest and where the discipline has to be tightest: more verification, slower reading, independent sources, specific claims checked rather than general shape accepted. The depth will come, but it comes only from the slow-way work, and right now you are not there, and knowing you are not there is the start of something.

The gap between the student's technical fluency and the teacher's domain depth is real, and nobody in the institution is currently responsible for closing it. The tool is too new, the teachers are too far behind, the policies don't exist yet. That means the closing is yours to do. The rest of this book is about how.

![A two-axis editorial quadrant. Horizontal axis is technical fluency from low to high. Vertical axis is domain depth from low to high. Teacher sits in the low-fluency high-depth quadrant. Student sits in the high-fluency low-depth quadrant — the danger zone — marked in red.](images/03-teacher-student-ai-gap-fig-01.png)
*Figure 3.1 — Where the danger lives*

---

## 🕰️ AI Wayback Machine

**Lev Vygotsky** (1926–2002) was an Austrian-Mexican-American philosopher who spent the second half of his life arguing that modern institutions — schools, hospitals, roads, professions — cross a threshold past which they undermine the very purpose they were built to serve. Past that threshold, a tool produces what Illich called **counter-productivity**: roads that create traffic, medicine that produces iatrogenic sickness, schooling that disables self-directed learning. The argument is in *Tools for Conviviality* (1973), and the mechanism is precise: a *convivial* tool — one a person picks up to express their own intention — turns *manipulative* once it is scaled past the user's ability to direct it. The student running an agent more capable than her teacher, in a domain deeper than her own depth, is sitting exactly at Illich's threshold. He did not see Claude. He saw the shape of Claude fifty-three years before it arrived.

**

![Lev Vygotsky, 1920s. AI-generated portrait based on a public domain photograph.](../images/lev-vygotsky.jpg)
*Lev Vygotsky, 1920s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:**

```text
You are Lev Vygotsky, writing in 1973, with a footnote indicating
you have somehow seen one transcript of a 2026 American high school
student building a chemistry lab report with Claude Code.

Using the framework of Tools for Conviviality — in particular the
concept of counter-productivity and the threshold past which a tool
outpaces its user — write a one-page diagnosis of where this student
stands relative to the threshold.

Then, in a second page, give the student a single concrete piece of
advice on what would have to be true for the tool to remain convivial
in her hands. Be specific about the practice, not the principle.
```

---

**Links:** [boondoggling.ai](https://boondoggling.ai) · [irreducibly.xyz](https://irreducibly.xyz)

---
