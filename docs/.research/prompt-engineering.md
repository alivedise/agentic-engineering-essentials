# AEE 300-306 Research Notes

Generated: 2026-04-14

---

## AEE-301: Prompt Structure Fundamentals

### Sources

1. `https://platform.openai.com/docs/guides/prompt-engineering`
   - **STATUS: CRITICAL FLAG — HTTP 403 Forbidden.** The URL returned a 403 error (Cloudflare or authentication block). Content could not be fetched or verified.
   - ARTICLE WRITER NOTE: platform.openai.com returned 403. Do not cite specific content, section titles, or tip numbers from this page without independently verifying. The page may require a logged-in session.

2. `https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview`
   - **STATUS: 301 redirect** to `https://platform.claude.com/docs/en/docs/build-with-claude/prompt-engineering/overview`, which in turn serves the **"Prompting best practices"** page at `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices`. Content verified.
   - The overview page itself is now thin: it links to the "Prompting best practices" guide as "the living reference" and mentions the Claude Console's prompt generator and improver tools. The substantive guidance has moved.

3. `https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/be-clear-and-direct`
   - **STATUS: 301 redirect** to `https://platform.claude.com/docs/en/docs/build-with-claude/prompt-engineering/be-clear-and-direct`, which served the full **"Prompting best practices"** page. Both Anthropic redirects landed on the same consolidated guide. Content verified.

### Key claims (verified from Anthropic docs)

**General principles from "Prompting best practices" (current Anthropic living reference):**

- **Be clear and direct:** Claude responds well to explicit instructions. Treat Claude like "a brilliant but new employee who lacks context on your norms and workflows." The golden rule: show your prompt to a colleague with minimal context — if they would be confused, Claude will be too.
  - Be specific about desired output format and constraints.
  - Provide instructions as sequential steps using numbered lists or bullet points when order matters.

- **Action verb framing (implicit):** The guide contrasts vague imperative ("Create an analytics dashboard") with an explicit directive that specifies scope and ambition ("Create an analytics dashboard. Include as many relevant features and interactions as possible. Go beyond the basics to create a fully-featured implementation."). Use affirmative action verbs stating what to do, not what to avoid.

- **Tell Claude what to do instead of what not to do:** Explicitly stated under format control. Example: instead of "Do not use markdown," use "Your response should be composed of smoothly flowing prose paragraphs."

- **Add context/motivation:** Explaining the reason behind an instruction helps Claude generalize. Example: "never use ellipses" is less effective than explaining that a TTS engine cannot pronounce them.

- **Use examples effectively (few-shot/multishot):** Described as "one of the most reliable ways to steer output format, tone, and structure." Recommendation: 3–5 examples, made relevant, diverse, and wrapped in `<example>` / `<examples>` XML tags.

- **Structure prompts with XML tags:** Use descriptive tag names (`<instructions>`, `<context>`, `<input>`) to reduce misinterpretation. Nest tags for hierarchical content.

- **Give Claude a role:** Set a role in the system prompt to focus behavior and tone.

- **Long context:** Put longform data at the top of the prompt (above instructions and query). Internal tests cited: "Queries at the end can improve response quality by up to 30% in tests, especially with complex, multi-document inputs." (ARTICLE WRITER NOTE: this figure is from Anthropic's own internal evaluation as described on the page; it is not from a peer-reviewed study.)

- **Format control specifics:**
  1. Tell Claude what to do instead of what not to do.
  2. Use XML format indicators (e.g., write prose inside `<smoothly_flowing_prose_paragraphs>` tags).
  3. Match prompt style to desired output style.
  4. Use detailed prompts for specific formatting preferences.

### Notes for article writer

- The OpenAI prompt engineering guide (platform.openai.com) was inaccessible (403). Do not attribute any specific numbered "strategies" or tips to that URL. If you need OpenAI content, use a cached/public mirror or the OpenAI Cookbook on GitHub instead.
- The Anthropic docs now redirect to `platform.claude.com`. The canonical URL for the living reference is: `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices`. The old `be-clear-and-direct` subpage URL no longer resolves to a separate page; the content is merged into the combined guide.
- The 30% quality improvement figure for putting queries at the end is from Anthropic's internal tests, not an independent study.
- The guide is written for Claude 4.x models (Opus 4.6, Sonnet 4.6, Haiku 4.5). Some advice (e.g., prefill deprecation, adaptive thinking) is Claude-specific.

---

## AEE-302: Chain-of-Thought Prompting

### Sources

1. `https://arxiv.org/abs/2201.11903` — Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"
   - **STATUS: Verified. Abstract fetched successfully.**

2. `https://arxiv.org/abs/2205.11916` — Kojima et al., "Large Language Models are Zero-Shot Reasoners"
   - **STATUS: Verified. Abstract fetched successfully.**

### Key claims

**Wei et al. (2201.11903) — abstract, verbatim as fetched:**

> "We explore how generating a chain of thought -- a series of intermediate reasoning steps -- significantly improves the ability of large language models to perform complex reasoning. In particular, we show how such reasoning abilities emerge naturally in sufficiently large language models via a simple method called chain of thought prompting, where a few chain of thought demonstrations are provided as exemplars in prompting. Experiments on three large language models show that chain of thought prompting improves performance on a range of arithmetic, commonsense, and symbolic reasoning tasks. The empirical gains can be striking. For instance, prompting a 540B-parameter language model with just eight chain of thought exemplars achieves state of the art accuracy on the GSM8K benchmark of math word problems, surpassing even finetuned GPT-3 with a verifier."

Verified specific numbers:
- Model size: 540B parameters
- Exemplars required: 8
- Benchmark: GSM8K (math word problems)
- Result: state-of-the-art, surpassing fine-tuned GPT-3 with a verifier
- Tasks: arithmetic, commonsense, and symbolic reasoning (three categories)
- Models tested: three large language models

ARTICLE WRITER NOTE: The abstract does not state a specific percentage accuracy number for GSM8K (e.g., "X% accuracy"). Do not fabricate a specific percentage — only cite "state of the art on GSM8K, surpassing fine-tuned GPT-3 with a verifier."

**Kojima et al. (2205.11916) — abstract, verbatim as fetched:**

> "Pretrained large language models (LLMs) are widely used in many sub-fields of natural language processing (NLP) and generally known as excellent few-shot learners with task-specific exemplars. Notably, chain of thought (CoT) prompting, a recent technique for eliciting complex multi-step reasoning through step-by-step answer examples, achieved the state-of-the-art performances in arithmetics and symbolic reasoning, difficult system-2 tasks that do not follow the standard scaling laws for LLMs. While these successes are often attributed to LLMs' ability for few-shot learning, we show that LLMs are decent zero-shot reasoners by simply adding "Let's think step by step" before each answer. Experimental results demonstrate that our Zero-shot-CoT, using the same single prompt template, significantly outperforms zero-shot LLM performances on diverse benchmark reasoning tasks including arithmetics (MultiArith, GSM8K, AQUA-RAT, SVAMP), symbolic reasoning (Last Letter, Coin Flip), and other logical reasoning tasks (Date Understanding, Tracking Shuffled Objects), without any hand-crafted few-shot examples, e.g. increasing the accuracy on MultiArith from 17.7% to 78.7% and GSM8K from 10.4% to 40.7% with large InstructGPT model (text-davinci-002), as well as similar magnitudes of improvements with another off-the-shelf large model, 540B parameter PaLM. The versatility of this single prompt across very diverse reasoning tasks hints at untapped and understudied fundamental zero-shot capabilities of LLMs, suggesting high-level, multi-task broad cognitive capabilities may be extracted by simple prompting."

Verified specific numbers:
- Prompt used: "Let's think step by step"
- MultiArith: 17.7% → 78.7% (zero-shot, InstructGPT / text-davinci-002)
- GSM8K: 10.4% → 40.7% (zero-shot, InstructGPT / text-davinci-002)
- Similar gains also seen with PaLM 540B
- Tasks: MultiArith, GSM8K, AQUA-RAT, SVAMP (arithmetic); Last Letter, Coin Flip (symbolic); Date Understanding, Tracking Shuffled Objects (logical)

### Notes for article writer

- Wei et al. does not state a percentage for GSM8K accuracy in the abstract. The key claim is "state of the art, surpassing fine-tuned GPT-3 with a verifier." Full accuracy numbers appear in the paper body, not in the abstract.
- Kojima et al.'s headline result (17.7% → 78.7% on MultiArith) is for the zero-shot setting using InstructGPT. Ensure the model and setting are cited correctly when quoting these numbers.
- Both papers are from 2022 and evaluate models that predate GPT-4. Performance baselines will appear dated.

---

## AEE-303: Few-Shot Prompting

### Sources

1. `https://arxiv.org/abs/2005.14165` — Brown et al., "Language Models are Few-Shot Learners" (GPT-3)
   - **STATUS: Verified. Abstract fetched successfully.**

2. `https://arxiv.org/abs/2202.12837` — Min et al., "Rethinking the Role of Demonstrations: What Makes In-Context Learning Work?"
   - **STATUS: Verified. Abstract fetched successfully.**

### Key claims

**Brown et al. (2005.14165) — abstract, verbatim as fetched:**

> "Recent work has demonstrated substantial gains on many NLP tasks and benchmarks by pre-training on a large corpus of text followed by fine-tuning on a specific task. While typically task-agnostic in architecture, this method still requires task-specific fine-tuning datasets of thousands or tens of thousands of examples. By contrast, humans can generally perform a new language task from only a few examples or from simple instructions - something which current NLP systems still largely struggle to do. Here we show that scaling up language models greatly improves task-agnostic, few-shot performance, sometimes even reaching competitiveness with prior state-of-the-art fine-tuning approaches. Specifically, we train GPT-3, an autoregressive language model with 175 billion parameters, 10x more than any previous non-sparse language model, and test its performance in the few-shot setting. For all tasks, GPT-3 is applied without any gradient updates or fine-tuning, with tasks and few-shot demonstrations specified purely via text interaction with the model. GPT-3 achieves strong performance on many NLP datasets, including translation, question-answering, and cloze tasks, as well as several tasks that require on-the-fly reasoning or domain adaptation, such as unscrambling words, using a novel word in a sentence, or performing 3-digit arithmetic. At the same time, we also identify some datasets where GPT-3's few-shot learning still struggles, as well as some datasets where GPT-3 faces methodological issues related to training on large web corpora. Finally, we find that GPT-3 can generate samples of news articles which human evaluators have difficulty distinguishing from articles written by humans. We discuss broader societal impacts of this finding and of GPT-3 in general."

Verified facts:
- GPT-3: 175B parameters, 10x larger than any previous non-sparse LM at time of publication
- Evaluated in pure few-shot setting (no gradient updates, no fine-tuning)
- Reaches "competitiveness with prior state-of-the-art fine-tuning approaches" on some tasks
- Tasks: translation, QA, cloze, reasoning, domain adaptation
- Human evaluators could not reliably distinguish GPT-3 news from human-written news

ARTICLE WRITER NOTE: The abstract does not provide specific accuracy numbers (e.g., SuperGLUE scores). Specific benchmark numbers appear in the paper body. Do not cite specific accuracy percentages without consulting the paper body or supplementary materials.

**Min et al. (2202.12837) — abstract, verbatim as fetched:**

> "Large language models (LMs) are able to in-context learn -- perform a new task via inference alone by conditioning on a few input-label pairs (demonstrations) and making predictions for new inputs. However, there has been little understanding of how the model learns and which aspects of the demonstrations contribute to end task performance. In this paper, we show that ground truth demonstrations are in fact not required -- randomly replacing labels in the demonstrations barely hurts performance on a range of classification and multi-choice tasks, consistently over 12 different models including GPT-3. Instead, we find that other aspects of the demonstrations are the key drivers of end task performance, including the fact that they provide a few examples of (1) the label space, (2) the distribution of the input text, and (3) the overall format of the sequence. Together, our analysis provides a new way of understanding how and why in-context learning works, while opening up new questions about how much can be learned from large language models through inference alone."

Verified key finding on label correctness vs. format:
- "Randomly replacing labels in the demonstrations barely hurts performance" — ground-truth labels are not required
- Tested consistently across 12 models including GPT-3
- Tasks: classification and multi-choice tasks
- The three drivers that DO matter: (1) exposing the label space, (2) showing the distribution of input text, (3) establishing the overall format/sequence structure
- Implication: in-context learning works primarily through structural and distributional cues, not through correct label semantics

### Notes for article writer

- Brown et al. (GPT-3 paper) is the foundational few-shot learning reference but does not give a single headline accuracy number in the abstract. Articles that quote specific numbers from this paper should cite the specific benchmark and table from the paper body.
- Min et al.'s finding is counterintuitive and high-impact: correct labels matter less than format and distribution. This should be stated carefully — it does not mean labels are irrelevant to model training, only to in-context demonstrations at inference time.
- Min et al. was evaluated on classification and multi-choice tasks; generalization to open-ended generation is not established in this abstract.

---

## AEE-304: Instruction Following

### Sources

1. `https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/be-clear-and-direct`
   - **STATUS: 301 redirect.** See AEE-301 notes. The URL redirects to the same "Prompting best practices" consolidated page. Content verified via redirect target.

2. `https://arxiv.org/abs/2211.01910` — Zhou et al., "Large Language Models Are Human-Level Prompt Engineers"
   - **STATUS: Verified. Abstract fetched successfully.**

### Key claims

**Anthropic "be-clear-and-direct" (now consolidated into Prompting best practices) — verified guidance:**

- Treat Claude like "a brilliant but new employee" lacking context on norms and workflows.
- Golden rule: if a colleague with minimal context would be confused by the prompt, Claude will be too.
- Provide instructions as sequential numbered steps or bullets when order or completeness matters.
- Tell Claude what to do (affirmative action verbs) rather than what not to do.
- Add context/motivation: "never use ellipses" is weaker than explaining why (TTS engine cannot pronounce them). Claude generalizes from the explanation.
- Be explicit about "above and beyond" behavior if desired — do not assume Claude will infer ambition from vague prompts.
- For tool use / agentic contexts: "Can you suggest some changes?" will elicit suggestions, not implementations. Use imperative: "Change this function to improve its performance."

**Zhou et al. (2211.01910) — abstract, verbatim as fetched:**

> "By conditioning on natural language instructions, large language models (LLMs) have displayed impressive capabilities as general-purpose computers. However, task performance depends significantly on the quality of the prompt used to steer the model, and most effective prompts have been handcrafted by humans. Inspired by classical program synthesis and the human approach to prompt engineering, we propose Automatic Prompt Engineer (APE) for automatic instruction generation and selection. In our method, we treat the instruction as the "program," optimized by searching over a pool of instruction candidates proposed by an LLM in order to maximize a chosen score function. To evaluate the quality of the selected instruction, we evaluate the zero-shot performance of another LLM following the selected instruction. Experiments on 24 NLP tasks show that our automatically generated instructions outperform the prior LLM baseline by a large margin and achieve better or comparable performance to the instructions generated by human annotators on 19/24 tasks. We conduct extensive qualitative and quantitative analyses to explore the performance of APE. We show that APE-engineered prompts can be applied to steer models toward truthfulness and/or informativeness, as well as to improve few-shot learning performance by simply prepending them to standard in-context learning prompts."

Verified facts:
- Method: Automatic Prompt Engineer (APE) — treats instruction as a program, optimizes via LLM-generated candidate pool
- Evaluation: zero-shot performance of a second LLM on the selected instruction
- Benchmark: 24 NLP tasks
- Result: outperforms "prior LLM baseline by a large margin"; matches or beats human-annotator instructions on 19/24 tasks (79%)
- Additional capability: APE prompts can steer toward truthfulness/informativeness and improve few-shot learning when prepended

ARTICLE WRITER NOTE: The paper title "Large Language Models Are Human-Level Prompt Engineers" is a claim about APE's performance relative to human annotators on the 24-task benchmark, not a general claim. The abstract says "better or comparable" on 19/24 tasks, meaning APE underperformed humans on 5/24 tasks. Use this nuance when citing.

### Notes for article writer

- The Anthropic "be-clear-and-direct" URL no longer resolves to a standalone page. Cite `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices` as the canonical source.
- Zhou et al. is about automated prompt generation (APE), not instruction following per se. Its relevance to AEE-304 is demonstrating that prompt quality materially affects task performance, and that LLM-generated prompts can reach human-level quality — supporting the importance of careful instruction engineering.
- The paper's "human-level" claim applies to the specific 24-task NLP benchmark, not to general instruction following.

---

## AEE-305: Self-Consistency and Ensembling

### Sources

1. `https://arxiv.org/abs/2203.11171` — Wang et al., "Self-Consistency Improves Chain of Thought Reasoning in Language Models"
   - **STATUS: Verified. Abstract fetched successfully.**

### Key claims

**Wang et al. (2203.11171) — abstract, verbatim as fetched:**

> "Chain-of-thought prompting combined with pre-trained large language models has achieved encouraging results on complex reasoning tasks. In this paper, we propose a new decoding strategy, self-consistency, to replace the naive greedy decoding used in chain-of-thought prompting. It first samples a diverse set of reasoning paths instead of only taking the greedy one, and then selects the most consistent answer by marginalizing out the sampled reasoning paths. Self-consistency leverages the intuition that a complex reasoning problem typically admits multiple different ways of thinking leading to its unique correct answer. Our extensive empirical evaluation shows that self-consistency boosts the performance of chain-of-thought prompting with a striking margin on a range of popular arithmetic and commonsense reasoning benchmarks, including GSM8K (+17.9%), SVAMP (+11.0%), AQuA (+12.2%), StrategyQA (+6.4%) and ARC-challenge (+3.9%)."

Verified specific numbers:
- GSM8K: +17.9% over chain-of-thought with greedy decoding
- SVAMP: +11.0%
- AQuA: +12.2%
- StrategyQA: +6.4%
- ARC-challenge: +3.9%
- Tasks: arithmetic (GSM8K, SVAMP, AQuA) and commonsense reasoning (StrategyQA, ARC-challenge)

Regarding N (number of sampled reasoning paths):
- **CRITICAL FLAG — N not stated in abstract.** The abstract does not specify what N (number of sampled paths) was used for these results, nor does it recommend a specific N. Do not cite a specific N value without consulting the paper body. Article writers claiming "N=40" or any other specific value must verify from the paper body or supplementary materials, not the abstract alone.

Core mechanism (from abstract):
- Replace greedy decoding with sampling a diverse set of reasoning paths
- Select the most consistent answer by marginalizing over sampled paths
- Intuition: complex problems admit multiple valid reasoning routes converging on the same answer

### Notes for article writer

- The accuracy improvements are stated as absolute percentage point gains over CoT with greedy decoding. Ensure this framing is preserved — these are not absolute accuracy numbers but gains over a baseline.
- N is not given in the abstract. Common practice in the literature is N=40 samples, but this cannot be confirmed from the abstract alone. CRITICAL FLAG: do not state a specific N in the article without citing the specific table in the paper body.
- The paper covers arithmetic and commonsense reasoning only. The abstract does not claim results on coding, creative, or other task types.

---

## AEE-306: Prompt Robustness Testing

### Sources

1. `https://arxiv.org/abs/2306.04528` — Zhu et al., "PromptBench: Towards Evaluating the Robustness of Large Language Models on Adversarial Prompts"
   - **STATUS: Verified. Abstract fetched successfully.**
   - NOTE: The abstract page refers to the benchmark as "PromptRobust" in the fetched content summary, but the paper title includes "PromptBench." The arXiv page for 2306.04528 was verified live. ARTICLE WRITER NOTE: Cross-check the final published name of the benchmark (PromptBench vs. PromptRobust) against the paper title and body before publication.

### Key claims

**Zhu et al. (2306.04528) — abstract, verbatim as fetched:**

> "The increasing reliance on Large Language Models (LLMs) across academia and industry necessitates a comprehensive understanding of their robustness to prompts. In response to this vital need, we introduce PromptRobust, a robustness benchmark designed to measure LLMs' resilience to adversarial prompts. This study uses a plethora of adversarial textual attacks targeting prompts across multiple levels: character, word, sentence, and semantic. The adversarial prompts, crafted to mimic plausible user errors like typos or synonyms, aim to evaluate how slight deviations can affect LLM outcomes while maintaining semantic integrity. These prompts are then employed in diverse tasks including sentiment analysis, natural language inference, reading comprehension, machine translation, and math problem-solving. Our study generates 4,788 adversarial prompts, meticulously evaluated over 8 tasks and 13 datasets. Our findings demonstrate that contemporary LLMs are not robust to adversarial prompts. Furthermore, we present a comprehensive analysis to understand the mystery behind prompt robustness and its transferability. We then offer insightful robustness analysis and pragmatic recommendations for prompt composition, beneficial to both researchers and everyday users."

Verified facts:
- Benchmark name in abstract: "PromptRobust" (the arXiv page title includes "PromptBench" — see note above)
- Adversarial attack levels: character, word, sentence, semantic (four levels)
- Attack design: mimics plausible user errors (typos, synonyms) while maintaining semantic integrity
- Tasks covered: sentiment analysis, natural language inference, reading comprehension, machine translation, math problem-solving
- Scale: 4,788 adversarial prompts, 8 tasks, 13 datasets
- Core finding: "contemporary LLMs are not robust to adversarial prompts"
- Additional output: robustness analysis and recommendations for prompt composition

Regarding specific accuracy drops:
- **CRITICAL FLAG — no specific accuracy drop numbers in abstract.** The abstract states LLMs "are not robust" but does not give specific percentage accuracy drops in the abstract text. Do not attribute specific drop numbers (e.g., "accuracy dropped by X%") without consulting the paper body or figures.

Variation types tested (verified from abstract):
1. Character-level modifications
2. Word-level alterations
3. Sentence-level perturbations
4. Semantic-level changes

### Notes for article writer

- Benchmark naming inconsistency: the abstract calls it "PromptRobust" while the arXiv page title says "PromptBench." This should be resolved by checking the paper's own introduction section before publication.
- Specific accuracy drop numbers are not in the abstract. Claims like "accuracy dropped by 35%" require a citation to a specific table in the paper body.
- The code repository is available (referenced as Microsoft GitHub in the fetched content), making this a reproducible benchmark — worth noting as a practical resource.
- The 4,788 prompt count and 8 tasks / 13 datasets scale can be cited as verified from the abstract.

---

## Summary of CRITICAL FLAGS

| Flag | Article | Issue |
|------|---------|-------|
| CRITICAL FLAG | AEE-301 | `platform.openai.com/docs/guides/prompt-engineering` returned HTTP 403. Content not verified. Do not cite. |
| CRITICAL FLAG | AEE-301 | Anthropic docs URLs now 301-redirect; canonical source is `platform.claude.com`. The `be-clear-and-direct` subpage no longer exists separately. |
| CRITICAL FLAG | AEE-302 | Wei et al. abstract does not state a specific GSM8K accuracy percentage. Do not fabricate one. |
| CRITICAL FLAG | AEE-305 | Wang et al. abstract does not state N (number of sampled paths). Do not cite a specific N without checking the paper body. |
| CRITICAL FLAG | AEE-306 | Zhu et al. abstract does not state specific accuracy drop percentages. Do not fabricate numbers. |
| CRITICAL FLAG | AEE-306 | Benchmark name discrepancy: abstract says "PromptRobust," arXiv title says "PromptBench." Verify before publication. |
