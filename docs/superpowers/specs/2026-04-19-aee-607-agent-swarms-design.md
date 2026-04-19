# AEE-607: Agent Swarms — Article Design

**Date:** 2026-04-19
**Status:** Approved design, pending implementation plan
**Article ID:** AEE-607
**Category:** Multi-Agent and Orchestration (600s)
**Files:**
- `docs/en/Multi-Agent and Orchestration/607.md`
- `docs/zh-tw/Multi-Agent and Orchestration/607.md`

## Purpose

Fill a gap in the current corpus: "agent swarm" has become an umbrella term in 2026 over multiple distinct multi-agent architectures, and the existing 600s articles do not address the concept. AEE-601's peer-to-peer topology is narrower than what "swarm" currently means. AEE-605's orchestration patterns are all conductor-style. This article establishes the conductor-vs-swarm architectural split, critically examines what "swarm" actually covers (three distinct flavors), and surveys the 2026 framework landscape.

## Non-goals

- Not a catalog of every multi-agent framework. The landscape section covers only frameworks that self-identify as swarms or are routinely called swarms; other multi-agent frameworks (pure LangGraph, pure AutoGen graph mode) appear only where they help contrast.
- Not a re-statement of general multi-agent material from AEE-600/601/605/606. This article points to those articles rather than reproducing their content.
- Not an endorsement or ranking of frameworks. The article treats frameworks as evidence for the architectural category, not as the category's subject.
- Not a bio-inspired swarm intelligence tutorial. References to biological swarms appear only to clarify the gap between the marketing analogy and real LLM agent behavior.

## Scope and Boundary vs. Existing 600s Articles

| Concern | Covered by | AEE-607 treatment |
|---|---|---|
| General "when to coordinate" | AEE-600 | Points to 600 |
| Supervisor/worker, pipeline, peer-to-peer topologies | AEE-601 | Points to 601; notes AEE-601's peer-to-peer is narrower than swarm |
| Agent communication | AEE-602 | Points to 602 |
| Task decomposition | AEE-603 | Points to 603 |
| Parallelism/synchronization | AEE-604 | Points to 604 |
| Map-reduce, fan-out, review loop, hierarchical patterns | AEE-605 | Points to 605; notes all are conductor-style |
| Multi-agent failure modes | AEE-606 | Points to 606 for general failure modes; adds swarm-specific failure modes |
| Conductor-vs-swarm architectural dichotomy | Not covered | Owns |
| What "swarm" actually covers in 2026 (three flavors) | Not covered | Owns |
| 2026 swarm framework landscape | Not covered | Owns |
| When to choose swarm vs. conductor | Not covered | Owns |

## Core Thesis

"Agent swarm" in 2026 is an umbrella term covering three distinct architectures:

1. **Centralized multi-agent with ergonomic tooling** — a conductor pattern dressed up with LLM-native primitives (hooks, persistent identity files, MCP tools, Docker isolation). The dominant "swarm"-labeled category. Examples: desplega-ai/agent-swarm (lead/worker), Ruflo (queen/worker).
2. **Handoff-based dynamic routing** — the currently-running agent decides who runs next by returning an agent reference. Distinct from both centralized orchestration and true peer-to-peer. Examples: OpenAI Swarm (deprecated), OpenAI Agents SDK.
3. **Genuinely decentralized coordination** — no single orchestrator; fault tolerance via redundancy; emergent behavior. Rare in production. Most framework marketing gestures at this without implementing it.

The article's job is to give readers a decoder for which flavor a given "swarm" framework actually is.

## Article Structure

Standard AEE template. No `**RFC 2119:**` section label (post-cleanup convention).

### Context (~200 words)

- Name the phenomenon: "swarm" has spread across the agentic tooling market but covers very different architectures depending on the speaker.
- Name the gap in the existing 600s: AEE-601's peer-to-peer is narrower than what swarm means today; AEE-605's orchestration patterns are all conductor-style. No current article addresses decentralized coordination or the conductor-vs-swarm framing.
- Set up the article's job: decode what "swarm" actually covers, establish the conductor-vs-swarm architectural split, survey the 2026 frameworks that use the label.
- End with a pointer: for the general concept of multi-agent coordination, see AEE-600 and AEE-601; this article is specifically about the swarm label and what sits under it.

### Design Think (~300 words)

- First paragraph (~80 words): the 2026 framing — conductor architectures (centralized coordinator directs workers) vs. swarm architectures (decentralized agents coordinate through shared state or local interaction). State this is an architectural split, not a continuum.
- Second paragraph (~70 words): what "swarm" is trying to name at its best — decentralization, fault tolerance via redundancy, dynamic composition, horizontal scale, agent autonomy. Each characteristic is a property of the system, not of any individual agent.
- Third paragraph (~80 words): honest disclosure. Most frameworks labeled "swarm" are conductor-with-extras, not true swarms. The biological analogy (ants, bees) is aspirational — real LLM agents have too much individual capability and too much global state to behave like ants. The label is usefully evocative, but readers need to verify what it actually denotes for any given framework.
- MUST/SHOULD/MAY bullets (flowing directly, no label):
  - Engineers MUST distinguish conductor-style multi-agent from swarm-style before choosing a framework. The coordination cost, failure surface, and debuggability differ sharply.
  - "Swarm" framing SHOULD be avoided when the underlying architecture is actually a conductor — it confuses reviewers and misrepresents the system's failure characteristics.
  - Swarm architectures SHOULD be chosen only when fault tolerance via redundancy is a requirement, task work is naturally parallel with loose coupling, or horizontal scale is the primary lever.
  - Teams adopting a "swarm" framework MUST verify which of the three flavors it actually implements before committing to it.

### Deep Dive — four subsections

**1. Conductor vs. swarm: the architectural split**

- Prose paragraph explaining each side at a glance.
- Comparison table:

| Aspect | Conductor | Swarm |
|---|---|---|
| Control | Centralized; one orchestrator directs workers | Decentralized; no single point of authority |
| Scaling | Vertical — orchestrator gets bigger and smarter | Horizontal — add more agents |
| Failure surface | Orchestrator is single point of failure | Graceful degradation; individual agents can fail |
| Coordination cost | O(N) (orchestrator knows N workers) | O(N²) worst case (any agent may talk to any other) |
| Debuggability | High — follow decisions from orchestrator | Low — emergent behavior, distributed traces |
| Best for | Sequential logic, auditable decision chains, tight coordination | Parallel loose-coupled work, fault tolerance, horizontal scale |

- Closing note: most real systems are hybrid. Pure swarm is rare in production; pure conductor is common. The question is where on the axis a given system sits, not which box it fills.

**2. What "swarm" actually covers in 2026 — the three flavors**

The centerpiece. For each flavor:
- Definition in one sentence
- Key primitive(s)
- Representative frameworks (brief)
- What distinguishes it from the other two
- When it is the right label

Flavor A — *Centralized multi-agent with ergonomic tooling*:
- A conductor pattern with LLM-native developer ergonomics (hooks, persistent identity files, MCP tool integration, Docker-isolated workers, memory/learning loops).
- Primitives: lead or queen coordinator, worker pool, lifecycle hooks, persistent agent identity.
- Representative: desplega-ai/agent-swarm (lead/worker, Docker isolation, SOUL.md identity files, memory indexing), Ruflo/claude-flow (queen/worker, SONA self-learning, 60+ specialized agents, RuVector DB).
- Distinguishes: still has a central authority, just wrapped in more ergonomic tooling than classical orchestrator patterns.
- Right label when: the goal is convenient multi-agent tooling and "swarm" is signalling ergonomic maturity, not architectural decentralization.

Flavor B — *Handoff-based dynamic routing*:
- The currently-running agent decides which agent runs next by returning a reference to another agent. No external dispatcher.
- Primitives: Agent, handoff (a function that returns another Agent).
- Representative: OpenAI Swarm (October 2024, now superseded), OpenAI Agents SDK (production evolution, March 2025).
- Distinguishes: coordination is embedded in agent logic itself, not in an external controller. Not truly decentralized (only one agent runs at a time), but control is dynamic and emergent in the choice of next agent.
- Right label when: the flow is conversational/sequential with agent-driven routing, and there is no global orchestrator making dispatch decisions.

Flavor C — *Genuinely decentralized / emergent coordination*:
- No single coordinator. Agents act on local state and shared artifacts. Behavior emerges from many concurrent local decisions.
- Primitives: shared blackboard or message bus, local decision policies, peer discovery.
- Representative: kyegomez/swarms offers multiple patterns including some (MixtureOfAgents, ForestSwarm) that approach this; research systems and Anthropic's multi-agent research architecture flirt with it. Genuinely decentralized production systems remain rare.
- Distinguishes: no orchestrator, even a lightweight one. Fault tolerance and horizontal scale come for free; debuggability is very low.
- Right label when: fault tolerance via redundancy is a hard requirement and the task decomposes into loosely coupled parallel work.

**3. Framework landscape**

Table-driven, skim-friendly. For each framework: flavor (A/B/C), primitives, notable feature, vendor neutrality.

| Framework | Flavor | Primitives | Notable feature |
|---|---|---|---|
| kyegomez/swarms | A + C (multiple patterns) | Agent + Swarm, multiple orchestration topologies | "Enterprise-grade" production framing; MixtureOfAgents approach |
| Ruflo / claude-flow (ruvnet) | A | Queen + worker, SONA self-learning, MCP tools | Claude Code-native; 60+ specialized agents; self-learning loop |
| desplega-ai/agent-swarm | A | Lead + worker, persistent identity (SOUL.md), Docker isolation | Memory-driven compounding knowledge; lifecycle hooks |
| OpenAI Swarm (2024, unmaintained) | B | Agent + handoff | First popular handoff-based framework; superseded by Agents SDK |
| OpenAI Agents SDK | B | Agent + handoff, tool schemas | Production evolution of Swarm |
| AutoGen (Microsoft) | A or B (mode-dependent) | Conversable agents, group chat | Conversational multi-agent; mode selects pattern |
| CrewAI | A | Crew + role-based agents | Role specialization; task delegation |
| LangGraph | A or C (graph-dependent) | Graph nodes, state, edges | Graph topology is explicit, not implied |

Brief prose after the table noting which frameworks fit which flavor and why. Acknowledge ambiguity: AutoGen and LangGraph span flavors depending on how they are configured; that itself is a signal that the label is context-dependent.

**4. When swarm is the right answer**

A simple decision framework:

*Choose swarm when:*
- Fault tolerance via redundancy is a hard requirement (no single point of failure acceptable).
- Work is naturally parallel with loose coupling (independent subtasks that do not depend on each other's intermediate state).
- Horizontal scale is the primary lever (throughput grows with agent count).
- Decision chains do not need audit trails through a central authority.

*Choose conductor when:*
- Decision chains must be auditable (compliance, safety-critical, post-incident review).
- Task structure is sequential or has tight synchronization needs.
- The team needs predictable debugging — emergent behavior is a cost, not a feature.
- Throughput matters less than correctness.

*Choose hybrid when:*
- The task decomposes into large independent domains (conductor at top, swarms inside domains).
- This is the usual answer in practice — pure either end is rare.

### Best Practices (6 items)

1. **Verify the flavor before adopting a "swarm" framework.** Read the architecture docs, not the marketing. If it has a queen, lead, or orchestrator, it is a conductor in swarm clothing; that may still be the right tool, but you should know what you are buying.
2. **Reject the swarm label for conductor systems.** A supervisor-with-persistent-memory is a conductor. Calling it a swarm misrepresents its failure surface and coordination cost to reviewers, newcomers, and yourself six months later.
3. **Treat debuggability as a first-class cost of swarm architectures.** Decentralized coordination is expensive to debug. Budget for distributed tracing, per-agent audit logs, and replay tooling before declaring a system swarm-architected.
4. **Prefer hybrid over pure swarm.** Most production workloads have some parts that need auditable decision chains (handled by a conductor) and some parts that benefit from horizontal parallel redundancy (handled by a swarm within a domain). Do not force the whole system into one mode.
5. **Expect the framework landscape to churn.** Most of the specific frameworks named in this article will change names, maintenance status, or semantics within 18 months. The architectural categories are more durable than any specific tool.
6. **Do not import biological swarm metaphors into engineering decisions.** Ants and bees have simple individual agents; LLM agents are the opposite. Emergent swarm intelligence as seen in biology is not what LLM swarms deliver. Evaluate them as concurrent distributed systems, not as colony organisms.

### Visual

Mermaid diagram of the conductor-vs-swarm split — two side-by-side structures. Plus a repeated table of the three flavors as an at-a-glance reference.

### Related AEEs

- AEE-600 When to Coordinate Agents — parent; when multi-agent is warranted at all
- AEE-601 Agent Roles and Topologies — the three canonical topologies; this article extends the peer-to-peer discussion to modern swarm variants
- AEE-605 Orchestration Patterns — all conductor-style patterns; this article is the decentralized complement
- AEE-606 Multi-Agent Failure Modes — general failure modes; swarm-specific modes are called out in Best Practices
- AEE-100 What Is an Agent — baseline definition; a swarm of agents is still a system of agents

### References

Primary sources (all to be verified during implementation):

1. Framework repos / docs:
   - `https://github.com/kyegomez/swarms`
   - `https://github.com/ruvnet/ruflo` (formerly claude-flow)
   - `https://github.com/desplega-ai/agent-swarm`
   - `https://github.com/openai/swarm` (historical; unmaintained)
   - OpenAI Agents SDK docs
   - AutoGen docs
   - CrewAI docs
   - LangGraph docs
2. Conceptual framing:
   - A "conductor vs. swarm" analysis piece from a reputable source (multiple exist; pick one verified at write time)
   - Any academic or industry reference on swarm intelligence vs. multi-agent that sharpens the distinction
3. Adjacent AEE evidence:
   - Anthropic's multi-agent research system (if a canonical blog post URL is reachable) as evidence of production multi-agent practice
   - Research papers on agent coordination cost as supporting material

Anything not verifiable at write time drops from the References list and, if load-bearing, drops from the article body too.

### Changelog

Single entry: `2026-04-19 -- Initial draft`

## Bilingual Plan

EN and zh-TW written in the same session. Tables and mermaid diagrams identical across languages. Terminology policy:

- "Agent Swarm" / "swarm" — rendered as `代理群集` in zh-TW (or `Swarm` kept in English where English is the canonical term in the cited material)
- "Conductor" (architectural) — `指揮者` or `中央協調者` in zh-TW, picked to match corpus conventions
- "Handoff" — keep in English; introduce `（交接）` parenthetical on first use
- "Orchestrator" — `編排器` (matches existing 600s zh-TW)
- Framework names, filenames, primitives — stay in English
- The three flavors: `集中式多代理（搭配人體工學工具）` / `基於交接的動態路由` / `真正去中心化協調`

Verify against AEE-601, AEE-605, and AEE-606 zh-TW for established terms; corpus usage wins if conflicts arise.

## Frontmatter

```yaml
# EN
id: 607
title: Agent Swarms
state: draft
```

```yaml
# zh-TW
id: 607
title: 代理群集
state: draft
```

## Category file updates

- `docs/en/Multi-Agent and Orchestration/600.md` — add AEE-607 to the category article list following the pattern for 601–606.
- `docs/zh-tw/Multi-Agent and Orchestration/600.md` — same update.
- `docs/en/list.md` / `docs/zh-tw/list.md` — auto-generated; not hand-edited.

## Implementation notes

- Reference AEE-601, AEE-605, AEE-606 for tone and section depth (same category).
- Reference AEE-807 for framework-survey prose style in Deep Dive subsection 3.
- No `**RFC 2119:**` bold labels (post-cleanup convention).
- No emoji.
- Run `pnpm docs:build` during implementation to catch frontmatter / link errors before commit.

## Out of scope / follow-ups

- Swarm intelligence in classical AI (PSO, ACO algorithms) — explicitly not covered; we stay focused on LLM-agent swarms.
- Detailed performance benchmarks of specific frameworks — deferred; would date quickly and risk becoming a product comparison.
- A template AGENTS.md / SOUL.md for a swarm setup — deferred; belongs in a different article if warranted.

## Changelog

- 2026-04-19 -- initial spec
