# Chapter 11 — Agentic AI in Teams and Organizations
*Individual caution is necessary. It is not sufficient.*

Picture a five-person marketing team six months into using Claude. Here is what their practice looks like on a Friday afternoon.

Person A has Cowork connected to the client contracts folder and uses it to draft status reports. Person B uses a personal Claude account on their laptop and pastes in excerpts from internal Slack messages. Person C refuses to use AI on anything client-facing and is skeptical the others are being careful. Person D set up a Cowork scheduled task that sends a weekly digest — no one else knows exactly what data it pulls. Person E asked IT to block Cowork and was told to figure it out on their own.

No one has done anything catastrophically wrong. But there is no shared understanding of what data is allowed, which tools are approved, who reviews what before it leaves the building, what a mistake would look like, or who is accountable if a client report contains a hallucinated figure.

This is the problem the chapter addresses. Not individual discipline — the preceding chapters covered that. The problem is that individual discipline does not aggregate into team safety. A team that deploys agents without shared governance has an inconsistency problem that grows proportionally with the number of people using agents and the breadth of what those agents can touch.

---

## Why Individual Caution Stops Working

A single practitioner managing their own agentic work can hold scope, tool choices, and approval logic in their head. That works for one person. When agents act across shared systems — shared folders, shared repositories, shared connectors, team communication channels, external-facing documents — the scope and risk are no longer individual.

Three problems emerge at scale.

*Inconsistency.* When every team member has different rules, there is no predictable boundary. Data one person treats as approved may be prohibited under another's reading. A connector added for one person's use may be visible to the entire team account and reach more than anyone intended.

*Accountability gaps.* If an agent produces a flawed output — an incorrect figure in a client deliverable, an unauthorized data disclosure, a message sent prematurely — it is not clear who owned the task, who approved the data scope, and who was supposed to verify the output before use. Individual practice does not answer these questions at the organizational level.

*Governance drift.* Without shared documentation, the team's actual practice diverges from what anyone would endorse if asked. The scheduled task no one remembers setting up is an audit risk. The connector added for convenience last quarter may have permissions that extended beyond its original purpose and haven't been reviewed since.

NIST's AI Risk Management Framework addresses this directly: organizational governance requires not just individual risk awareness but explicit management functions — govern, map, measure, manage — applied to AI systems (NIST AI RMF 1.0, 2023). ISO/IEC 42001:2023 frames AI governance as a management system, the same structural category as quality management or security management. The point is not formality for its own sake. The point is that shared governance makes accountability visible and checkable. Without it, accountability exists on paper and nowhere else.

---

## What Shared Governance Actually Consists Of

A team operating model for agentic AI is a set of shared decisions in six areas.

*Approved use cases.* What tasks can agents be used for? An inventory prevents unreviewed expansion. The team that has never named its approved uses cannot tell when a new use has crossed a line.

*Prohibited data and actions.* What must never enter an agent context? Credentials, personal identifiable information, unpublished confidential data, and production system access appear on almost every team's prohibited list. The specifics depend on the domain.

*Tool and connector approvals.* Which Claude surfaces, MCP servers, and connectors are approved for team use? Who can add a new one? Who reviews the tool's permission scope and removes it when no longer needed?

*Role boundaries.* Who can initiate an agentic task? Who can approve high-risk actions? Who owns verification before an output reaches an external audience?

*Audit trails.* What is logged? Who can access the log? How long is it retained? Without an audit trail, "who did what" is a question no one can answer.

*Escalation paths.* What triggers a human escalation — unexpected data in the output, an action outside the defined scope, a sensitive artifact, an agent request for access it was not designed to have?

For a five-person team, this fits in a shared document and a brief onboarding checklist. For a regulated enterprise, it maps onto formal compliance requirements. The structure scales:

<!-- → [TABLE: governance ladder — seven rows from Personal rule through Enterprise compliance — columns: level name, what it looks like in practice, who it applies to] -->

Most practitioners are somewhere in the middle of that ladder. The goal is to make whatever level the team is at deliberate rather than accidental.

---

## Shared Assets Change the Risk Calculation

When an agent can access shared assets, individual risk becomes collective risk. The shared assets that most commonly appear in agentic workflows: team file systems, shared repositories, communication channels, MCP servers enabled at the account level, and any external-facing system the agent can reach.

The first governance task is mapping what agents can actually touch. The answer is often more than the team expects. A Cowork connector configured to access a team folder accesses everything in that folder — not just the files one person intended. A shared MCP server added to a team account is available to every team member's agent, not just the one who added it. OWASP's Top 10 for Model Context Protocol specifically names server trust, tool permission scope, and credential exposure as organizational risks in shared deployments (OWASP, 2025) [verify — current as of writing].

<!-- → [DIAGRAM: team shared-asset map — central node labeled "shared team systems" with spokes showing file system, repository, communication channels, MCP servers, external systems — each spoke annotated with example agent access pattern and who the risk now belongs to] -->

The mapping exercise has a direct output: a list of assets agents can reach that the team did not explicitly authorize. That list becomes the first thing the team governs.

---

## The Team AI-Use Register

The team AI-use register is the practical governance document. For each use case, it answers: what is allowed, who is responsible, and what does the human gate require?

A single entry looks like this: Use case — draft client status report from project files. Owner — project manager. Claude surface — Cowork. Data allowed — sanitized project folder only. Data forbidden — contracts, PII, credentials, financial terms. Tools/connectors — approved project folder connector only. Human gate — source and privacy review before sending. Log — prompt used, files accessed, output produced, reviewer name. Escalation — legal/privacy lead if sensitive data appears in output.

Three to ten entries covering the team's regular agent uses is a working start. The key is that the team has agreed on the boundaries, not that the document is exhaustive. A register that is incomplete but exists and is consulted is more valuable than a comprehensive policy that was written once and never opened again.

<!-- → [TABLE: sample register with four use-case rows across different domains — columns: use case, owner, surface, data allowed, data forbidden, human gate, escalation] -->

---

## Meaningful Oversight Is Not the Same as Nominal Oversight

Zhu et al. (2026), in research on designing meaningful human oversight, identify a recurring failure mode: nominal oversight. A reviewer is assigned. They lack the time, context, authority, or evidence to actually evaluate the output. The result is an oversight role that provides accountability on paper but no real check on what the agent did.

This is not a hypothetical. It is the default when teams add a reviewer step without adding the resources the reviewer needs to function.

Meaningful oversight requires four things. *Time:* a reviewer with thirty seconds to skim a ten-page report is not reviewing it. *Context:* a reviewer who does not know what data the agent used, what instructions it was given, or what it was supposed to produce cannot catch fabrications, omissions, or scope violations. *Authority:* a reviewer who cannot reject or revise the output — because the deadline has passed, because organizational culture treats agent output as default-approved — is not a gate. *Evidence:* a reviewer who sees only the final output cannot check whether the agent used approved data, whether sources were verified, or whether the scope stayed within bounds.

The audit trail is what makes meaningful oversight possible. When the log records the prompt used, the files the agent accessed, the tools that ran, and the output produced, the reviewer has the evidence to actually review. Without that log, oversight is a formality. With it, oversight is a function.

---

## A Software Team Builds a Policy

Consider a software team of eight engineers using Claude Code for development work. Without a policy, practice varies: some engineers use Claude Code on feature branches, some use it directly on main, some have given it read access to production configuration files, one recently asked it to help write a database migration without running tests.

The team works through a one-hour exercise.

*Use-case inventory.* They list what agents are actually used for: feature branch development, test writing, documentation, code review assistance, migration planning.

*Data and access boundary.* They agree: Claude Code may access the repository but not production credentials, environment files, or external secrets management. They add a `.claudeignore` file listing excluded paths [verify — current as of writing].

*Approval structure.* Feature branch work: engineer reviews the diff before committing. Test-requiring changes: tests must pass before merge. Production-adjacent work — migrations, configuration changes: senior engineer review required before any execution.

*Audit trail.* The team uses the existing pull request review history as their primary audit artifact. For agent-assisted work, the PR description includes a note: what the agent was asked to do, what tests were run, what the human verified.

*Escalation.* If Claude Code requests access to a path outside its defined scope, or proposes an action the engineer does not recognize as part of the approved task, they stop and bring it to the team.

*Review cadence.* Monthly retro includes a standing agenda item: any agent incidents, any policy questions, any new use cases to evaluate.

This is not a heavy process. It takes an hour to draft and ten minutes a month to maintain. It converts informal individual caution into a shared, auditable practice. The same structure applies across domains — the fields in the register change, the structure does not.

---

## MCP Governance at the Team Level

MCP servers compound the governance challenge because a single server added to a team account extends capabilities to every team member's agent. OWASP's parallel analysis of MCP risks identifies tool permission scope and server trust as organizational-level concerns, not individual ones (OWASP, 2025) [verify — current as of writing].

A minimal team MCP governance policy covers: an approved server list with a named owner per server; documented tools and the systems each server can reach; access scope — available to all team members or specific roles only; log availability and access; and a decommissioning process. The principle of least privilege applies here exactly as it applies to individual agent scope: approve the minimum tool set for the defined use case, and revisit when the use case changes.

The question worth asking before approving any new server: if this server were used against the most sensitive data in our shared systems, what could it expose or change? If the answer is "more than we intend," the permissions need to be narrowed before the server is approved.

---

## The Governance Ladder in Practice

Organizations vary in their formality requirements. What matters is operating at the right level of the ladder deliberately, not operating at the most formal level possible.

A research group that agrees verbally to keep unpublished data out of commercial AI accounts and have a researcher verify all cited claims before manuscript inclusion has a working governance model at level three. A regulated financial firm that needs legal, IT, and privacy review of all agentic workflows is operating at level seven. The structure of the decisions — use case, data boundary, tool approval, human gate, audit trail, escalation — is the same at both levels. The documentation and enforcement mechanisms differ.

What does not work is operating as if individual caution alone handles the shared-asset risks. The marketing team in the opening scene has five individually cautious people and no shared model of what cautious means. That is not governance at any level of the ladder. It is individuals hoping for the best in parallel.

---

## What Would Change My Mind

The argument here is that informal individual practice is insufficient once agents act on shared assets, and that a lightweight team governance model is the minimum viable structure. That argument would weaken under a few conditions.

If evidence showed that teams with no explicit governance had materially fewer agent-related incidents than teams with documented practices, the case for governance overhead would soften. No such evidence currently exists.

If tools emerged that automatically enforced data boundaries, permission scopes, and audit logging at the platform level in ways that eliminated the need for human-designed governance, the burden would shift to configuring those tools rather than writing policy. Current platform controls reduce but do not eliminate governance requirements [verify — current as of writing].

If the costs of team governance consistently exceeded its benefits across small teams, the minimum viable bar would need to be redefined. In practice, a use-case register takes an afternoon to draft and prevents inconsistencies that take far longer to resolve.

---

## Still Puzzling

How much governance is appropriate for a two-person team? The ladder helps, but the minimum viable threshold for very small teams deserves more specific guidance than frameworks currently provide.

Who owns AI governance in organizations where IT, legal, and operational teams have competing authority? The chapter focuses on team-level practice, but organizational politics of AI governance ownership are genuinely contested and not resolved by frameworks.

How do audit trails interact with data-retention and privacy obligations? Keeping logs of what data an agent accessed may itself create retention obligations or privacy risks in some jurisdictions. This requires legal guidance specific to context.

How should teams handle shadow AI — agent use that bypasses official channels? The opening scene is not unusual. Teams that prohibit tools without providing alternatives typically see higher informal use. The policy question of how to bring shadow use into a governable structure is not fully resolved.

---

## AI Wayback Machine

![Elinor Ostrom](../images/elinor-ostrom-5bi.png)

*Puppet Art by [Nik Bear Brown](https://www.nikbearbrown.com/).*

**Run this:**

```
Who was Elinor Ostrom, and how does her work on governing the commons connect to how teams should manage shared AI tools and agent access? Keep it to three paragraphs. End with the single most surprising thing about her career or ideas.
```

Search **"Elinor Ostrom"** on Wikipedia, then try it with Claude.

**Now make the prompt better:**

- Ask it to apply Ostrom's design principles for commons governance to a specific shared AI resource — a team Cowork account, a shared MCP server, or a team repository with Claude Code access.
- Ask whether "the tragedy of the commons" applies to AI tool use on a team. Does it, or does the analogy break down?

What changes? What gets more useful? Where does the analogy fail?

---

## LLM Exercises

**1.** Map your team's actual agent use. List every agent-assisted workflow your team or organization uses today — not what is officially approved, but what is actually happening. For each one, ask Claude to help you identify: what data does the agent access, who owns the task, what is the human gate before the output is used, and how many of these are documented anywhere. Then ask Claude to flag which entries in your list represent shared-asset risks that would not appear in any individual's personal risk assessment.

**2.** Draft a team AI-use register entry for one real use case from your work. Fill out the register template from this chapter: use case, owner, Claude surface, data allowed, data forbidden, tools/connectors, human gate, log, escalation. Submit it to Claude and ask it to identify ambiguities, missing fields, or governance gaps. Revise once based on the response.

**3.** Apply the meaningful oversight test to a review process your team currently uses for agent output. Ask Claude to evaluate whether your described process meets the four criteria — time, context, authority, evidence — or whether it constitutes nominal oversight. If nominal, ask Claude to propose a minimal change to one criterion that would make the oversight meaningful without substantially increasing the review burden.

---

## Sources Used

- NIST. "Artificial Intelligence Risk Management Framework (AI RMF 1.0)." 2023. https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10
- NIST. "Artificial Intelligence Risk Management Framework: Generative Artificial Intelligence Profile." 2024. https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf
- ISO/IEC 42001:2023. "Artificial Intelligence Management System." https://www.iso.org/standard/42001
- Zhu et al. "Designing Meaningful Human Oversight in AI." AI and Ethics, 2026. https://link.springer.com/article/10.1007/s43681-026-01147-7
- "AAGATE: A NIST AI RMF-Aligned Governance Platform for Agentic AI." arXiv, 2025. https://arxiv.org/abs/2510.25863
- "Policy-Aware Generative AI for Safe, Auditable Data Access Governance." arXiv, 2025. https://arxiv.org/abs/2510.23474
- OWASP. "Top 10 for LLM Applications 2025." https://owasp.org/www-project-top-10-for-large-language-model-applications/
- OWASP. "Top 10 for Model Context Protocol." https://owasp.org/www-project-mcp-top-10/
- Anthropic. "Use Claude Cowork safely." Claude Help Center. https://support.claude.com/en/articles/13364135-use-cowork-safely
- Anthropic. "Use Claude's chat search and memory to build on previous context." Claude Help Center. https://support.claude.com/en/articles/11817273-using-claude-s-chat-search-and-memory-to-build-on-previous-context
