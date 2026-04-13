# Foundations & Mental Models — Research Notes

Generated: 2026-04-13. All URLs verified live via web search.

---

## AEE-100 upgrade sources (additional beyond stub)

Stub already cites: Anthropic agent docs, AWS agent docs, ReAct arXiv.

- [A Survey on Large Language Model based Autonomous Agents (arXiv 2308.11432)](https://arxiv.org/abs/2308.11432) — 2023 survey defining agent components (planning, memory, action); widely cited foundational taxonomy for LLM-based agents; provides rigorous property breakdown beyond the informal four-property list in the stub.
- [Agentic AI: a comprehensive survey of architectures, applications, and future directions (Springer, 2025)](https://link.springer.com/article/10.1007/s10462-025-11422-4) — peer-reviewed 2025 survey; defines "agentic AI" as systems with proactive planning, contextual memory, sophisticated tool use, and behavioral adaptation; complements the stub definition with architectural vocabulary.
- [LangChain State of AI Agents Report 2024](https://www.langchain.com/stateofaiagents) — practitioner survey of 1,300+ professionals; 51% already running agents in production; defines agent as "a system that uses an LLM to decide the control flow of an application"; useful real-world grounding for the definition section.
- [AI Agents vs. Agentic AI: A Conceptual Taxonomy (ScienceDirect, 2025)](https://www.sciencedirect.com/science/article/pii/S1566253525006712) — distinguishes "AI agents" (individual capability) from "Agentic AI" (system-level autonomy with goal complexity and generality); helps the article clarify the spectrum rather than a binary.

---

## AEE-101 upgrade sources (additional beyond stub)

Stub already cites: Karpathy tweet, Scaling Laws arXiv.

- [The AI Perception Gap: How to Ensure Employers and Workers Are Ready (World Economic Forum, 2026)](https://www.weforum.org/stories/2026/01/ai-perception-gap/) — WEF documents the worker-vs-employer perception mismatch; quantifies that professionals acknowledge AI's importance but systematically underestimate impact on their own roles; provides credentialed institutional backing for the gap claim.
- [Was 2025 the Year of Agentic AI, or Just More Agentic Hype? (Futurum, 2025)](https://futurumgroup.com/insights/was-2025-really-the-year-of-agentic-ai-or-just-more-agentic-hype/) — analyst review of 2025 production deployments vs. marketing claims; documents the "big gap between agent prototypes and production-ready products, similar to self-driving cars" analogy; directly supports the capability gap framing.
- [Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity (METR, 2025)](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) — empirical study: developers allowed to use AI took 19% *longer* on tasks, yet believed AI sped them up by 20-24%; striking evidence that perception and reality diverge even among experienced practitioners; primary empirical source for the gap.
- [The Truth About Agentic AI: Misconceptions, Risks, and Realities (Fullstack Labs)](https://www.fullstack.com/labs/resources/blog/the-truth-about-agentic-ai-common-misconceptions-debunked) — practitioner-oriented debunking of common misconceptions; covers capability overestimation, definitional confusion, and expectation mismatches between builders and business stakeholders.

---

## AEE-102 upgrade sources (additional beyond stub)

Stub already cites: Anthropic RLHF, DeepSeek-R1, Let's Verify Step by Step.

- [RLHF vs RLVR: Why AI Training Is Shifting to Verifiable Rewards (WhatHappenedInAI, 2026)](https://whathappenedinai.space/rise-of-rlvr-verifiable-rewards-ai-reasoning-2026/) — clear practitioner comparison of RLHF (human preference signal, noisy, bottlenecked) vs. RLVR (automated verifier, binary, scalable); explains exactly why technical domains improve faster; good prose source for the article.
- [Reinforcement Learning with Verifiable Rewards Makes Models Faster, Not Smarter (Promptfoo)](https://www.promptfoo.dev/blog/rlvr-explained/) — finding: "most RLVR gains come from sampling efficiency, not new capabilities"; the model could already reach correct answers but RLVR optimizes the path; important nuance for the "why technical domains excel" argument.
- [The State of Reinforcement Learning for LLM Reasoning (Sebastian Raschka)](https://magazine.sebastianraschka.com/p/the-state-of-llm-reasoning-model-training) — practitioner-level survey of RL training approaches; covers RLHF, RLVR, DPO, and their relative merits; cites DeepSeek-R1 and o-series as RLVR exemplars; authoritative ML educator perspective.
- [A Survey of Reinforcement Learning from Human Feedback (arXiv 2312.14925)](https://arxiv.org/abs/2312.14925) — comprehensive academic survey of RLHF across LLMs, robotics, and other domains; provides the formal treatment of RLHF that underpins the contrast with RLVR; published 2024, widely cited.

---

## AEE-103 Agent vs. Chatbot

- [Chatbot vs Agent: Understanding the Architecture, Tools and Memory Layer (DEV Community)](https://dev.to/yeahiasarker/chatbot-vs-agent-understanding-the-architecture-tools-and-memory-layer-3gop) — engineering-level breakdown of the architectural difference: chatbots are "stateless prediction" (request-response loop) vs. agents as "stateful reasoning" (perception-action loop); defines the specific transition point as tool use + persistent state.
- [AI Agents: Evolution, Architecture, and Real-World Applications (arXiv 2503.12687)](https://arxiv.org/html/2503.12687v1) — 2025 arxiv paper covering the evolution from chatbot to agent; key framing: "chatbots offer assistance, agents offer labor"; covers architectural properties that mark the transition.
- [Stop Building Chatbots: The Architecture of the 2026 Agentic Tech Stack (Medium)](https://medium.com/@abdulrahmanafifi33/stop-building-chatbots-the-architecture-of-the-2026-agentic-tech-stack-09d268879f5a) — practitioner perspective on the shift; characterizes agent vs. chatbot as: chatbot waits for human input + NLP/retrieval, agent is triggered by goal/event + reasoning/planning/tool use; includes architectural diagrams.
- [The Definitive Guide to AI Agents: Architectures, Frameworks, and Real-World Applications (MarkTechPost, 2025)](https://www.marktechpost.com/2025/07/19/the-definitive-guide-to-ai-agents-architectures-frameworks-and-real-world-applications-2025/) — consolidates definitions from OpenAI, Anthropic, LangChain; explains that a chatbot focuses on "interpreting a question and returning an answer" while an agent carries "a request from intent to outcome" across connected systems; good for quoting authoritative vendor definitions.

---

## AEE-104 Capability Tiers

- [Levels of Autonomy for AI Agents (Knight First Amendment Institute)](https://knightcolumbia.org/content/levels-of-autonomy-for-ai-agents-1) — institutional publication defining five-level autonomy framework based on the roles a user can take: operator, collaborator, consultant, approver, observer; explicitly states autonomy is a design decision decoupled from raw capability.
- [Levels of Autonomy for AI Agents Working Paper (arXiv 2506.12469)](https://arxiv.org/abs/2506.12469) — the formal academic version of the above; proposes "autonomy certificates" as governance mechanism; provides rigorous definitions for each tier.
- [Five Levels of AI Agents: From Reactive to Fully Autonomous (Kore.ai)](https://www.kore.ai/blog/five-levels-of-ai-agents) — industry practitioner breakdown of L1–L5: L1-L3 are reactive/assisted, L4-L5 are semi-autonomous/fully autonomous; analogizes to autonomous driving levels; good for a concrete tier table.
- [The 2025 AI Agent Index (MIT, aiagentindex.mit.edu)](https://aiagentindex.mit.edu/) — empirical deployment data showing most chat agents operate at Level 1–3, browser agents at Level 4–5; documents the surge in agent releases in 2024–2025; primary data source for "what tiers are actually deployed."
- [Measuring AI Agent Autonomy in Practice (Anthropic Research)](https://www.anthropic.com/research/measuring-agent-autonomy) — Anthropic's own empirical study of autonomy in Claude Code sessions; longest sessions grew from 25 to 45 minutes as users built trust; 52% of conversations classified as "augmentation" (HITL) vs. 45% "automated"; first-party production data.

---

## AEE-105 Perception Gap

- [Separating AI Hype from Engineering Reality (LeadDev)](https://leaddev.com/ai/separating-ai-hype-from-engineering-reality) — engineering management perspective; documents that "most common barriers to scaling AI are not model limitations but organizational ones"; good framing of the production gap as multi-dimensional.
- [Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity (METR, 2025)](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) — key empirical finding: developers expected 24% speedup, experienced 19% slowdown, and still believed afterward that AI had sped them up by 20%; strongest available empirical evidence of the practitioner perception gap.
- [Insights 15: The State of AI Agents in 2025: Balancing Optimism with Reality (AI2 Incubator)](https://www.ai2incubator.com/articles/insights-15-the-state-of-ai-agents-in-2025-balancing-optimism-with-reality) — practitioner analysis; documents the "big gap between agent prototypes and production-ready products"; uses self-driving car analogy (amazing progress, but you still can't buy a truly self-driving car); covers corner cases, ambiguity, depth of recall, security.
- [AI Hype Meets AI Reality: 2025's Biggest AI Misses (Peterson Technology Partners, 2026)](https://www.ptechpartners.com/2026/01/13/ai-hype-meets-ai-reality-2025s-biggest-ai-misses/) — retrospective on specific 2025 AI claims vs. outcomes; documents specific instances of capability overestimation that materialized as deployment failures; useful for concrete examples.

---

## AEE-106 Autonomy Spectrum

- [Human-in-the-Loop vs Human-on-the-Loop: Navigating the Future of AI (Serco)](https://www.serco.com/na/media-and-news/2025/human-in-the-loop-vs-human-on-the-loop-navigating-the-future-of-ai) — institutional source defining HITL (human approves every action) vs. HOTL (human monitors, intervenes on exceptions); discusses key trade-offs: HITL maximizes control, HOTL maximizes scale; cites use in high-stakes domains.
- [The Loop Paradox: HITL, Human-above-the-Loop, AI-in-the-Loop, Human-OUT of the Loop (Medium, 2026)](https://medium.com/@savneetsingh_1/the-loop-paradox-human-in-the-loop-human-above-the-loop-ai-in-the-loop-and-human-out-of-the-loop-03fee4d66798) — extends the standard HITL/HOTL taxonomy to four positions including "Human-above-the-loop" (strategic governance) and "Human-out-of-the-loop" (fully autonomous); most complete spectrum framing found.
- [Human-in-the-Loop - Wikipedia](https://en.wikipedia.org/wiki/Human-in-the-loop) — canonical reference definition; traces the term to military command-and-control systems; establishes formal vocabulary; good for citing etymology and base definition.
- [From Human-in-the-Loop to Human-on-the-Loop: Evolving AI Agent Autonomy (ByteBridge, Medium)](https://bytebridge.medium.com/from-human-in-the-loop-to-human-on-the-loop-evolving-ai-agent-autonomy-c0ae62c3bf91) — explains the architectural shift from HITL to HOTL as agents become more capable; covers when each is appropriate; practitioner-level with concrete design patterns.

---

## AEE-107 Goals, Tasks, Plans

- [LLM Powered Autonomous Agents (Lilian Weng, Lil'Log, 2023)](https://lilianweng.github.io/posts/2023-06-23-agent/) — canonical reference post by OpenAI researcher; defines the agent system as: LLM brain + memory + tool use + planning; covers task decomposition strategies (CoT, Tree of Thoughts, ReAct), memory types, and tool categories; the most-cited practitioner reference on agent architecture.
- [What is BabyAGI? (IBM)](https://www.ibm.com/think/topics/babyagi) — IBM's clear explanation of the BabyAGI loop: execute task → create new tasks based on result → reprioritize task list; uses GPT-4 + vector DB for memory; illustrates the simplest form of goal-directed task planning.
- [LLM Agent Task Decomposition Strategies (APXML)](https://apxml.com/courses/agentic-llm-memory-architectures/chapter-4-complex-planning-tool-integration/task-decomposition-strategies) — covers CoT, Tree of Thoughts, ReAct, and hierarchical decomposition strategies; explains why decomposition transforms "intractable problems into series of manageable sub-problems"; curriculum-grade explanation.
- [Routine: A Structural Planning Framework for LLM Agent System in Enterprise (arXiv 2507.14447)](https://arxiv.org/pdf/2507.14447) — 2025 paper proposing structured planning for enterprise agent systems; covers how goals are decomposed into routines, tasks, and actions; provides formal vocabulary for the goal→task→plan hierarchy.

---

## AEE-108 Context as a Resource

Key paper: "Lost in the Middle" — [arXiv 2307.03172](https://arxiv.org/abs/2307.03172) | [ACL Anthology (published version)](https://aclanthology.org/2024.tacl-1.9/) | [MIT Press TACL](https://direct.mit.edu/tacl/article/doi/10.1162/tacl_a_00638/119630/Lost-in-the-Middle-How-Language-Models-Use-Long)

- [Lost in the Middle: How Language Models Use Long Contexts (Liu et al., arXiv 2307.03172)](https://arxiv.org/abs/2307.03172) — foundational paper showing U-shaped performance curve: LLMs perform best when relevant info is at start or end of context, degrade significantly when it is in the middle; GPT-3.5-Turbo multi-doc QA drops >20% at worst; primary evidence for "context position matters" claim.
- [Context Rot: Why LLMs Degrade as Context Grows (Morph)](https://www.morphllm.com/context-rot) — explains "context rot" — performance degradation as input tokens increase, even within nominal context window; distinct from "lost in the middle" (position effect) vs. "context rot" (total volume effect); both effects relevant to AEE-108.
- [Cutting Through the Noise: Smarter Context Management for LLM-Powered Agents (JetBrains Research, 2025)](https://blog.jetbrains.com/research/2025/12/efficient-context-management/) — JetBrains research on two strategies: observation masking and LLM summarization; empirical finding that keeping 10 most recent full turns + summarizing earlier turns is optimal; practical implementation guidance.
- [ACON: Optimizing Context Compression for Long-Horizon LLM Agents (arXiv 2510.00615)](https://arxiv.org/html/2510.00615v2) — 2025 paper on dynamic context compression; lowers peak token usage by 26-54% while maintaining task performance; improves small LM agent performance by 32-46% on benchmarks; best available paper specifically on context compression for agentic tasks.

---

## AEE-109 How LLMs Work

Key paper: [Attention Is All You Need — arXiv 1706.03762](https://arxiv.org/abs/1706.03762) (Vaswani et al., 2017; 173,000+ citations as of 2025)

- [Attention Is All You Need (Vaswani et al., arXiv 1706.03762)](https://arxiv.org/abs/1706.03762) — the foundational transformer paper; proposes architecture based solely on attention mechanisms, dispensing with recurrence and convolutions; 2017, NeurIPS; primary citation for any transformer explanation.
- [How Transformers Work: A Detailed Exploration (DataCamp)](https://www.datacamp.com/tutorial/how-transformers-work) — practitioner-level walkthrough covering encoder-decoder structure, multi-head self-attention, positional encoding, and autoregressive generation; accessible without heavy math; suitable as supplementary reading for engineers.
- [Byte-Pair Encoding Tokenization (Hugging Face LLM Course)](https://huggingface.co/learn/llm-course/en/chapter6/5) — authoritative HuggingFace curriculum on BPE; explains the merging algorithm step-by-step; covers why English is efficient (~1 token/word) and other languages are not; canonical reference for tokenization.
- [What Is a Transformer Model? (IBM)](https://www.ibm.com/think/topics/transformer-model) — IBM's practitioner explanation covers self-attention, feedforward layers, and how every major LLM (GPT, Claude, Gemini, Llama) uses decoder-only architecture; good non-academic reference for engineers who need conceptual grounding.
- [minbpe: Minimal BPE Tokenizer Implementation (Karpathy, GitHub)](https://github.com/karpathy/minbpe) — Karpathy's clean Python implementation of BPE; shows the algorithm in ~200 lines; useful reference for engineers who learn from code; links to his accompanying lecture series.

---

## AEE-110 LLM Limitations and Failure Modes

- [A Comprehensive Survey of Hallucination in Large Language Models (arXiv 2510.06265)](https://arxiv.org/html/2510.06265v1) — 2025 comprehensive survey covering hallucination causes across the entire LLM lifecycle (data, architecture, training, inference); categorizes factuality hallucination vs. faithfulness hallucination; identifies attention mechanism weaknesses and capability misalignment as root causes.
- [Hallucination is Inevitable: An Innate Limitation of Large Language Models (arXiv 2401.11817)](https://arxiv.org/abs/2401.11817) — formal argument that LLMs cannot learn all computable functions and will therefore hallucinate if used as general problem solvers; provides theoretical grounding for why hallucination is structural, not just a training artifact.
- [Sycophancy in Large Language Models: Causes and Mitigations (arXiv 2411.15287)](https://arxiv.org/abs/2411.15287) — dedicated survey on sycophancy (LLM tendency to favor user agreement over independent reasoning); covers causes (RLHF reward hacking), impacts, and mitigations; explains the relationship between sycophancy and hallucination (belief misalignment).
- [LLM-based Agents Suffer from Hallucinations: A Survey of Taxonomy, Methods, and Directions (arXiv 2509.18970)](https://arxiv.org/html/2509.18970v1) — 2025 survey specifically on hallucination in agentic contexts (not just single-turn generation); covers how multi-step reasoning compounds hallucination risks; directly relevant to AEE-110 in the context of this course.

---

## AEE-111 Model Selection for Agentic Tasks

- [The Model Selection Trap: Choosing the Right LLM for Agentic Systems 2026 (Medium)](https://medium.com/@nraman.n6/the-model-selection-trap-choosing-the-right-llm-for-agentic-systems-2026-be2817c2e533) — practitioner framework for agentic model selection; key heuristic: "context window, pricing, and tool features predict fit better than parameter counts"; covers the trap of defaulting to frontier models for all tasks.
- [LLM Routing: How to Stop Paying Frontier Model Prices for Simple Queries (TianPan.co, 2025)](https://tianpan.co/blog/2025-10-19-llm-routing-production) — production routing guide; demonstrates that shifting 50% of queries from GPT-4o to GPT-4o-mini cuts costs ~14x on that segment; covers routing by task type (factual lookup, JSON extraction, short summary can use cheaper models); provides concrete cost math.
- [A Unified Approach to Routing and Cascading for LLMs (arXiv 2410.10347)](https://arxiv.org/abs/2410.10347) — academic paper deriving optimal cascade routing strategy; cascading (try cheaper model first, escalate if needed) achieves 5x+ cost savings at negligible performance loss on RouterBench; most rigorous available paper on the routing/cascading problem.
- [LLM vs. SLM vs. FM: A Strategic Guide to AI Model Selection (Acuvate)](https://acuvate.com/blog/choosing-right-ai-model-llm-slm-fm/) — three-tier framework: SLMs for speed/cost/governance, LLMs for flexible reasoning, Frontier Models for complex autonomous tasks; SLMs serve tokens in tens of ms vs. hundreds of ms for cloud LLMs; practical decision matrix.
- [Choosing an LLM in 2026: The Practical Comparison Table (DEV Community)](https://dev.to/superorange0707/choosing-an-llm-in-2026-the-practical-comparison-table-specs-cost-latency-compatibility-354g) — comparison table of major models by specs, cost, latency, and context window; current (2026) reference for concrete numbers engineers need when making selection decisions.
