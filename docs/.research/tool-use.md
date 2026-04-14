# Tool Use & Execution Research Notes

## AEE-401: Function Calling

### Verified Sources

- `https://platform.claude.com/docs/en/docs/tool-use` (redirected from `https://docs.anthropic.com/en/docs/tool-use`) — Anthropic tool use overview: client vs server tools, stop reason, pricing table, benchmarks
- `https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools` — Tool definition format, best practices, tool_choice, input_examples
- `https://platform.claude.com/docs/en/agents-and-tools/tool-use/handle-tool-calls` — tool_use block format, tool_result format, is_error, error handling, formatting rules
- `https://platform.claude.com/docs/en/agents-and-tools/tool-use/parallel-tool-use` — Parallel tool call mechanics, disable_parallel_tool_use, message formatting

### Key Facts

#### Tool Definition Format

Specified in the top-level `tools` array of the API request. Each tool object has:

```json
{
  "name": "get_weather",
  "description": "Get the current weather in a given location",
  "input_schema": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "The city and state, e.g. San Francisco, CA"
      },
      "unit": {
        "type": "string",
        "enum": ["celsius", "fahrenheit"],
        "description": "The unit of temperature, either 'celsius' or 'fahrenheit'"
      }
    },
    "required": ["location"]
  }
}
```

Mandatory fields: `name`, `description`, `input_schema`.
Optional fields: `input_examples`, `cache_control`, `strict`, `defer_loading`, `allowed_callers`.

Name constraint: must match `^[a-zA-Z0-9_-]{1,64}$`.

#### tool_use Content Block (Model Response)

When the model decides to call a tool, the response has `stop_reason: "tool_use"` and one or more `tool_use` blocks in `content`:

```json
{
  "id": "msg_01Aq9w938a90dw8q",
  "model": "claude-opus-4-6",
  "stop_reason": "tool_use",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "I'll check the current weather in San Francisco for you."
    },
    {
      "type": "tool_use",
      "id": "toolu_01A09q90qw90lq917835lq9",
      "name": "get_weather",
      "input": { "location": "San Francisco, CA", "unit": "celsius" }
    }
  ]
}
```

Fields on a `tool_use` block: `type` ("tool_use"), `id` (unique identifier for matching results), `name`, `input`.

#### tool_result Format (User Message)

After executing the tool, return results as a `user` message with `tool_result` block(s):

```json
{
  "role": "user",
  "content": [
    {
      "type": "tool_result",
      "tool_use_id": "toolu_01A09q90qw90lq917835lq9",
      "content": "15 degrees"
    }
  ]
}
```

Content can be a plain string, a list of `text`/`image`/`document` blocks, or omitted entirely (empty result). The `tool_result` block must come FIRST in the content array; any supplementary text must come after.

#### is_error Flag

Set `"is_error": true` on a `tool_result` to signal a tool execution failure:

```json
{
  "type": "tool_result",
  "tool_use_id": "toolu_01A09q90qw90lq917835lq9",
  "content": "ConnectionError: the weather service API is not available (HTTP 500)",
  "is_error": true
}
```

Claude incorporates the error message and responds accordingly. If a tool call is invalid/missing parameters, Claude will retry 2-3 times with corrections before giving up.

#### Stop Reason Values

- `tool_use` — model wants to call one or more client tools; your app must execute and return results
- `end_turn` — model finished its response normally

Server tools (web_search, code_execution, web_fetch, tool_search) run on Anthropic's infrastructure; no `tool_use` stop_reason is emitted for those.

#### Parallel Tool Calls

Claude may emit multiple `tool_use` blocks in a single response. All results must be returned in a **single** `user` message (not separate messages):

```json
// CORRECT: all results in one user message
[
  {"role": "assistant", "content": [tool_use_1, tool_use_2]},
  {"role": "user", "content": [tool_result_1, tool_result_2]}
]

// WRONG: results split across messages (breaks future parallel calls)
[
  {"role": "assistant", "content": [tool_use_1, tool_use_2]},
  {"role": "user", "content": [tool_result_1]},
  {"role": "user", "content": [tool_result_2]}
]
```

To disable parallel tool use: set `disable_parallel_tool_use=true` on the `tool_choice` parameter. When `tool_choice` type is `auto`, this ensures at most one tool; when type is `any` or `tool`, this ensures exactly one tool.

#### tool_choice Options

- `auto` (default when tools provided) — Claude decides whether to call a tool
- `any` — Claude must use one of the provided tools (but Claude picks which)
- `tool` — forces a specific tool: `{"type": "tool", "name": "get_weather"}`
- `none` (default when no tools) — prevents tool use

Note: `any` and `tool` prefill the assistant message to force tool use; natural language before the tool_use block will not appear. With extended thinking, only `auto` and `none` are supported.

#### Pricing — Tool System Prompt Token Overhead

Tool use automatically adds a special system prompt. For Claude Opus 4.6, Sonnet 4.6, Haiku 4.5 (and most Claude 4.x / 3.7 models): `auto`/`none` = 346 tokens; `any`/`tool` = 313 tokens.

---

## AEE-402: Tool Schema Design

### Verified Sources

- `https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools` — schema guidance, naming conventions, description best practices, tool_choice, input_examples
- `https://json-schema.org/understanding-json-schema/` — JSON Schema reference (overview page only; detailed type specs are on sub-pages)

### Key Facts

#### JSON Schema Subset Accepted

The `input_schema` field accepts a JSON Schema object. Confirmed supported types and keywords from Anthropic API examples:

- `type`: `"object"`, `"string"`, `"integer"`, `"number"`, `"boolean"`, `"array"`, `"null"`
- `properties` — object property definitions
- `required` — array of required property names
- `enum` — array of allowed values (e.g., `["celsius", "fahrenheit"]`)
- `description` — per-property description strings
- Nested objects and arrays (demonstrated in SDK examples)

The `input_schema` root must be `"type": "object"`. Strict mode (`strict: true` on the tool definition) guarantees inputs always match the schema exactly.

#### Tool Naming Conventions (Anthropic Guidance)

From the define-tools page:
- Name must match regex `^[a-zA-Z0-9_-]{1,64}$` (alphanumeric, underscore, hyphen; max 64 chars)
- Use meaningful namespacing when tools span multiple services: prefix with the service name (e.g., `github_list_prs`, `slack_send_message`)
- Consolidate related operations into fewer tools with an `action` parameter rather than one tool per action (e.g., one `pull_request` tool with `action: create|review|merge` instead of `create_pr`, `review_pr`, `merge_pr`)

#### Tool Description Best Practices (Anthropic Guidance)

From the define-tools page (these are the most important factor in tool performance):
- Explain **what** the tool does
- Explain **when** it should be used (and when it should not)
- Explain **what each parameter** means and how it affects behavior
- Include **any caveats or limitations** (e.g., what information it does not return)
- Aim for at least 3-4 sentences per description; more for complex tools

Good example:
```json
{
  "name": "get_stock_price",
  "description": "Retrieves the current stock price for a given ticker symbol. The ticker symbol must be a valid symbol for a publicly traded company on a major US stock exchange like NYSE or NASDAQ. The tool will return the latest trade price in USD. It should be used when the user asks about the current or most recent price of a specific stock. It will not provide any other information about the stock or company."
}
```

Bad example (too brief): `"description": "Gets the stock price for a ticker."`

#### input_examples Field

Optional field on tool definitions (not supported for server tools). Each example must be valid per the tool's `input_schema`. Token cost: ~20–50 tokens for simple examples, ~100–200 tokens for complex nested objects.

#### Tool Response Design

Return only high-signal information. Use semantic, stable identifiers (slugs or UUIDs) rather than opaque internal references. Include only fields Claude needs for its next step.

#### Model Selection for Tool Use

- Use latest Claude Opus (4.6) for complex tools and ambiguous queries — handles multiple tools better, seeks clarification when parameters are missing
- Claude Haiku models work for straightforward tools but may infer/guess missing parameters

### CRITICAL FLAGS

- The `https://json-schema.org/understanding-json-schema/` page is a navigation hub; it does not enumerate all supported JSON Schema types inline. The full type reference is on sub-pages. The complete set of JSON Schema features supported by Anthropic's API is not enumerated in a single primary source — the list above is inferred from API examples and SDK code. Article writers should verify against the Anthropic API schema validation error messages or the tool-reference page for a definitive list.

---

## AEE-403: Code Execution

### Verified Sources

- `https://e2b.dev/docs` — E2B overview: Python SDK `Sandbox` class, `Sandbox.create()`, `sandbox.commands.run()`, `sandbox.run_code()`
- `https://e2b.dev/docs/quickstart` — Quickstart: `Sandbox.create()`, `sandbox.run_code()`, `sandbox.files.list()`
- `https://e2b.mintlify.app/llms.txt` — E2B llms.txt: filesystem isolation, network egress rules, process management, supported languages
- `https://modal.com/docs` — Modal overview: serverless, sub-second cold starts, GPU support, auto-scaling
- `https://modal.com/docs/guide/sandboxes` — Modal Sandboxes: `Sandbox.create()`, lifecycle, timeouts, resource configuration

### Key Facts

#### E2B Python SDK

Installation: `pip install e2b`

Core class: `Sandbox`

```python
from e2b import Sandbox

# Create a sandbox (requires E2B_API_KEY env var)
sandbox = Sandbox.create()

# Run a shell command
result = sandbox.commands.run('echo "Hello from E2B Sandbox!"')
print(result.stdout)

# Run Python code directly
execution = sandbox.run_code("print('hello world')")
```

Supported code execution languages (from llms.txt): Python, JavaScript, TypeScript, Java, Bash, R.

SDK also exposes: `sandbox.files.list(path)` for filesystem access, file upload/download, directory watching.

#### E2B Isolation

From the docs and llms.txt:
- **Filesystem**: Full filesystem isolation (each sandbox is a Linux VM with its own filesystem); file upload/download supported
- **Network**: Network egress rules are configurable ("Put sandboxes network"); full network access by default (internet reachable), egress can be restricted
- **Process**: Process isolation via VM boundary; process listing, signaling, and stdin/stdout control available
- Sandbox described as "a fast, secure Linux VM created on demand"

#### E2B Cold Start and Resource Limits

CRITICAL FLAG — Cold start latency is not documented on the pages fetched. The e2b.dev/docs pages fetched do not state a specific cold start figure. Multiple sub-pages returned 404 (`/docs/getting-started/python`, `/docs/sandbox/overview`, `/docs/sandbox/sandboxes`, `/docs/hello-world/py`). Resource limits are not documented in the pages accessible.

#### Modal Overview

Modal is a serverless cloud for compute-intensive applications. Key properties:
- **Cold start**: Sub-second cold starts documented (no exact ms figure given)
- **GPU support**: CUDA support, dynamic GPU allocation across major clouds; GPU type specified in code (no YAML/config files)
- **Auto-scaling**: Automatic container scaling under load
- **Pricing**: Charged per second of actual usage
- **Python API**: Decorator-based (`@app.function`, `modal.Function`) — everything including container environments and GPU spec is expressed in Python

#### Modal Sandboxes (for Untrusted Code Execution)

`modal.Sandbox` is a dedicated primitive for executing untrusted/arbitrary code at runtime:

```python
import modal

app = modal.App.lookup("my-app", create_if_missing=True)
sb = modal.Sandbox.create(app=app)
# ... run commands in sandbox
```

- Default timeout: 5 minutes
- Maximum configurable timeout: 24 hours
- Idle timeout: automatic termination after inactivity (no active commands, stdin writes, or TCP connections)
- Supports readiness probes (TCP probe, exec probe)
- Supports nearly all `modal.Function` configurations: custom images, volumes, environment variables
- Images and mounts can be defined dynamically at runtime (useful for LLM-generated code specifying its own dependencies)
- Tagged with key-value pairs for filtering
- `sandbox.detach()` recommended for cleanup

#### Comparison

| | E2B | Modal Sandbox |
|---|---|---|
| Isolation | Linux VM per sandbox | Container per sandbox |
| Cold start | Not documented (CRITICAL FLAG) | Sub-second (no exact ms) |
| Default timeout | Not documented (CRITICAL FLAG) | 5 minutes |
| Max timeout | Not documented (CRITICAL FLAG) | 24 hours |
| GPU support | Not mentioned | Yes (CUDA) |
| Language support | Python, JS, TS, Java, Bash, R | Any (arbitrary containers) |

### CRITICAL FLAGS

- `https://e2b.dev/docs/getting-started/python` — **404 NOT FOUND**. Multiple E2B sub-pages returned 404: `/docs/getting-started/python`, `/docs/sandbox/overview`, `/docs/sandbox/sandboxes`, `/docs/hello-world/py`. E2B docs may have been restructured.
- E2B cold start latency: not found in any accessible E2B page. Any numeric claim (e.g., "150ms cold start") cannot be verified from primary source.
- E2B resource limits (CPU, RAM, disk): not documented in accessible pages.
- Modal cold start: documented as "sub-second" but no specific millisecond figure given in accessible pages.

---

## AEE-404: Browser and Computer Use

### Verified Sources

- `https://platform.claude.com/docs/en/docs/build-with-claude/computer-use` (redirected from `https://docs.anthropic.com/en/docs/build-with-claude/computer-use`) — Full computer use documentation: tools, actions, beta headers, safety, agent loop, environment setup
- `https://playwright.dev/docs/intro` — Playwright intro: browsers supported, system requirements, installation, key features
- `https://github.com/browser-use/browser-use` — browser-use README: Python API, features over raw Playwright, benchmarks

### Key Facts

#### Anthropic Computer Use API

**Beta headers** (required):
- `"computer-use-2025-11-24"` — for Claude Opus 4.6, Claude Sonnet 4.6, Claude Opus 4.5
- `"computer-use-2025-01-24"` — for Claude Sonnet 4.5, Haiku 4.5, Opus 4.1, Sonnet 4, Opus 4, Sonnet 3.7

**Current models**: Claude Opus 4.6 and Claude Sonnet 4.6 use the newer `computer_20251124` tool type.

**Available tools** (specified in the `tools` array):

1. `computer_20251124` (for Opus 4.6, Sonnet 4.6, Opus 4.5) — requires `display_width_px`, `display_height_px`; optional `display_number`, `enable_zoom`
2. `computer_20250124` (for Claude 4 models and Sonnet 3.7) — earlier version, lacks zoom
3. `text_editor_20250728` — text editing tool (name: `str_replace_based_edit_tool`)
4. `bash_20250124` — bash shell tool

Example API call:
```json
{
  "model": "claude-opus-4-6",
  "max_tokens": 1024,
  "tools": [
    {
      "type": "computer_20251124",
      "name": "computer",
      "display_width_px": 1024,
      "display_height_px": 768,
      "display_number": 1
    },
    { "type": "text_editor_20250728", "name": "str_replace_based_edit_tool" },
    { "type": "bash_20250124", "name": "bash" }
  ]
}
```

**Available actions** for `computer_20251124`:

Basic (all versions):
- `screenshot` — capture current display
- `left_click` — click at `[x, y]` coordinates
- `type` — type a text string
- `key` — press key or key combination (e.g., `"ctrl+s"`)
- `mouse_move` — move cursor to coordinates

Available from `computer_20250124` (Claude 4 / Sonnet 3.7):
- `scroll` — scroll in any direction with amount control
- `left_click_drag` — click and drag between coordinates
- `right_click`, `middle_click`
- `double_click`, `triple_click`
- `left_mouse_down`, `left_mouse_up`
- `hold_key` — hold key for a specified duration in seconds
- `wait`

Additional in `computer_20251124`:
- `zoom` — view a specific screen region at full resolution (requires `enable_zoom: true` in tool definition); takes `region: [x1, y1, x2, y2]`

Modifier keys with click/scroll: pass `"text": "shift"` (or `"ctrl"`, `"alt"`, `"super"`) on click/scroll actions.

**Screenshot-Action Loop Mechanics:**
1. Add computer use tool + user prompt to API request
2. Claude returns `stop_reason: "tool_use"` with a `tool_use` block (e.g., `{"action": "screenshot"}`)
3. App executes the action in the virtual environment, captures result (screenshot or command output)
4. App returns result as `tool_result` in a new `user` message
5. Claude analyzes result, decides next action or declares done
6. Loop repeats until no `tool_use` in response (task complete) or max iterations reached

**System prompt token overhead**: computer use beta adds 466–499 tokens to system prompt; computer use tool definition = 735 tokens per tool (Claude 4.x and Sonnet 3.7).

**Environment setup**: Virtual X11 display (Xvfb), lightweight Linux desktop (Mutter + Tint2 panel), Docker container. Reference implementation: `https://github.com/anthropics/anthropic-quickstarts/tree/main/computer-use-demo`.

#### Safety Recommendations (Anthropic)

From the computer use documentation:
1. Use a dedicated VM or container with minimal privileges
2. Avoid giving Claude access to sensitive data or account login credentials
3. Limit internet access to an allowlist of domains
4. Require human confirmation for meaningful real-world consequences (financial transactions, accepting cookies, agreeing to TOS)
5. Prompt injection risk: Claude may follow instructions found in webpage content or images; built-in classifiers flag potential injections and prompt for user confirmation before proceeding
6. For high-resolution screens (>1568px longest edge or >~1.15MP total), scale screenshots down before sending and scale coordinates back up before executing

**WebArena benchmark**: Claude achieves state-of-the-art results among single-agent systems for autonomous web navigation.

#### Playwright

From `https://playwright.dev/docs/intro` (live, HTTP 308 redirect to HTTPS then 200):
- Supports Chromium, WebKit, Firefox
- Platforms: Windows, Linux, macOS (local and CI)
- Node.js 20.x, 22.x, 24.x; macOS 14+; Linux Debian 12/13 or Ubuntu 22.04/24.04
- Headed and headless modes; mobile emulation (Chrome for Android, Mobile Safari)
- Features: built-in test runner, assertions, test isolation, parallelization, HTML reports, UI Mode (time travel debugging), Trace viewer
- Installation: `npm init playwright@latest`
- Also offers VS Code extension

#### browser-use

From `https://github.com/browser-use/browser-use` README:
- Open-source Python library for LLM-driven browser automation
- Adds AI decision-making on top of Playwright; translates natural language task descriptions into browser actions without manual step-by-step scripting

```python
from browser_use import Agent, Browser, ChatBrowserUse

agent = Agent(
    task="your objective here",
    llm=ChatBrowserUse(),
    browser=Browser()
)
await agent.run()
```

- Supports swappable LLM providers: Google Gemini, Anthropic Claude, OpenAI
- Custom tools and system message extensions supported
- Benchmark: "100 real-world browser tasks"; `ChatBrowserUse` (their fine-tuned model) claimed "3-5x faster than other models with SOTA accuracy"
- Pricing for ChatBrowserUse model: $0.20/1M input tokens, $0.02/1M cached input, $2.00/1M output

### CRITICAL FLAGS

- The `https://docs.anthropic.com/en/docs/build-with-claude/computer-use` URL redirects (301) to `https://platform.claude.com/docs/en/docs/build-with-claude/computer-use` — the content was successfully fetched from the redirect target and is live and complete.
- browser-use benchmark claim ("3-5x faster than other models with SOTA accuracy") is from the project's own README; this is not an independently verified third-party benchmark. Article writers should note this is a self-reported claim.

---

## AEE-405: Tool Selection

### Verified Sources

- `https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools` — naming conventions, description best practices, tool consolidation guidance, model selection, tool_choice

### Key Facts

(All guidance from the same define-tools page verified in AEE-402; consolidated here for the tool-selection article angle.)

#### Tool Count and Selection Accuracy

No specific numeric claim (e.g., "accuracy drops by X% with N tools") was found in the accessible documentation. Anthropic's guidance addresses selection accuracy through:
- Using Claude Opus 4.6 for complex tools / ambiguous queries (explicitly stated to handle multiple tools better)
- Writing detailed descriptions (most important factor in tool performance)
- Consolidating related operations into fewer tools — "fewer, more capable tools reduce selection ambiguity"
- Namespacing tool names when spanning multiple services

#### Forcing Tool Selection

Use `tool_choice` to force tool use:
- `{"type": "any"}` — must use one of the provided tools
- `{"type": "tool", "name": "get_weather"}` — must use this specific tool
- Combine with `strict: true` on tool definitions to guarantee both tool call AND schema conformance

Adding explicit instructions in user message with `tool_choice: auto` also works: `"What's the weather like in London? Use the get_weather tool in your response."`

#### When `tool_choice: any`/`tool` Forces Tool Use

When using `any` or `tool`, the API prefills the assistant message to force tool use — the model will not emit a natural language response or explanation before the `tool_use` block, even if asked.

#### Prompting for Parallel Tool Use

To maximize parallel tool execution, add to system prompt:
```
For maximum efficiency, whenever you need to perform multiple independent operations, invoke all relevant tools simultaneously rather than sequentially.
```

### CRITICAL FLAGS

- No primary-source data on how tool count affects selection accuracy. Any percentage claim (e.g., "accuracy drops X% above N tools") is unverified. Article writers should not present such figures without a primary citation.

---

## AEE-406: Sandboxing and Execution Safety

### Verified Sources

- `https://owasp.org/www-project-top-10-for-large-language-model-applications/` — OWASP LLM Top 10 list with risk names and one-line descriptions
- `https://platform.claude.com/docs/en/docs/build-with-claude/computer-use` — Anthropic safety and isolation guidance for computer use (see AEE-404 above)

### Key Facts

#### OWASP LLM Top 10 — Relevant Risks for Tool Execution

Full verified list from the OWASP page:

| ID | Name | Relevance to Tool Execution |
|---|---|---|
| LLM01 | Prompt Injection | High — attackers craft inputs to hijack tool calls or exfiltrate data via tools |
| LLM02 | Insecure Output Handling | High — tool outputs passed to downstream code without validation can trigger code execution |
| LLM03 | Training Data Poisoning | Low — not directly tool execution |
| LLM04 | Model Denial of Service | Medium — excessive tool calls can exhaust resources |
| LLM05 | Supply Chain Vulnerabilities | Medium — compromised tool libraries |
| LLM06 | Sensitive Information Disclosure | High — tools may expose sensitive data in outputs |
| LLM07 | Insecure Plugin Design | High — "LLM plugins processing untrusted inputs and having insufficient access control risk severe exploits like remote code execution" |
| LLM08 | Excessive Agency | High — "Granting LLMs unchecked autonomy to take action can lead to unintended consequences, jeopardizing reliability, privacy, and trust" |
| LLM09 | Overreliance | Medium — blindly executing tool outputs |
| LLM10 | Model Theft | Low — not directly tool execution |

Key risks for tool execution: **LLM01** (Prompt Injection), **LLM07** (Insecure Plugin Design), **LLM08** (Excessive Agency).

#### Anthropic Safety Recommendations for Computer Use (Isolation Setup)

From the computer use documentation (verified):
1. **VM/Container isolation**: Use a dedicated virtual machine or container with minimal privileges to prevent direct system attacks or accidents
2. **Data access restriction**: Avoid giving the model access to sensitive data or account login information
3. **Network allowlisting**: Limit internet access to an allowlist of domains
4. **Human-in-the-loop**: Require human confirmation for actions with meaningful real-world consequences (financial transactions, accepting cookies, agreeing to TOS)
5. **Prompt injection mitigations**: Claude has been trained to resist prompt injections from webpage content/images; built-in classifiers automatically run on prompts to flag potential injections and steer Claude to ask for confirmation. Can be opted out by contacting support.
6. **Coordinate validation**: Validate that coordinates from Claude are within display bounds before executing click actions
7. **Iteration limits**: Implement max iteration counts in the agent loop to prevent runaway API costs from infinite loops

#### Anthropic Computer Use Environment Reference

Recommended architecture per Anthropic docs:
- Virtual X11 display server (Xvfb)
- Lightweight Linux desktop environment (Mutter + Tint2)
- Docker container with port mappings for viewing/interacting
- Reference implementation: `https://github.com/anthropics/anthropic-quickstarts/tree/main/computer-use-demo`

### CRITICAL FLAGS

- The OWASP page provides risk names and one-sentence summaries only. Detailed descriptions of LLM07 (Insecure Plugin Design) and LLM08 (Excessive Agency) are on sub-pages not fetched. Article writers wanting to quote specific mitigations from OWASP should fetch `https://owasp.org/www-project-top-10-for-large-language-model-applications/Archive_2023/` or the individual risk pages.
- The OWASP LLM Top 10 version is not specified on the page fetched; the numbering (LLM07: Insecure Plugin Design) matches the 2023 version. The 2025 version may renumber risks. Writers should verify the current version number.
