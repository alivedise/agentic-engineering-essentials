# AEE Model & Context Layer — Research Notes

> Temporary file. Delete before tagging v0.3.0.
> Last updated: 2026-04-14

---

## AEE-201: Tokenization in Practice

### Key claims

**Token-to-word ratios**
- tiktoken README states: "On average, in practice, each token corresponds to about 4 bytes." For typical English prose this yields roughly 0.75 words per token (i.e., ~1.3 tokens per word). The tiktoken README gives no explicit per-language breakdown.
- Chinese and Japanese: Because these scripts are not well-covered by BPE subword units trained on predominantly English data, they tokenize less efficiently. UNVERIFIED — check before citing: specific ratio numbers (commonly cited as 1–2 characters per token vs. 3–4 characters per English word token) are not confirmed from a primary source in this research pass.
- Code: UNVERIFIED — check before citing. The commonly cited figure is roughly 1 token per 4 characters for code (similar to English prose), but code can vary significantly by language and style. No primary source confirmed during this research pass.

**Tokenizer for GPT-4o**
- From `tiktoken/model.py` (fetched 2026-04-14): GPT-4o uses `o200k_base`. GPT-4 uses `cl100k_base`. GPT-3.5-turbo uses `cl100k_base`.
- `cl100k_base`: vocabulary of 100,000 tokens, used by GPT-4 and GPT-3.5-turbo.
- `o200k_base`: vocabulary of 200,000 tokens, used by GPT-4o, o1, o3, o4-mini, and GPT-5-series. Larger vocabulary allows more efficient tokenization for non-English languages and code.
- The `o200k_harmony` variant is used by `gpt-oss-` prefix models (confirmed in model.py).

**Edge cases**
- Numbers like "12345": BPE tokenization of digits varies. UNVERIFIED — check before citing. Common observation is that multi-digit numbers split into individual digits or small groups (e.g., "12345" may tokenize as ["123", "45"] or similar), but exact behavior depends on vocabulary. No primary source confirmed with specific examples during this research pass.
- URLs: URLs typically tokenize into many tokens because they contain non-standard substrings with slashes, dots, and query parameters that do not appear as frequent subwords. UNVERIFIED — check before citing: specific token counts for sample URLs were not confirmed from a primary source.

**tiktoken**
- tiktoken is an open-source BPE tokenizer library from OpenAI, available at https://github.com/openai/tiktoken.
- It is "between 3-6x faster than a comparable open source tokeniser" (tiktoken README, confirmed fetched).
- API: `tiktoken.get_encoding("o200k_base")` or `tiktoken.encoding_for_model("gpt-4o")`. Encode with `.encode()`, decode with `.decode()`.
- On average each token corresponds to about 4 bytes (tiktoken README).

**Claude token counting API**
- Endpoint: `POST https://api.anthropic.com/v1/messages/count_tokens`
- Accepts the same structured input as the Messages API (system, messages, tools, images, PDFs).
- Response field: `{ "input_tokens": N }`
- Eligible for Zero Data Retention (ZDR).
- Confirmed from Anthropic docs (fetched 2026-04-14).

### Verified sources
- https://github.com/openai/tiktoken — confirmed live (HTTP 200), contains tiktoken BPE tokenizer for OpenAI models; README describes ~4 bytes/token average and 3-6x speed advantage
- https://raw.githubusercontent.com/openai/tiktoken/main/tiktoken/model.py — confirmed live; contains model-to-encoding mapping (GPT-4o → o200k_base, GPT-4 → cl100k_base)
- https://platform.openai.com/tokenizer — HTTP 403 (Cloudflare challenge). URL structure confirms it is the OpenAI tokenizer playground, but content was not accessible from curl. Flag for human verification.
- https://docs.anthropic.com/en/docs/build-with-claude/token-counting — confirmed live; describes `/v1/messages/count_tokens` endpoint, response `{ "input_tokens": N }`, ZDR eligibility

### Dead/unverified URLs
- https://platform.openai.com/tokenizer — returns HTTP 403 Cloudflare challenge from non-browser clients. The URL is real but could not be scraped. Human verification recommended.
- https://platform.openai.com/docs/guides/structured-outputs — same Cloudflare 403 issue (applies to all platform.openai.com/docs/* URLs from this research pass).
- https://platform.openai.com/docs/guides/fine-tuning — same Cloudflare 403 issue.
- https://platform.openai.com/docs/guides/prompt-engineering — same Cloudflare 403 issue.

---

## AEE-202: Context Window Architecture

### Key claims

**RoPE (Rotary Position Embedding)**
- Introduced in "RoFormer: Enhanced Transformer with Rotary Position Embedding" by Su et al. (2021).
- arXiv: 2104.09864. Submitted 20 Apr 2021; last revised 8 Nov 2023.
- Key idea: "encodes the absolute position with a rotation matrix and meanwhile incorporates the explicit relative position dependency in self-attention formulation."
- Notable properties: flexibility of sequence length, decaying inter-token dependency with increasing relative distances, capability of equipping linear self-attention with relative position encoding.
- RoFormer is integrated into HuggingFace (confirmed in abstract).

**Lost in the Middle**
- Paper: "Lost in the Middle: How Language Models Use Long Contexts" by Liu et al. (2023).
- arXiv: 2307.03172. Submitted 6 Jul 2023; last revised 20 Nov 2023. Published in Transactions of the Association for Computational Linguistics (TACL), 2023.
- Tasks tested: multi-document question answering and key-value retrieval.
- Finding: "performance is often highest when relevant information occurs at the beginning or end of the input context, and significantly degrades when models must access relevant information in the middle of long contexts, even for explicitly long-context models."
- Quantitative accuracy drop: The abstract states performance "degrades significantly" but does not give a specific percentage number. UNVERIFIED — check before citing: specific numbers (often cited as ~20 percentage point drop) would need to be verified against the paper body, which was not fetched. Do not cite a specific number without checking the full paper.

**Sliding window attention (Longformer)**
- Paper: "Longformer: The Long-Document Transformer" by Beltagy, Peters, Cohan et al. (2020).
- arXiv: 2004.05150. Submitted 10 Apr 2020; last revised 2 Dec 2020.
- Key idea: attention mechanism that scales linearly with sequence length (standard self-attention scales quadratically). Combines local windowed attention with task-motivated global attention.
- Achieved state-of-the-art on text8, enwik8, WikiHop, and TriviaQA at time of publication.
- Introduced Longformer-Encoder-Decoder (LED) variant for long document generative tasks.

**Context rot**
- Term describes the degradation of LLM output quality as context length grows, due to attention dilution and signal-to-noise ratio decay.
- From the morphllm.com article (confirmed live, HTTP 200): "At 10K tokens: attention_weight ≈ 1/10,000 = 0.0001. At 100K tokens: attention_weight ≈ 1/100,000 = 0.00001. At 1M tokens: attention_weight ≈ 1/1,000,000 = 0.000001. Each 10x increase in context reduces per-token attention by 10x."
- The article cites: "Cognition (Devin) measured this directly: agents spend over 60% of their first turn just retrieving context." UNVERIFIED — check before citing: this Cognition/Devin claim should be traced to a primary source.
- Signal-to-noise example from the article: in a 20,000-token context where only ~500 tokens are the relevant code, the signal-to-noise ratio is 2.5%.
- The article also references Chroma researchers' finding: "what matters more than whether relevant information is present is how that information is presented." UNVERIFIED — check before citing: needs primary source.

### Verified sources
- https://arxiv.org/abs/2104.09864 — confirmed live (HTTP 200); "RoFormer: Enhanced Transformer with Rotary Position Embedding" (Su et al., 2021/2023)
- https://arxiv.org/abs/2307.03172 — confirmed live (HTTP 200); "Lost in the Middle: How Language Models Use Long Contexts" (Liu et al., 2023); published in TACL 2023
- https://arxiv.org/abs/2004.05150 — confirmed live (HTTP 200); "Longformer: The Long-Document Transformer" (Beltagy et al., 2020)
- https://www.morphllm.com/context-rot — confirmed live (HTTP 200); article "Context Rot: Why LLMs Degrade as Context Grows" with mathematical attention weight analysis

### Dead/unverified URLs
- None dead. All four URLs returned HTTP 200.

---

## AEE-203: Structured Output Design

### Key claims

**JSON mode vs. structured outputs (OpenAI)**
- The OpenAI structured outputs docs URL (https://platform.openai.com/docs/guides/structured-outputs) returned Cloudflare 403 during this research pass. Content could not be verified.
- UNVERIFIED — check before citing: distinction between JSON mode (guarantees valid JSON but not schema conformance) vs. structured outputs (schema-constrained via JSON Schema, guarantees conformance). This distinction is widely documented but was not confirmed from a live fetch of the primary source.

**Constrained decoding / grammar-based sampling**
- UNVERIFIED — check before citing: mechanism description (masking out logits for tokens that would violate the grammar at each decoding step) was not confirmed from a fetched primary source during this research pass.

**Outlines library**
- https://github.com/dottxt-ai/outlines — confirmed live (HTTP 200); repo is titled "Structured Outputs" under the dottxt-ai organization. The note about potential migration from outlines-dev to dottxt-ai is confirmed correct: the current canonical URL is dottxt-ai/outlines.

**Anthropic tool use (structured output)**
- https://docs.anthropic.com/en/docs/build-with-claude/tool-use — confirmed live; page title is "Tools — Overview / How tool use works". Anthropic uses tool definitions with JSON Schema to constrain structured outputs from Claude.
- Schema design for reliability: UNVERIFIED — check before citing: specific schema design guidance (e.g., prefer required fields, avoid deeply nested optional objects) needs primary source verification against the actual tool use docs content (page was JS-rendered and full body was not extracted).

### Verified sources
- https://github.com/dottxt-ai/outlines — confirmed live (HTTP 200); "Structured Outputs" library under dottxt-ai organization
- https://docs.anthropic.com/en/docs/build-with-claude/tool-use — confirmed live (HTTP 200); Anthropic tool use overview

### Dead/unverified URLs
- https://platform.openai.com/docs/guides/structured-outputs — HTTP 403 Cloudflare challenge; URL is real but content was inaccessible. Human verification required.

---

## AEE-204: System Prompt Engineering

### Key claims

**System vs. user turn positioning**
- From the Anthropic prompting best practices page (confirmed live, fetched 2026-04-14): "Put longform data at the top: Place your long documents and inputs near the top of your prompt, above your query, instructions, and examples. Queries at the end can improve response quality by up to 30% in tests, especially with complex, multi-document inputs."
- This "up to 30%" figure is from Anthropic's own documentation. UNVERIFIED — check before citing: Anthropic's docs state this but do not cite a specific study; treat as Anthropic's internal benchmark, not an independent peer-reviewed finding.
- Role in system prompt: "Setting a role in the system prompt focuses Claude's behavior and tone for your use case. Even a single sentence makes a difference" (Anthropic docs, confirmed).

**Prompt injection attack vectors**
- From OWASP LLM01:2025 Prompt Injection (confirmed live, fetched 2026-04-14):
  - Direct prompt injection: "user's prompt input directly alters the behavior of the model in unintended or unexpected ways."
  - Indirect prompt injection: "LLM accepts input from external sources, such as websites or files. The content may have data that when interpreted by the model, alters the LLM's behavior."
  - Multimodal injection: "Malicious actors could exploit interactions between modalities, such as hiding malicious instructions in images."
- Potential impacts listed: disclosure of sensitive information, revealing system prompt details, content manipulation leading to incorrect/biased outputs, unauthorized access to functions, executing arbitrary commands in connected systems, manipulating critical decision-making processes.

**OWASP LLM Top 10 on prompt injection**
- The OWASP Gen AI page lists this as "LLM01:2025 Prompt Injection."
- Definition from page: "A Prompt Injection Vulnerability occurs when user prompts alter the LLM's behavior or output in unintended ways. These inputs can affect the model even if they are imperceptible to humans."
- Note: "Prompt injection and jailbreaking are related concepts in LLM security, they are often used interchangeably. Prompt injection involves manipulating model responses through specific inputs to alter the LLM's behavior, while jailbreaking typically involves circumventing safety measures."

**Anthropic system prompts docs**
- The URL https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/system-prompts returned HTTP 301 redirect to https://platform.claude.com/docs/en/docs/build-with-claude/prompt-engineering/system-prompts. The content fetched under the prompting best practices page covers system prompt guidance. Specific dedicated "system prompts" page content was not fully extracted.
- OpenAI prompt engineering guide: https://platform.openai.com/docs/guides/prompt-engineering returned HTTP 403 Cloudflare. Content not accessible.

### Verified sources
- https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/system-prompts — HTTP 301 redirect exists; redirects to platform.claude.com equivalent. Content about system prompts is covered under the broader prompting best practices page which was confirmed live.
- https://genai.owasp.org/llmrisk/llm01-prompt-injection/ — confirmed live (HTTP 200); OWASP LLM01:2025 Prompt Injection page with definition, types, and mitigation strategies

### Dead/unverified URLs
- https://platform.openai.com/docs/guides/prompt-engineering — HTTP 403 Cloudflare challenge; inaccessible. Human verification required.
- https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/system-prompts — redirects (HTTP 301). The redirect destination (platform.claude.com) should be verified for final citation.

---

## AEE-205: Fine-Tuning Basics

### Key claims

**LoRA paper**
- Paper: "LoRA: Low-Rank Adaptation of Large Language Models" by Hu et al. (2021).
- arXiv: 2106.09685. Confirmed live.
- Key numbers from abstract:
  - "LoRA can reduce the number of trainable parameters by 10,000 times" (compared to GPT-3 175B fine-tuned with Adam).
  - "GPU memory requirement by 3 times."
  - "LoRA performs on-par or better than fine-tuning in model quality on RoBERTa, DeBERTa, GPT-2, and GPT-3, despite having fewer trainable parameters, a higher training throughput, and, unlike adapters, no additional inference latency."
- Mechanism: "freezes the pre-trained model weights and injects trainable rank decomposition matrices into each layer of the Transformer architecture."

**HuggingFace PEFT docs**
- https://huggingface.co/docs/peft/index — confirmed live (HTTP 200).
- From the page: "PEFT (Parameter-Efficient Fine-Tuning) is a library for efficiently adapting large pretrained models to various downstream applications without fine-tuning all of a model's parameters because it is prohibitively costly. PEFT methods only fine-tune a small number of (extra) model parameters — significantly decreasing computational and storage costs — while yielding performance comparable to a fully fine-tuned model."
- The PEFT library supports: LoRA, AdaLoRA, IA3, LLaMA-Adapter, LoHa, LoKr, OFT, BOFT, prefix tuning, prompt tuning, P-tuning, and many others (confirmed from docs nav).
- Guidance on when to use LoRA vs full fine-tuning: UNVERIFIED — check before citing. The index page does not state this explicitly; it would need to be found in the conceptual guides section of PEFT docs.

**OpenAI fine-tuning guide**
- https://platform.openai.com/docs/guides/fine-tuning — HTTP 403 Cloudflare challenge. Content could not be verified.
- UNVERIFIED — check before citing: any claims about OpenAI's fine-tuning data requirements (commonly cited as "at least 10 examples; recommended 50-100+") need human verification from the live docs.

**Catastrophic forgetting**
- UNVERIFIED — check before citing: description of catastrophic forgetting (the model loses general capabilities when fine-tuned on narrow data) and mitigation strategies (rehearsal/replay, regularization, LoRA which preserves frozen base weights) were not confirmed from a primary source during this research pass.

### Verified sources
- https://arxiv.org/abs/2106.09685 — confirmed live (HTTP 200); "LoRA: Low-Rank Adaptation of Large Language Models" (Hu et al., 2021); abstract states 10,000x parameter reduction and 3x GPU memory reduction vs. full fine-tuning of GPT-3 175B
- https://huggingface.co/docs/peft/index — confirmed live (HTTP 200); PEFT library overview page, current version v0.18.0

### Dead/unverified URLs
- https://platform.openai.com/docs/guides/fine-tuning — HTTP 403 Cloudflare challenge; inaccessible from non-browser. Human verification required.

---

## AEE-206: Model Selection in Production

### Key claims

**Benchmark contamination risks**
- UNVERIFIED — check before citing: specific claims about benchmark contamination (training data overlap with test sets, inflated benchmark scores) were not confirmed from a primary source during this research pass. This is a well-documented concern but requires a primary source for citation.

**Anthropic prompt caching**
- Confirmed from docs (https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching, fetched 2026-04-14):
  - Cache write tokens (5-minute TTL): 1.25x base input token price (25% more than base).
  - Cache write tokens (1-hour TTL): 2x base input token price.
  - Cache read tokens: 0.1x base input token price (90% discount vs. base input tokens).
  - Minimum cacheable length: varies by model (e.g., 2048 tokens for Claude Sonnet 4.6; 4096 tokens for Claude Opus 4.6 and Claude Haiku 4.5; 1024 tokens for Claude Sonnet 4.5 and Opus 4).
  - Default TTL is 5 minutes. Optional 1-hour TTL available at 2x base price.
  - Up to 4 cache breakpoints per request.
  - 20-block lookback window for cache hit detection.
  - Available on Claude API and Azure AI Foundry (preview); Bedrock and Vertex AI support noted as "coming later."
- The docs do not state an overall "X% cost savings" figure; the savings depend on the ratio of cache reads to cache writes in a given workload. The 90% discount on cache reads is the primary pricing fact.

**LLM routing paper (arXiv 2410.10347)**
- Paper: "A Unified Approach to Routing and Cascading for LLMs" — confirmed live, fetched from arxiv.org.
- The abstract does NOT contain a "5x cost savings" claim. It states: "cascade routing consistently outperforms the individual approaches by a large margin." No specific cost multiplier is given in the abstract.
- CRITICAL FLAG: The "5x cost savings" claim that was cited in an earlier article is NOT supported by the abstract of this paper. Article writers must NOT cite "5x cost savings" from arXiv:2410.10347 without locating the specific passage in the full paper body. This may be a fabricated or misattributed statistic.

**TTFT vs. throughput**
- UNVERIFIED — check before citing: definitions of TTFT (time to first token, measures perceived responsiveness/latency from user perspective) and throughput (tokens per second, measures sustained generation speed) are standard but were not confirmed from a primary source during this research pass.

**LiteLLM**
- https://github.com/BerriAI/litellm — confirmed live (HTTP 200).
- Self-described as: "Open Source AI Gateway for 100+ LLMs. Self-hosted. Enterprise-ready. Call any LLM in OpenAI format."
- Addresses provider lock-in by providing a unified OpenAI-compatible interface across providers (Anthropic, OpenAI, Azure, etc.): `completion(model="anthropic/claude-sonnet-4-20250514", ...)` uses the same API as `completion(model="openai/gpt-4o", ...)`.
- Supports: chat completions, responses, embeddings, images, audio, batches, rerank, A2A protocol, MCP.
- Y Combinator W23 company.

**Artificial Analysis**
- https://artificialanalysis.ai/ — confirmed live (HTTP 200); provides live model benchmarks and performance comparisons.

### Verified sources
- https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching — confirmed live (HTTP 200); cache write costs 1.25x base for 5-min TTL, 2x for 1-hour TTL; cache reads cost 0.1x base; minimum lengths by model documented
- https://arxiv.org/abs/2410.10347 — confirmed live (HTTP 200); paper title is "A Unified Approach to Routing and Cascading for LLMs"; abstract contains NO "5x cost savings" claim
- https://github.com/BerriAI/litellm — confirmed live (HTTP 200); "Open Source AI Gateway for 100+ LLMs" with unified OpenAI-format API
- https://artificialanalysis.ai/ — confirmed live (HTTP 200); live model benchmark site

### Dead/unverified URLs
- None dead for AEE-206. All four URLs confirmed live.

---

## Cross-cutting notes for article writers

1. **OpenAI platform.openai.com docs are inaccessible via curl** (Cloudflare 403): structured outputs, fine-tuning, prompt engineering, and tokenizer playground all returned 403. A human must verify these in a browser before citing specific claims.

2. **The "5x cost savings" claim attributed to arXiv:2410.10347 is unsubstantiated** from the abstract. Do not use this figure without finding its exact location in the paper body (or another primary source).

3. **Anthropic docs (docs.anthropic.com) are accessible but JS-rendered**: content was extractable via text stripping. The system-prompts URL redirects to platform.claude.com — use the redirect destination for final citations.

4. **Lost in the Middle quantitative drop**: The abstract does not provide a specific percentage. The figure "~20 percentage points" appears in the literature but must be verified against the paper body before citing.

5. **Token ratio for non-English scripts and code**: No primary source confirmed during this research pass. These are commonly cited figures in the community but should be marked UNVERIFIED or traced to a primary benchmark.
