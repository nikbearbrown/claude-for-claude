# Chapter 2 — What You're Actually Good At (And What Claude Is Better At)

*Pattern recognition is Claude's domain. Supervisory intelligence is yours. Knowing which is which is the whole game.*

---

Here is something that happened to me on a Saturday afternoon.

I was building a grade-tracking tool — something small, for myself — and I needed a function that would take a list of student records and return the top three by GPA, breaking ties alphabetically by last name. I asked Claude. Eleven seconds later I had a clean little function with a tuple key: sort by GPA descending, then by last name ascending. I ran my tests. Three students, all different GPAs, came back in the right order. I was about to move on.

I don't know exactly why I looked at the test data one more time. Some itch. I added a row — two students tied at 3.92, last names *Bell* and *Adams*. I ran the tests again.

Bell came back first.

The spec said alphabetical by last name. Adams before Bell. Claude's sort hadn't done that. The tuple key was `(-gpa, last_name)`, which looks right, but Python's sort is stable, and on my original test set — all unique GPAs — the stability had been silently doing the work that the second key was *supposed* to do. The tie exposed it. The code had been correct for my test set and silently wrong for the real requirement, and the gap between those two things was invisible until I happened to add one more row.

I almost didn't add the row.

That near-miss is the whole chapter. Not because it's a hard bug — it's a sorting bug, the most embarrassing kind. The chapter is that gap: between *it compiled and passed my tests* and *it did what I actually needed*. Claude couldn't see the gap. The same statistical machinery that wrote the function was the only machinery available to audit it, and the audit said: looks good. The requirement that Bell-before-Adams was wrong lived in the spec I had written down somewhere else, in my head, nowhere in the prompt. Claude had no access to it.

Let's figure out what that means.

---

## Where Claude is Better Than You

I want to be honest about this first, because the rest of the chapter is going to be about what Claude isn't, and if I lead with the limits I'll sound like someone who thinks the internet is a fad.

Claude is better than you at pattern completion. That sentence is uncomfortable for a high school senior to write — or for anyone — but it is true in a measurable way, and pretending otherwise would make this book useless. Claude has read more code than you will read in your lifetime. It has seen the standard binary search written ten thousand times. Every common sorting algorithm, every React skeleton, every Flask boilerplate, every Python `argparse` setup — Claude has seen those patterns so many times that producing a clean version of any one of them is effortless in a way that is genuinely not effortless for you. A 2023 study by Brynjolfsson, Li, and Raymond tracked around five thousand customer-support agents and found that AI assistance raised average worker productivity by 14%, with the biggest gains — around 34% — going to novices on routine tasks (Brynjolfsson, Li, & Raymond, 2025). The gains are largest where the task is most pattern-like. That's the signature of something doing pattern work well.

What counts as pattern work? Boilerplate. Idiomatic translation — you wrote a thing in Python, you want it in TypeScript. Syntax recall — is it `==` or `===`, does the comma go inside or outside, what's the import path for `defaultdict`. Standard-library lookup: there are around two hundred modules in the Python standard library and you know maybe twenty of them well; Claude knows all of them. First-draft file structure for a new project. Summarizing test output. These are all, at root, the same thing: completing a known pattern from a large distribution of examples. Claude does all of them faster and, on average, more correctly than you do on a first pass.

Daniel Kahneman called the fast, associative, pattern-completing mode of human cognition *System 1* — the part of your brain that recognizes a friend's face across a crowded room before you've consciously decided to look. The defining property of System 1, the one Kahneman hammers on for hundreds of pages in *Thinking, Fast and Slow*, is that it cannot help itself (Kahneman, 2011). It completes the pattern whether or not the pattern is appropriate. Confidence is what System 1 *is for*; it doesn't have an uncertainty mode. Claude is not literally System 1 — Claude is a transformer running on a server somewhere — but Claude's outputs *arrive in your mind* as System 1 candidates. They are fluent, completed-feeling, plausible. They land before you've audited them. They pattern-match to correct, and your brain goes: yes, this, moving on.

The only defense against this is to route Claude's outputs, deliberately, into your own System 2 before you act on them. The whole chapter is about how.

| Claude does | Human does |
|---|---|
| Pattern completion across millions of training samples | Plausibility auditing — hearing the wrong note before tests catch it |
| Code generation from a specified prompt | Problem formulation — deciding what the build IS before Claude sees it |
| Syntax resolution and library-idiom translation | Interpretive judgment — deciding what an output means for *this* project |
| Test execution and mechanical iteration against output | Tool orchestration — which task, in what order, with what trust level |
| Searching a codebase for a documented pattern | Executive integration — holding the build's whole across a long session |

---

## Where You Are Better Than Claude

There is a class of work Claude is, currently and structurally, weak at. The word "currently" matters — I'll come back to it. The class goes under the name *supervisory intelligence*, and it breaks into five things I want to name here, not formally define. The formal definitions are Chapter 5. Right now the names are enough.

**Plausibility auditing.** When the code comes back, the question isn't *does it run* but *does the output match the world I'm building it for*. My GPA bug was a plausibility-auditing miss. The code ran. The output looked right. The mismatch was between Claude's output and the spec living in my head. Claude couldn't see the spec, so the implicit audit — the small *yes, this looks right* before it printed the function — had nothing real to check against.

**Problem formulation.** Before you ask Claude to solve, somebody has to decide what the problem actually is. This sounds trivial until you watch someone paste a graph problem with negative edge weights into Claude and get back a textbook Dijkstra implementation. Dijkstra is the dominant pattern in the training distribution for graph problems. Dijkstra is also wrong for negative weights. The formulation step — *this graph has negative edges, so Dijkstra fails* — sits upstream of anything Claude can do. If you don't do it, Claude won't flag it. It will produce confident output for whatever question you asked, which may not be the question you needed to ask.

**Interpretive judgment.** Claude returns a function. The function returns `0.7142857142857143`. Is that a probability? An accuracy? A ratio? A bug — should it have been an integer? Interpretation is the work of mapping an output back to the domain it came from. Claude can describe the number. Claude cannot tell you whether the number is *right for what you're doing*, because what you're doing is the part that lives outside Claude's window.

**Tool orchestration.** A real build is rarely one prompt to one model. It's a prompt to Claude Code, which runs a script, which writes a file, which gets committed to git, which triggers a test, which produces a log, which goes back to Claude. The question of *which tool, in which order, on which input* is currently yours. Claude can run a tool when you ask it to. Claude does not reliably decide which tool was the right one, and even when it does, it's choosing inside the menu you set up. If your menu is wrong, Claude's choice is wrong.

**Executive integration.** This one is the hardest to see because it doesn't sit at any one step — it sits across all of them. You are building a thing. At step seven you are debugging a subroutine. The question *does this subroutine still fit the design we sketched at step one* is not a question that step seven contains. Somebody has to hold the design in mind across the whole build. That somebody is you. Claude has a context window; you have a project. The context window forgets the design when it scrolls off. You, in principle, don't.

---

## Why This Division Isn't Arbitrary

Here is where I want to be careful, because this is the part that, if I overstate it, becomes the kind of confident wrong claim this book is supposed to teach you to spot.

Claude is structurally weak at supervising its own output because, in the most literal sense, the same weights that produced the output are the only weights available to do the audit. When Claude reads its own function and says *looks good*, that judgment is computed by the very statistical machine that just wrote the function. The audit is not an independent check. It is the same process, running again, with slightly different framing. If the production step was confidently wrong, the audit step will, on average, be confidently wrong in the same direction.

The empirical signature is consistent across the literature. Pearce and colleagues, in a paper usually cited as "Asleep at the Keyboard," generated 1,689 programs using GitHub Copilot across scenarios drawn from MITRE's list of the top twenty-five most dangerous software weaknesses. Roughly 40% of the generated programs contained the very vulnerability the scenario was designed to elicit (Pearce et al., 2022). The code compiled. The code passed basic tests. The code was exploitable. Copilot did not flag it, because Copilot wrote it, and "this is exploitable" is not a pattern that overrides "this completes the user's request" in Copilot's weights.

Liu and colleagues' 2024 hallucination taxonomy identified two failure modes that turn out to be the same architectural fact in different clothes: *task-requirement-conflicting* outputs, where the function does something close to but not exactly what was asked, and *knowledge-conflicting* outputs, where the function references a library or API signature that doesn't exist (Liu et al., 2024). In both cases, the model cannot detect the conflict because detection requires checking against something the model doesn't have — your actual requirement, or the actual API of the actual library.

The package-hallucination version is particularly clean. A meaningful fraction of packages imported in Claude-generated code — different studies put it at roughly one in five for some scenarios — don't exist on PyPI or npm. Claude is not lying. It has correctly identified that a package *of approximately that shape and name* would solve the problem, and produced the import statement that would import it if the package existed. The audit step — *does this package exist* — cannot be performed without leaving Claude's window, and Claude has no reliable mechanism for leaving its window unprompted.

The careful version of this claim: *currently*, a model whose architecture is statistical completion cannot reliably audit its own output, because audit and production are the same operation. This is not a metaphysical claim about machines forever. It is an empirical claim about 2026. Retrieval augmentation might shrink part of the gap. Better tool use might shrink more. The contested question — and you should know it is genuinely contested — is whether all five supervisory capacities eventually reduce to better tooling, or whether some of them (problem formulation and executive integration look most stubborn) are structurally outside what statistical completion can do. The honest answer is that nobody knows. This chapter is teaching you the division of labor as it stands today, because that division determines whether your function ships with a GPA bug in it.

---

## The Asymmetry That Matters

There is a corollary worth drawing out, because it sets the shape of the whole student-Claude relationship.

Claude's solve speed is rising fast. It has been rising fast for three years and there is no public evidence the rise is slowing. Every model generation gets faster, gets more accurate on standard benchmarks, handles more context. Your verification speed — how quickly you can check whether the candidate solution is correct against the domain you're working in — is approximately constant. You can train it; a year of serious programming work measurably improves your verification on the code you write. But it does not improve at the rate Claude's solve speed improves, and it cannot, because verification is bottlenecked on a human cognitive process Claude is not subject to.

![A line chart over time. Claude's solve speed rises steeply; human verification capacity stays roughly flat. The gap between the two trajectories widens.](images/02-division-of-labor-fig-01.png)
*Figure 2.1 — The solve-verify asymmetry*

Two lines. One rising. One roughly flat. The gap between them is widening.

The temptation, when you see those two lines, is to try to close the gap by delegating the verification too — to use Claude to check Claude. The architectural argument is a direct argument against this. Audit by Claude has the same structural limitation as production by Claude. You can run a second pass; you can ask a second model; you cannot get an independent check by handing the check to the same kind of system that did the work. The verification has to land on a process that has access to what Claude doesn't see — your spec, your test data, your actual graph with its actual edge weights, the lab machine with its fixed Python install. That process is you.

So here is the version worth memorizing: **your job is not to solve faster. Your job is to verify better.** Claude has the solve side. You have the verify side. If you compete on the solve side you lose, and you should — the same way you should lose at long division to a calculator. If you abandon the verify side, the build ships with whatever Claude got wrong, and you won't know until something in the world tells you.

---

## The Dangerous Middle

I have been circling a third category for the whole chapter. Let me name it now.

*Claude work* — pure pattern completion — is honest and safe. You ask, it answers, the stakes are low. *Human work* — explicit supervisory judgment — you'll usually catch yourself doing, because it feels like work. *The dangerous middle* is the category of tasks that **look** like Claude work and **are** actually human work in disguise.

The sorting function I opened with was a dangerous-middle task. It looked like boilerplate. It presented as the kind of thing you delegate without thinking. On inspection, it required a plausibility audit against a spec that lived only in my head.

The SQL-injection vulnerabilities in the Pearce paper are dangerous-middle tasks. They look like *build me a string and run it as a query* — that is a pattern Claude completes in its sleep. The correct completion requires that somebody know prepared statements exist, that the codebase already uses them, that string-concatenated SQL is the canonical example of *the pattern Claude completes confidently and you must override*. Roughly 40% of Copilot's completions in those scenarios were vulnerable (Pearce et al., 2022). Not because Copilot is bad. Because the surface task is pattern work and the actual task is judgment.

The Dijkstra-on-negative-weights failure is a dangerous-middle task. The student pasted a graph problem; Claude returned the dominant graph-problem pattern; the supervisory move — problem formulation — required recognizing the structure of the actual problem before delegating it.

The hallucinated library is a dangerous-middle task. Claude imported `from quickstats import bootstrap`; no such package exists on PyPI; the code failed on the lab machine; the supervisory move — executive integration — required keeping the ground-truth environment in mind across the whole build, including the boring fact that the lab machine has a fixed Python install.

Notice the shape. In every case the task *presents* as pattern work: write a sort, build a query, pick an algorithm, import a library. Each one, on inspection, requires a supervisory capacity that Claude cannot supply because the input Claude would need lives outside Claude's window. The dangerous middle is not a fixed list of bug categories. It is the entire region where surface-pattern fluency overrides the need for judgment. It is most of the bugs you will ever ship.

The countermove is easier to name than to do. Before accepting a Claude output, name the supervisory capacity the step requires. Out loud, in your head, in a comment — anywhere. If you can name it, you'll exercise it. If you can't name it, you're in the middle and you don't see the middle.

Bondi-Kelly and colleagues, in a 2,784-participant study published in *Harvard Data Science Review* (2025), found that the dominant predictor of whether someone accepted a wrong AI suggestion was not domain knowledge and not task difficulty. It was attitude toward the tool. People favorable to automation accepted incorrect suggestions at significantly higher rates than skeptics. The result is uncomfortable. It means that how you feel about Claude *before you start* changes whether you'll catch its mistakes. The chapter can't make you a skeptic. It can ask you to be one in this specific way: when a task feels like pattern work, raise the suspicion that it might be middle work in disguise.

---

## The Same Function, Run Twice

**Run one.** I open Claude, paste the spec, accept the output. The function compiles. I run it on my test set — three students, all different GPAs, the right three come back in the right order. I move on. Two days later, with real data containing a tie, the function silently returns the wrong order. The bug propagates through everything downstream. I find it because a friend tells me her sister's name is in the wrong spot on a leaderboard.

**Run two.** Before I paste anything, I do *problem formulation*: the spec explicitly mentions ties, which means my test set needs a tie or I'm not testing the spec. I add one. I paste the spec. Claude returns the tuple-key sort. I do *plausibility auditing*: I look at the key — `(-gpa, last_name)` — and ask whether it handles the tie case correctly. I run it on the new test set. Bell comes back before Adams. Wrong. I do *interpretive judgment*: I read the output as a domain result — which student is listed first — not just as a syntax-correct return value, and I see the mismatch. I do *tool orchestration*: instead of asking Claude to "fix it," I describe the specific failure — *Bell came back before Adams; spec requires Adams first on a GPA tie* — and ask for a corrected key. I run it again. The tie comes out right. I do *executive integration*: I add the tie case to my permanent test file, so future versions of this function will be checked against the same case.

Same Claude. Same starting output. Five small moves and the bug doesn't ship.

The difference between the two runs is not effort. Run two cost me maybe four extra minutes. The difference is that run two contained five small acts of supervision and run one contained zero. Each act is small enough to feel trivial. Each one is a habit. Once they're habits, the four-minute overhead becomes the way you work — and the silent-failure path becomes the thing that happens to other people.

The model is fine. The supervision is missing.

---

## 🕰️ AI Wayback Machine

**John Dewey** (1856–1915) is the person who first tried to write down, on paper, which cognitive work belongs to the human and which to the system. *The Principles of Scientific Management*, published in 1911, contains the line that gives this chapter its bones: "the work of every workman is fully planned out by the management at least one day in advance" (Taylor, 1911). Taylor's move was to separate *planning* — judgment, formulation, evaluation — from *execution* — repeatable procedural pattern — and to study each separately. It is the same move this chapter performs, transposed: separate pattern work from supervisory work, and analyze each.

I want to be honest about Taylor, because he is famous and he is controversial and the controversy is earned. The version of scientific management Taylor implemented in steel mills stripped craft workers of judgment, handed it to a managerial class, and produced both real productivity gains and real dehumanization. The historical record on his selective data and on the human cost of his system is not flattering. This chapter borrows Taylor's *question* — which cognitive work belongs where — while rejecting his answer that the worker's judgment is residue. In this book, supervisory intelligence is not what's left over after the machine takes the good parts. It is the load-bearing capacity. The student, not Claude, is the engineer in Taylor's sense; Claude is the part Taylor would have studied for time and motion.

**

![John Dewey, 1930s. AI-generated portrait based on a public domain photograph.](../images/john-dewey.jpg)
*John Dewey, 1930s. AI-generated portrait based on a public domain photograph (Wikimedia Commons).*

Run this:** *"You are John Dewey in 1911, writing a memo to a 2026 high school student building a project with Claude Code. Using the planning-versus-execution framework from* The Principles of Scientific Management, *but updating it for a worker whose 'machine' is a large language model, write a one-page memo telling the student which work belongs to her and which belongs to Claude. Be specific. Then, in a second page, critique the memo from the perspective of a 2026 reader who has read the historical record on the human cost of Taylorism. What does this division of labor get right? What does it dehumanize?"*

---

## Bridge

The reader can name the five capacities. Chapter 3 explains why school isn't teaching them — and why the student is on their own.

---
