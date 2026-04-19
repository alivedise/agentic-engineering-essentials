# AEE-607 Reference Verification Notes

## Verified sources

| # | URL | What it supports | Flavor | Status |
|---|---|---|---|---|
| 1 | https://github.com/kyegomez/swarms | "Enterprise-Grade Production-Ready Multi-Agent Orchestration Framework"; primitives Agent + Swarm; patterns: SequentialWorkflow, ConcurrentWorkflow, AgentRearrange, GraphWorkflow, MixtureOfAgents, GroupChat, HierarchicalSwarm, HeavySwarm, ForestSwarm, SwarmRouter. Vendor-neutral (OpenAI, Anthropic, Groq, Cohere, DeepSeek, Ollama, OpenRouter, XAI, Llama4). Latest v6.8.1 Dec 2024. | A + C (offers both hierarchical/conductor patterns and MixtureOfAgents/ForestSwarm decentralized-leaning patterns) | verified |
| 2 | https://github.com/ruvnet/ruflo | "Multi-agent AI orchestration for Claude Code"; queen/worker swarm model; SONA self-learning (sub-ms adaptation); 100+ specialized agents; 313 MCP tools; RuVector vector DB. Quote: "Claude Flow is now Ruflo — named by Ruv, who loves Rust, flow states, and building things that feel inevitable." Flow: User → Ruflo (CLI/MCP) → Router → Swarm → Agents → Memory → LLM Providers. | A (queen is the central coordinator) | verified |
| 3 | https://github.com/desplega-ai/agent-swarm | Lead/worker coordination with Docker-isolated workers; persistent identity files (SOUL.md, IDENTITY.md, TOOLS.md, CLAUDE.md); six lifecycle hooks (SessionStart, PreCompact, PreToolUse, PostToolUse, UserPromptSubmit, Stop); searchable memory via OpenAI embeddings (text-embedding-3-small); integrations Slack/GitHub/GitLab/AgentMail/Sentry; TypeScript (94.4%). "Compounding knowledge"; "get smarter over time". | A (lead is the central coordinator) | verified |
| 4 | https://github.com/openai/swarm | Two primitives: Agents + Handoffs. Explicit deprecation: "Swarm is now replaced by the OpenAI Agents SDK, which is a production-ready evolution of Swarm. The Agents SDK features key improvements and will be actively maintained by the OpenAI team. We recommend migrating to the Agents SDK for all production use cases." Stateless, client-side, Chat Completions API. | B | verified (as historical reference) |
| 5 | https://openai.github.io/openai-agents-python/ | Official OpenAI Agents SDK site. Positioning: "It's a production-ready upgrade of our previous experimentation for agents, Swarm." Primitives: Agents + Handoffs + Guardrails. Multilingual docs. | B | verified |
| 6 | https://microsoft.github.io/autogen/stable/ | "A framework for building AI agents and applications"; four tiers: Studio, AgentChat, Core (event-driven multi-agent), Extensions. Supports "conversational single and multi-agent applications". Primitives: AssistantAgent, OpenAIAssistantAgent, Model clients, McpWorkbench, distributed runtime. Event-driven. | A or B (mode-dependent: AgentChat conversations are B-like; Core event-driven can be A-like; GroupChat approaches A) | verified |
| 7 | https://docs.crewai.com/ | Positioning: "Ship multi-agent systems with confidence". Core primitives: Agents + Crews + Flows. Flows: start/listen/router steps with state management. "Guardrails, memory, knowledge, and observability baked in." | A (Crews are role-orchestrated; Flows are centrally defined) | verified |
| 8 | https://docs.langchain.com/oss/python/langgraph/overview | "A low-level orchestration framework and runtime for building, managing, and deploying long-running, stateful agents." Focus: orchestration, not multi-agent abstractions directly. Primitives: Nodes, Edges, State, Graphs (StateGraph). Graph topology is explicit. | A or C (graph-dependent: a DAG with supervisor node is A; a graph where nodes communicate via shared state without a supervisor approaches C) | verified |
| 9 | https://agixtech.com/conductor-vs-swarm-multi-agent-ai-orchestration/ | Establishes the conductor-vs-swarm framing directly. Quote: "the **Conductor** (centralized, hierarchical control) and the **Swarm** (decentralized, parallel execution)". Trade-offs: conductor has single point of failure, sequential, higher latency; swarm is fault-tolerant, parallel, lower latency. Recommends hybrid for production. | — | verified (industry framing) |

## Final framework table rows

```markdown
| [kyegomez/swarms](https://github.com/kyegomez/swarms) | A + C | Agent, Swarm, SwarmRouter; 10+ orchestration patterns | "Enterprise-Grade Production-Ready"; vendor-neutral LLM support; HierarchicalSwarm, HeavySwarm, ForestSwarm, MixtureOfAgents |
| [Ruflo (formerly claude-flow) — ruvnet/ruflo](https://github.com/ruvnet/ruflo) | A | Queen + worker, Router, 100+ specialized agents | SONA self-learning; 313 MCP tools; RuVector vector DB; Claude Code-native |
| [desplega-ai/agent-swarm](https://github.com/desplega-ai/agent-swarm) | A | Lead + worker, persistent identity files (SOUL/IDENTITY/TOOLS/CLAUDE), 6 lifecycle hooks | Docker-isolated workers; memory-driven compounding knowledge via OpenAI embeddings |
| [OpenAI Swarm (unmaintained)](https://github.com/openai/swarm) | B | Agent + handoff | Historical; explicitly deprecated in favor of OpenAI Agents SDK |
| [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) | B | Agent + handoff + guardrail | Production evolution of OpenAI Swarm |
| [AutoGen (Microsoft)](https://microsoft.github.io/autogen/stable/) | A or B | AgentChat (conversational), Core (event-driven), GroupChat | Spans flavors by mode; four-tier architecture (Studio/AgentChat/Core/Extensions) |
| [CrewAI](https://docs.crewai.com/) | A | Agents + Crews + Flows | Role-based coordination; flows for orchestration; production-framed |
| [LangGraph](https://docs.langchain.com/oss/python/langgraph/overview) | A or C | Nodes + edges + state (StateGraph) | Graph topology is explicit; flavor depends on whether the graph has a supervisor node |
```

## Conductor vs swarm framing source

- [Conductor vs. Swarm: Multi-Agent AI Architecture Guide — Agix Technologies](https://agixtech.com/conductor-vs-swarm-multi-agent-ai-orchestration/) — establishes the conductor-vs-swarm framing; verified 2026-04-19.

## Dropped

- Anthropic multi-agent research system blog — optional; not fetched to reduce unnecessary citations. The article stands on its other evidence.
