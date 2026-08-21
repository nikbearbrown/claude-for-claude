# Chapter 4 — Claude Code as Agentic Engineering
*When the agent is inside the repository, the supervision problem is different in kind, not just in degree.*

Here is the scenario that should make you pause. A developer pastes a stack trace into a chat window, asks for a fix, copies the suggested code into the affected file, and the error stops appearing in the log. Two days later, an edge case that the fix silently broke reaches a user.

This is not an AI failure. The system produced exactly what it was designed to produce: a plausible artifact. The failure is in the workflow. The developer skipped the step where the fix runs against the actual codebase, tests against the failing case and several adjacent ones, and diffs against the original to reveal exactly what changed. Plausible is not tested. Plausible is not safe.

The reason that scenario matters in this chapter is that Claude Code changes the workflow by moving the agent inside the repository. The chat interface was on the outside: it handed you text and you decided what to do with it. Claude Code is on the inside: it can read your actual files, run your actual tests, see the actual failure, and produce an actual diff. That is a genuine capability gain. It also means the agent is operating on a system that matters, with tools that can change it. The supervision requirements go up, not just sideways.

---

## What Claude Code Actually Is

Claude Code is a command-line AI agent that operates inside a development environment. This is the first thing to be clear about, because the word "AI" in "AI coding tool" spans an enormous range. At one end is an autocomplete that finishes a line of code based on the few lines above it. At the other end is an agent that reads a codebase, forms a plan, edits multiple files, runs tests, observes the result, revises its approach, and reports. Claude Code is at the second end.

Concretely, it can read files across a repository, edit multiple files in a single task, run shell commands — tests, builds, linters, scripts — and report what it changed and why. The important word in that list is "run." This is not text generation with extra steps. When the agent runs a command, the command executes. When it edits a file, the file changes.

The architecture underneath this is the ReAct loop described in Chapter 2: reasoning and action interleave (Yao et al., 2023). Each observation updates the plan; each action produces new observations. An agent investigating a bug may read one file, infer it needs to read another, run a reproduction command to see the failure, update its hypothesis, and edit only after several inspection rounds. This is how a careful engineer works, which is part of what makes the architecture appealing. It is also why the supervision model cannot be "let it run and read the report" — by the time you read the report, the actions have already happened.

<!-- → [DIAGRAM: ReAct loop diagram showing the observe → plan → act → observe cycle specific to Claude Code — observations are file reads and test output, actions are file edits and shell commands, the loop continues until the agent reports; caption: the loop closes before the human sees the result unless checkpoints are built in] -->

---

## The Action Surface for Engineering Work

Chapter 1 introduced the see/decide/do framework as a way to characterize what an AI system is actually capable of. For a coding agent, that framework produces a more specific picture than it does for a general assistant.

What can it see? Every file in the repository scope you define, the output of any command it runs, and any context you provide in the task description. If secrets, API keys, or database passwords live in files within that scope, the agent can see them.

What can it decide? Which files to read in what order, what the bug hypothesis is, which approach to take, what to edit, what commands to run to verify. The agent forms a plan and executes it, adjusting as observations come back. You do not see those intermediate decisions unless you ask for a plan before execution begins.

What can it do? This is the list that requires the most careful thought.

| Agent action | Risk level | Required gate |
|---|---|---|
| Read files | Privacy and secrets exposure | Scope to repository or folder; exclude secrets |
| Edit files | Behavioral change in a working system | Diff review before merge |
| Run tests | Low–moderate | Command approval if new to environment |
| Install packages | Supply-chain risk | Human approval |
| Access credentials | High | Avoid; use governed secrets management |
| Deploy to production | Very high | Human-only or organizational gate |
| Delete files or databases | Very high | Human-only; prefer copies and staging |

The stable principle from Saltzer and Schroeder (1975) applies here exactly as it applies to any system that can take consequential action: grant the minimum set of capabilities the task requires. A bug fix does not need deployment credentials. A documentation update does not need database write access. A refactor does not need production environment access. The principle is not caution for its own sake — it is containment. When an agent's action surface is narrower than the available surface, errors stay bounded.

OWASP's LLM Top 10 lists excessive agency as a core risk (OWASP, 2025): an agent that can do more than the task requires is an agent whose errors can propagate further than the task warrants.

---

## Seven Steps From Issue to Decision

The most useful way to understand agentic engineering supervision is to follow a bounded task from problem statement to merge decision. Each step is a specific human action, not a formality.

**Define the issue as acceptance criteria.** The human writes what is broken, what the correct behavior should be, and how to confirm it. Vague scope — "fix the login bug" — is not enough. Concrete scope includes: what input produces the wrong output, what the expected output is, and what test or demonstration confirms the fix is correct. This is not pedantry. An agent that receives vague scope will infer scope, and its inference may not match yours.

**Bound the permissions before the agent starts.** Which repository, which folders, which commands, and what is explicitly off-limits. Secrets directories should be excluded. Production systems should be excluded unless the task specifically requires production access and that access has been approved independently.

**Review the plan before any action executes.** Before the agent edits a file or runs a command, it should produce a plan: which files it will inspect, what it expects to find, how it proposes to address the problem, and what it will do to verify the result. This plan is the human gate. Read it. Ask whether it covers the stated scope and nothing beyond it. A plan that reaches further than the issue is an opening for a conversation, not authorization to proceed.

<!-- → [DIAGRAM: Issue-to-PR workflow as a linear pipeline with the human gate highlighted at the plan-review step — stages: define issue → bound permissions → review plan → [GATE] → authorize edits and commands → run verification → inspect diff → merge or reject] -->

**Authorize bounded edits and commands.** When the plan is acceptable, the agent proceeds. For high-risk commands — installing packages, running migration scripts, modifying configuration — require explicit approval per action rather than blanket authorization for the session.

**Run verification.** When the agent reports completion, verify with the means appropriate to the task. Run the tests. Run the build. Run the linter. The foundational insight from HumanEval evaluation work (Chen et al., 2021) generalizes well here: code should be evaluated by execution, not by whether it looks correct to the reader. A fix that passes the failing test but breaks an adjacent behavior is not a fix. Run the full test suite, not only the targeted case.

**Inspect the diff.** Read what actually changed. The diff is the complete record of the agent's action on your system. If lines changed that were not discussed in the plan, ask why before merging. If the diff is larger than the problem scope warrants, that is a warning sign. Accept based on what the diff says, not on what the agent's explanation says. These can diverge.

**Merge or reject.** The human makes this call. Reflexion-style self-evaluation research shows that agents can reason about their own work and revise it (Shinn et al., 2023). Self-reflection is useful — but it is not a substitute for external test evidence. The agent may be confidently wrong. The tests and the diff are evidence. The agent's description of why its changes are correct is a hypothesis.

---

## Two Categories That Require Special Handling

Two areas of the action surface warrant extra attention because their failure modes are not just bugs but structural vulnerabilities.

**Secrets and credentials.** Coding agents read files. If API keys, database passwords, private keys, or authentication tokens live in files within the repository scope, those secrets are inside the agent's observation surface. The safe posture: exclude secrets directories and credential files explicitly from the scope, use a .gitignore-style exclusion or folder separation, and never include credential handling as part of an agentic task. If the agent reads a secrets file and that context is logged, summarized, or incorporated into a response you paste somewhere, the secret has traveled.

**Deployment and production access.** An agent that can run commands can, in principle, run deployment commands. Unless the workflow explicitly requires it and has separate organizational approval, deployment should not be part of an agentic coding task. The boundary is not arbitrary conservatism — it is a recognition that deployment errors affect users, and that the cost of a wrong deployment is qualitatively different from the cost of a wrong test result. Staging and test environments are appropriate agentic scope. Production requires a separate human-authorized gate.

---

## The Property That Makes Engineering Tractable

Knowledge work is hard to verify. A document might look correct and contain a subtle error. A summary might omit something important without any visible sign that it is incomplete. Chapter 5 will address exactly this problem.

Code is different. Code has an oracle. A test either passes or fails. A build either compiles or errors. A linter either finds a violation or it does not. This is why agentic engineering, despite the elevated action surface, is in some ways the most tractable application of agentic AI. The verification path is built into the domain.

The implication is that a good agentic engineering workflow leans into this property hard. Tests are not a courtesy check. They are the primary verification method. If a codebase has poor test coverage, agentic engineering on that codebase is riskier than it looks, because the oracle is incomplete. The agent can make changes that pass every existing test while breaking behaviors the tests do not cover.

Research on AI coding assistance finds productivity gains on well-defined tasks, but the verification burden does not disappear with the gains (Peng et al., 2023). A faster path to a wrong answer is not progress. The goal is not faster code — it is correct code, delivered faster because the agent handles the investigation and editing while the human owns the outcome.

---

## The Human Role, Restated

What is the human actually doing in an agentic engineering workflow? Not pressing a button and waiting for the pull request. The role is:

Write the issue clearly enough that the acceptance criteria are testable. Set the permission boundary before the agent starts. Read the plan before authorizing action. Verify with tests and diff, not with the agent's description of success. Make the final accept or reject call.

This is engineering supervision. The agent handles the investigation and implementation work. The human holds the scope definition, the plan approval, and the merge decision. Delegation of the doing does not transfer accountability for the outcome. The developer who merges an agent-produced change is responsible for that change, which means they are responsible for having read the diff and run the tests.

The Microsoft Research Guidelines for Human-AI Interaction frame this kind of arrangement as a design requirement, not an optional layer: systems should support appropriate user control, uncertainty disclosure, and recoverability (CHI 2019). For a coding agent, "recoverability" means version control, staging environments, and the ability to revert. These are not new tools. They are existing engineering practices that become supervision infrastructure when an agent is doing the editing.

---

## What Compiling Does Not Prove

A misconception that travels across teams and deserves a direct answer: generated code that compiles has passed a syntax check. That is all. Compilation confirms that the code is grammatically valid in the language. It says nothing about whether the code does what was intended, whether it introduces a security vulnerability, whether it handles edge cases, or whether it regresses behavior that worked before. Code that compiles and fails is still a failure. The compiler is not a test suite.

The related misconception is that a green test suite means review is unnecessary. Tests check what they test. A targeted fix can pass its own test while introducing a regression in an adjacent behavior that no test covers. The diff review exists precisely for this reason: it surfaces what changed regardless of whether the test suite noticed.

---

## LLM Exercises

**Exercise 1.** Take a bug or small task from your current work, or invent a plausible one. Write it as an agent-ready task packet: what is broken, what the correct behavior is, which files are in scope, which are not, and what test or demonstration would prove the fix is correct. Compare your draft with what you would normally write in a ticket. Name one thing that is different and why it matters for an agent.

**Exercise 2.** Find a recent code change — one you made, one a colleague made, or one from an open-source project. Read the diff without reading the commit message first. Identify: what changed, whether the scope matches what you would expect from the description, and whether anything in the diff was outside the stated purpose of the change. Practice until diff reading feels like reading, not like inspecting alien text.

**Exercise 3.** For a hypothetical codebase you know well, answer the three questions from Chapter 1: what should Claude Code be allowed to see, what should it be allowed to decide, and what should it be allowed to do? Then identify one action category that should require explicit per-action approval rather than session-level authorization, and explain why.

---

## Graduated Exercises

**Warm-up**

1. *Recall the principle.* The chapter states that a coding agent should be granted the minimum set of capabilities the task requires. Give one concrete example of a task where applying this principle would mean restricting an access that might seem convenient to leave open. Name the specific risk you are avoiding.

2. *Classify the action.* A developer asks a coding agent to search the repository for all files that import a deprecated library and list them in a report. No file edits are made. Using the action surface table, identify the risk level, state whether a human gate is needed, and explain your reasoning.

**Application**

3. *Write a bounded task.* A function in a web application returns an HTTP 500 error when a user submits an empty form field that should be validated client-side. Write a complete agent-ready task packet: what is broken, expected behavior, files in scope, files excluded, and the test or behavior that confirms the fix is correct.

4. *Evaluate a plan.* An agent presents the following plan for a bug fix task: "I will read the authentication module, update the password validation function, add a test for the new validation logic, and run the full test suite." The original issue was only about a missing null check. What would you ask the agent before authorizing it to proceed?

5. *Apply the diff review standard.* After an agent completes a task, the diff shows three changed files. The task description mentioned one file. The agent explains that the other two files import the function that was fixed and needed compatible updates. What is your verification step before merging, and what specifically would you look for in those two additional files?

**Synthesis**

6. *Design a permission boundary.* You are setting up Claude Code to assist with a microservices backend. The repository contains application code, a `/secrets` folder with environment-specific credentials, a `/deploy` folder with production deployment scripts, and a `/tests` folder. A developer wants the agent to be able to investigate and fix bugs in the application code. Write a permission boundary specification: what is explicitly in scope, what is explicitly excluded, and what action categories require per-action human approval.

7. *Assess a workflow.* A team has adopted the following practice: after Claude Code completes a task, if the test suite passes with no failures, the change is automatically merged to the main branch. Using the verification framework from this chapter, identify the specific gaps in this workflow and propose the minimum additions that would make it defensible.

**Challenge**

8. *Principled tiering.* The chapter notes that the field has not converged on a principled tiering of when automated merge is defensible versus when human review is required. Propose a tiering framework for a medium-sized software product. Your framework should specify at least three tiers, describe the criteria that place a task in each tier, and state what verification and approval requirements apply at each tier. Defend the placement of one concrete task in each tier.

---

## Sources Used

- Anthropic. "Claude Code overview." *Claude Code Docs*. https://code.claude.com/docs [verify — current as of writing]
- Anthropic. "How Claude Code works." *Claude Code Docs*. https://code.claude.com/docs/en/how-claude-code-works [verify — current as of writing]
- Anthropic. "Configure permissions." *Claude Code Docs*. https://code.claude.com/docs/en/permissions [verify — current as of writing]
- Anthropic. "Claude Code security." *Claude Code Docs*. https://docs.claude.com/en/docs/claude-code/security [verify — current as of writing]
- Chen, M. et al. "Evaluating Large Language Models Trained on Code." *arXiv*, 2021. https://arxiv.org/abs/2107.03374
- Peng, S. et al. "The Impact of AI on Developer Productivity: Evidence from GitHub Copilot." *arXiv*, 2023. https://arxiv.org/abs/2302.06590
- Yao, S. et al. "ReAct: Synergizing Reasoning and Acting in Language Models." *ICLR*, 2023. https://arxiv.org/abs/2210.03629
- Shinn, N. et al. "Reflexion: Language Agents with Verbal Reinforcement Learning." *NeurIPS*, 2023. https://papers.nips.cc/paper_files/paper/2023/hash/1b44b878bb782e6954cd888628510e90-Abstract-Conference.html
- OWASP. "Top 10 for LLM Applications 2025." https://owasp.org/www-project-top-10-for-large-language-model-applications/
- Saltzer, J. H. and Schroeder, M. D. "The Protection of Information in Computer Systems." *Proceedings of the IEEE*, 1975. https://web.mit.edu/Saltzer/www/publications/protection/
- Microsoft Research. "Guidelines for Human-AI Interaction." CHI 2019. https://www.microsoft.com/en-us/research/project/guidelines-for-human-ai-interaction/publications/
