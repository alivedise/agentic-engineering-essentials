# AEE Overall — Research Notes

Researched: 2026-04-13

---

## AEE-1 Glossary Terms

| Term | Definition | Source URL |
|------|-----------|------------|
| agent | A software system that uses an LLM as its reasoning engine to perceive goals, plan multi-step workflows, call external tools, and act autonomously to complete tasks with minimal human oversight | https://cloud.google.com/discover/what-is-agentic-ai |
| tool use | The mechanism by which an LLM calls external capabilities (APIs, databases, code interpreters, file systems) at runtime; the model emits a structured instruction specifying which tool to invoke and with what parameters, then receives the result as an observation | https://www.promptingguide.ai/agents/function-calling |
| function calling | The original OpenAI term for tool use — the LLM produces a structured JSON payload naming a function and its arguments; the host application executes the function and feeds the return value back into context; emphasizes discrete, executable code blocks rather than broader tool abstractions | https://fireworks.ai/blog/function-calling |
| harness | All code, configuration, and execution logic that wraps an LLM but is not the model itself: the orchestration loop, tool registry, memory management, context assembly, state persistence, error handling, and guardrails. "An agent = a model + a harness." | https://parallel.ai/articles/what-is-an-agent-harness |
| skill | A packaged, reusable behavioral pattern exposed to an agent via the harness — distinct from a raw tool (function call) in that it encapsulates intent, parameters, and execution logic as a named, composable unit the agent can invoke to accomplish a specific subtask (e.g., "web_search", "run_code", "send_email") | AEE project-defined term; concept also present in Microsoft Semantic Kernel skills — https://learn.microsoft.com/en-us/semantic-kernel/concepts/plugins/ |
| orchestration | The coordination layer that manages how an LLM agent sequences actions, delegates to sub-agents or tools, tracks state across steps, and routes outputs back into the reasoning loop; the "conductor" of multi-step agentic workflows | https://www.ibm.com/think/tutorials/llm-agent-orchestration-with-langchain-and-granite |
| context window | The maximum number of tokens (input + output) an LLM can process in a single forward pass; determines how much history, instructions, and retrieved data can be visible to the model at once; as of 2024–2025 leading models offer 128K–1M+ token windows | https://www.ibm.com/think/topics/context-window |
| RAG | Retrieval-Augmented Generation — a technique that grounds LLM outputs in factual, up-to-date information by retrieving relevant documents from an external knowledge base at inference time and prepending them to the prompt; coined by Lewis et al. (NeurIPS 2020) | https://arxiv.org/abs/2005.11401 |
| ReAct | A prompting / agent-loop framework (Yao et al., ICLR 2023) that interleaves Reasoning traces and Acting steps — the model alternates between generating chain-of-thought and issuing tool calls, with each tool's observation fed back before the next thought; improves grounding and reduces hallucination compared to pure CoT | https://arxiv.org/abs/2210.03629 |
| prompt | The text (or multimodal input) submitted to an LLM to elicit a response; in agentic systems a prompt is assembled dynamically by the harness and typically contains a system prompt, retrieved context, tool definitions, conversation history, and the current user message | https://www.promptingguide.ai/agents/function-calling |
| system prompt | The privileged, model-level instruction block prepended to every conversation; sets persona, rules, available tools, output format, and safety constraints; invisible to the end user but read by the model before any user turn | https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/system-prompts |
| inference | The process of running a trained model forward on new input to generate output; for autoregressive LLMs this is iterative token-by-token sampling; distinct from training — no weights are updated during inference | https://bentoml.com/llm/llm-inference-basics/how-does-llm-inference-work |
| hallucination | When an LLM generates text that is factually incorrect, internally inconsistent, or entirely fabricated while appearing fluent and confident; arises because LLMs are trained to produce plausible token sequences, not to verify truth | https://www.k2view.com/blog/what-is-grounding-and-hallucinations-in-ai/ |
| grounding | The practice of anchoring LLM responses in verifiable, factual data — typically via RAG, tool calls, or fine-tuning on authoritative corpora — so that outputs are traceable to real-world knowledge sources rather than parametric memory alone | https://www.k2view.com/blog/what-is-grounding-and-hallucinations-in-ai/ |
| autonomy spectrum | A conceptual axis that describes how much independent decision-making an AI system exercises, ranging from fully human-directed (copilot / autocomplete) through supervised autonomy (human-in-the-loop) to fully autonomous operation (unsupervised, long-horizon execution) | https://arxiv.org/abs/2510.25445 |
| verifiable reward | In Reinforcement Learning with Verifiable Rewards (RLVR), a reward signal derived from an objective, programmatic ground-truth check (e.g., unit-test pass/fail, math answer equality) rather than a learned human-preference model; binary (0/1), tamper-resistant, and well-suited to code and math domains | https://www.theainavigator.com/blog/what-is-reinforcement-learning-with-verifiable-rewards-rlvr |
| fine-tuning | Continued training of a pre-trained base model on a task-specific or domain-specific dataset to specialize its outputs; updates model weights (unlike RAG, which injects knowledge at inference); common subtypes include supervised fine-tuning (SFT), RLHF, and RLVR | https://huggingface.co/docs/transformers/training |
| tokenization | The preprocessing step that splits raw text into tokens — the atomic units an LLM processes; tokens correspond roughly to word fragments (subwords via BPE or similar algorithms); ~1 token ≈ 0.75 English words; determines how text length maps to model capacity and cost | https://blogs.nvidia.com/blog/ai-tokens-explained/ |

---

## AEE-2 Landscape Sources

- [Agentic AI: A Comprehensive Survey of Architectures, Applications, and Future Directions (arXiv 2510.25445)](https://arxiv.org/abs/2510.25445) — PRISMA-based review of 90 studies (2018–2025); introduces dual-paradigm framework (Symbolic/Classical vs Neural/Generative); documents the post-2022 shift to neural orchestration frameworks following the rise of LLMs

- [Measuring AI Ability to Complete Long Tasks — METR (March 2025)](https://metr.org/blog/2025-03-19-measuring-ai-ability-to-complete-long-tasks/) — Proposes "time horizon" as the key autonomy metric; shows task-duration capability has doubled approximately every 7 months for 6 years; suite of 180+ ML-engineering, cybersecurity, and SE tasks ranging from 1 minute to 8+ hours of human effort

- [SWE-bench Leaderboard](https://www.swebench.com/) — Live benchmark tracking coding-agent performance on real GitHub issues; as of early 2026, top confirmed models reach 76–81% on SWE-bench Verified; Claude Opus 4.5 was the first model to break 80% (80.9%), with GPT-5.1 at 76.3%; SWE-bench Pro scores are ~23% for top models, signaling headroom

- [Top Agentic AI Frameworks in 2025 (Medium / Data Science Collective)](https://medium.com/data-science-collective/top-agentic-ai-frameworks-in-2025-which-one-fits-your-needs-0eb95dcd7c58) — Categorizes 2025 frameworks into six groups: Autonomous Agents (AutoGPT, BabyAGI, AgentGPT), Multi-Agent Collaboration (AutoGen, CrewAI, MetaGPT, OpenAI Swarm), RAG-Based (LangChain, LlamaIndex, Haystack), Reasoning-Optimized (LangGraph, Smolagents, n8n), Domain-Specific (Azure AI Agent Service, AWS Bedrock Agents, Google Vertex AI Agent Builder), and Low-Code/No-Code (Flowise, Dify, Relevance AI)

- [State of AI Agents in 2025 (Carl Rannaberg, Medium)](https://carlrannaberg.medium.com/state-of-ai-agents-in-2025-5f11444a5c78) — Technical analysis; notes Anthropic Claude Opus 4 demonstrated sustained 7-hour task execution; documents transition from experimental demos to production-scale deployment; identifies reliability, security, and observability as top enterprise blockers

---

## Key Statistics / Benchmarks

> **Note for article-writing subagents:** Re-check swebench.com live before citing any SWE-bench numbers — this is a rapidly-moving leaderboard and these numbers may be outdated.

- **SWE-bench Verified (2025):** Claude Opus 4.5 achieved 80.9%, first model to break the 80% barrier; GPT-5.1 at 76.3%, Gemini 3 Pro at 76.2% — source: https://www.swebench.com/
- **SWE-bench Pro (2025):** Best models (OpenAI GPT-5, Claude Opus 4.1) score ~23.3% / 23.1%, illustrating the gap between curated benchmarks and real-world difficulty — source: https://labs.scale.com/leaderboard/swe_bench_pro_public
- **METR Time Horizon (March 2025):** AI agents can reliably complete tasks taking humans ~1 hour (50% success rate); this metric doubles every ~7 months; suite expanded to 228 tasks in TH1.1 (January 2026) — source: https://metr.org/blog/2025-03-19-measuring-ai-ability-to-complete-long-tasks/
- **METR TH1.1 (January 2026):** Expanded evaluation suite from 170 to 228 tasks — source: https://metr.org/blog/2026-1-29-time-horizon-1-1/
- **Gartner 2025 Hype Cycle:** AI agents named one of the two fastest-advancing technologies; predicts 33% of enterprise software will include agentic AI by 2028; 15% of daily work decisions made autonomously by 2028
- **PwC 2025 AI Agent Survey:** 79% of organizations have adopted AI agents; ~64% cite security and risk concerns as top barrier to full scaling

> **Unverified — do not cite without confirming:** Gartner / PwC figures need live URL before use in article.
- **Context window scale (2024–2025):** Leading commercial models offer 1M+ token context windows (Google Gemini 1.5); Anthropic Claude Enterprise launched with 500K tokens (September 2024)

---

## Source Verification Notes

All URLs above were returned by live web searches conducted on 2026-04-13. Primary academic sources (arXiv, NeurIPS proceedings) are stable permanent URLs. Leaderboard URLs (swebench.com, METR) are live sites that update continuously — statistics reflect the state as of search date. Framework comparison articles from Medium are secondary sources; the arXiv survey (2510.25445) and METR blog posts are the most authoritative primary sources for the landscape section.
