# Chapter 10 — Prompts for Teaching and Learning

**Capability built:** Design prompts that preserve cognitive labor and support learning.

---

## Opening Scene

A graduate student studying for a qualifying exam types into Claude: "Explain the difference between classical and Bayesian inference." Claude delivers four paragraphs. The student reads them, feels like she understands, and closes the tab. Three days later, on the exam, the question appears. She cannot answer it.

This is not Claude's failure. The explanation was accurate. The failure was in what the prompt asked for — an explanation delivered to a passive reader — rather than what learning requires, which is active construction. Ambrose et al. in *How Learning Works* describe the distinction clearly: students who receive information do not automatically encode it in a way that makes it retrievable and applicable. Retrieval, application, and correction of misconceptions require active work.

The student's prompt was a request for execution. What learning requires is a prompt designed to make the student do the cognitive labor.

This chapter is about the difference between prompts that hand over answers and prompts that build understanding. It is aimed at both learners who use Claude to study and educators who design Claude-supported learning activities.

---

## What This Chapter Lets You Do

After this chapter you will be able to:

- Write a tutoring prompt that requires you to produce an attempt before Claude responds
- Design a Socratic prompt that stages questions rather than delivering an explanation
- Build a misconception-check prompt that surfaces wrong beliefs and addresses them specifically
- Prompt Claude to give feedback on your reasoning rather than on your product
- Create a self-assessment loop that asks you to evaluate before Claude does

The underlying design principle in each case is the same: cognitive labor is the mechanism of learning, and the prompt must protect that labor rather than bypass it.

---

## Core Concepts

**The answer-first trap.** Claude's default, when asked a learning question, is to answer it. That is appropriate when the goal is information retrieval. It is counterproductive when the goal is skill development or durable understanding. A prompt that asks "what is the difference between X and Y?" will produce a comparison. Whether the student learns from that comparison depends almost entirely on what they do with it — and the prompt as typically written does not require them to do anything.

**Active-constructive-interactive learning (Chi).** Chi's framework distinguishes passive behaviors (receiving information), active behaviors (doing something with material, such as highlighting or summarizing), constructive behaviors (producing new knowledge, such as explaining in your own words), and interactive behaviors (working with another agent). Tutoring prompts should target constructive and interactive behaviors — prompts that require the learner to produce, not just consume.

**Staged hints.** In effective tutoring, hints are not given all at once. They are staged: the first hint narrows the problem space; the second offers a strategy; the third offers a closer suggestion; the final step provides the answer only if prior hints have not been enough. Bloom's tutoring literature supports staged support over direct disclosure (Bloom, tutoring research). A prompt can specify this structure explicitly.

**Misconception diagnosis.** Hattie and Timperley's work on feedback shows that the most effective feedback is specific to an error — it identifies the precise thing the student got wrong and explains why. Generic praise and generic correction are among the least effective forms of feedback. A tutoring prompt must ask Claude to diagnose specifically, not generically.

**The Anthropic Learning Mode** offers a tutoring mode that asks students questions rather than delivering answers. [verify — current as of writing] The design principle behind this feature is the same principle this chapter teaches: structure the interaction so the learner must produce first.

**Cognitive apprenticeship.** Cognitive apprenticeship involves modeling, coaching, scaffolding, and fading. A prompt sequence that begins with Claude demonstrating a worked example (modeling), then asks the student to try a similar one with feedback available (coaching), then removes the scaffolding by asking the student to work without prompting (fading), follows this structure. The difference from standard tutoring is explicit attention to reducing support over time as competence grows.

---

## Before / After Prompt Walkthrough

### The Before Prompt (what most learners send)

> Explain the difference between null hypothesis significance testing and Bayesian inference. Include examples.

This is a reasonable question. Claude will produce a thorough answer. The learner will read it. Whether they learn depends on what they do next — and the prompt requires them to do nothing.

### The After Prompt (a tutoring work order)

> I am studying for an exam on research methods. I have some understanding of null hypothesis significance testing (NHST) but am confused about how Bayesian inference differs.
>
> Before you explain anything, ask me to describe what I think the main difference is in my own words. After I respond, tell me what I got right and what I got wrong, specifically — not generally. Then ask me a follow-up question that tests the part I got wrong.
>
> Do not give me the full explanation first. Start with the question to me.
>
> If I am stuck after two attempts, give me a single staged hint — not the answer — and ask me to try again with the hint.

What changed:

1. The prompt requires the learner to produce before Claude responds.
2. It specifies feedback as specific rather than generic ("what I got right and wrong, specifically").
3. It stages the hint structure — hints before full disclosure, not full disclosure first.
4. It instructs Claude not to skip straight to the explanation.

The output from this prompt is a conversation in which the learner must do cognitive work at every step. The output from the first prompt is a text the learner may or may not engage with.

---

## The Human Gate: What You Must Verify

When using Claude for learning, the human gate is not just accuracy — it is whether you have actually understood.

**Verify by closing the tab and explaining.** After any Claude-assisted study session, close the conversation and explain the concept to yourself or someone else without looking at Claude's output. If you cannot, you have not yet learned it. Claude's explanation was fluent; fluency is not understanding.

**Verify misconception diagnoses against an authoritative source.** Claude will identify misconceptions, but its diagnosis may itself be wrong. If Claude tells you that your understanding of concept X is mistaken, verify against a textbook, a course instructor, or a primary source before revising your mental model.

**Verify staged hints are actually staged.** If you write a hint-structured prompt but Claude gives you the answer in the first hint, the prompt has not worked. Revise it: add an explicit instruction — "do not give me more than one piece of new information per hint; do not reveal the answer until I have made at least two attempts."

**Academic integrity.** The U.S. Department of Education AI report and UNESCO generative AI guidance both note that institutions are developing varying policies on AI use in assessed work. Before using Claude to support learning in a course context, confirm what use is permitted and disclose AI assistance in accordance with your institution's or instructor's requirements.

---

## Common Mistakes

**Asking for explanations instead of asking for questions.** "Explain X" is a passive trigger. "Ask me what I know about X" is an active one. The first delivers an artifact; the second starts a learning process.

**Treating fluent output as evidence of understanding.** Claude's explanations sound right because Claude has been trained on texts that sound right. Sounding right is not a test of whether you understood. Test yourself.

**Not specifying that feedback should be specific.** Generic feedback prompts get generic feedback: "Good effort, but consider X more carefully." Specific feedback prompts — "Tell me precisely which part of my explanation is incorrect and why" — get feedback you can use.

**Asking Claude to generate practice problems without a difficulty ramp.** A set of ten practice problems at uniform difficulty does not build skill the way a set that starts easy and increases in difficulty does. Specify the ramp: "Start with one problem I can probably solve, then one that requires me to extend what I know, then one that requires something I have not seen before."

**Using Claude to avoid hard thinking.** This is not a moral claim. It is a practical one. If you use Claude to skip the cognitive effort that encodes understanding, you will not remember or be able to apply the material when Claude is not in front of you. Bloom's tutoring research is consistent on this point: tutoring works when the student is actively engaged; passive reception does not close the gap.

---

## Try This

**Exercise 1 — The attempt-first prompt.** Choose a concept you are currently studying that you think you partly understand. Write a prompt that: (a) tells Claude what you think you know; (b) asks Claude to find the gap or error in your explanation before offering any correction; (c) instructs Claude to ask a follow-up question rather than immediately explain. Run the prompt and take notes on what Claude identifies. Then verify Claude's diagnosis against a second source.

**Exercise 2 — Build a misconception prompt.** For a concept in your field, write a prompt that asks Claude to list the three most common misconceptions students have when learning this concept. Then write a second prompt: "I am going to tell you what I believe about [concept]. Tell me which misconception from your list, if any, my explanation reflects." Use this as a pre-check before studying to surface your prior wrong beliefs.

**Exercise 3 — Design a tutoring sequence.** For an instructor or self-directed learner: write a three-step prompt sequence that follows the cognitive apprenticeship model — (1) a worked example prompt that asks Claude to show one complete worked problem with reasoning visible at each step; (2) a coaching prompt that gives the learner a similar problem and asks Claude to give feedback on their attempt rather than the answer; (3) a fading prompt that presents a harder problem and instructs Claude to give no hints unless explicitly asked. Run the sequence and evaluate whether each step required more independent work than the last.

---

## What Would Change My Mind

This chapter is built on the premise that bypassing cognitive effort undermines learning, and that prompts should therefore force productive effort. That premise is well-supported in learning science (Ambrose et al.; Chi; Hattie and Timperley; Bloom). But there are limits. For learners who are so stuck they cannot make an attempt, requiring an attempt first may produce frustration without learning. For highly routine procedural tasks where the goal is speed rather than deep understanding, explanation-first may be appropriate.

If research specific to AI-assisted learning showed that explanation-first prompts produced durable learning gains comparable to attempt-first prompts — not just self-reported satisfaction — the sequencing advice would need revision. The current evidence from classroom tutoring is clear that active engagement beats passive exposure. Whether AI tutoring inherits this finding cleanly is an open empirical question.

---

## Still Puzzling

The tension between AI-supported learning and academic integrity is genuinely difficult. The same prompt design that helps a learner understand material more deeply could, if used differently, complete an assignment the institution expected the student to complete alone. This chapter takes no position on policy. But it notes that designing prompts that require the learner to produce first — rather than prompts that produce for the learner — is at least structurally aligned with learning integrity, regardless of institutional policy.

There is also a question about different subject domains. The tutoring prompts in this chapter were designed with humanities and social science in mind. For mathematics, the structure works but the mechanics differ: worked examples, specific error identification in calculations, and symbolic notation all require explicit handling in the prompt. For code, the structure works but "explain before showing" becomes "review my attempt before correcting it." Domain-specific prompt examples are a gap in this chapter.

---

## Bridge

Learning prompts protect cognitive labor by structuring what the learner must do before Claude responds. Handoff prompts — the subject of the next chapter — protect a different kind of labor: the judgment that defines what an autonomous agent is allowed to do on your behalf. Both share the same discipline: a vague prompt produces an output you cannot supervise. A specified prompt produces an output you can check, correct, and reuse.

---

## Sources Used

- Anthropic, Claude for Education. https://www.anthropic.com/news/introducing-claude-for-education
- Anthropic Learning Mode materials
- Ambrose, S. A. et al., *How Learning Works*
- Chi, M. T. H., active-constructive-interactive learning framework
- Hattie, J. and Timperley, H., feedback research
- Bloom, B. S., tutoring literature
- Cognitive apprenticeship literature
- U.S. Department of Education, AI report
- UNESCO, generative AI guidance
- Academic integrity guidance
