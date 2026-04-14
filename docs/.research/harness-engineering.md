# Harness Engineering Research Notes

---

## AEE-700: What Is a Harness?

### Sources

- [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — LIVE. Anthropic Engineering blog, published November 26, 2025. Covers two-agent harness design (initializer + coding agent). Does NOT formally define "execution harness" vs "eval harness" by those names.
- [Managed Agents](https://www.anthropic.com/engineering/managed-agents) — LIVE. Defines harness formally: "the loop that calls Claude and routes Claude's tool calls to the relevant infrastructure." Introduces meta-harness concept. Virtualizes session, harness, and sandbox as three decoupled components.
- [Claude Code](https://claude.ai/code) — Official Anthropic product page. Claude Code is an execution harness (agentic coding assistant with tool loop).
- [Cursor](https://cursor.com/) — Official site. Cursor is an execution harness (AI code editor, fork of VS Code).
- [Inspect AI](https://inspect.aisi.org.uk/) — Official UK AISI site. Open-source eval harness for LLMs. GitHub: https://github.com/UKGovernmentBEIS/inspect_ai
- [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) — EleutherAI GitHub. Few-shot evaluation framework for language models. Powers Hugging Face Open LLM Leaderboard.

### Key Claims

- Anthropic defines "harness" as: "the loop that calls Claude and routes Claude's tool calls to the relevant infrastructure" (Managed Agents article)
- The execution vs. evaluation harness distinction is NOT made with those exact terms in Anthropic's official documentation. The distinction is well-established in the community but is editorially constructed for the article.
- Managed Agents article describes a meta-harness: "a system with general interfaces that allow many different harnesses" — this is the clearest official framing
- Anthropic engineering blog confirms harnesses "encode assumptions about what Claude can't do on its own" and those assumptions "can go stale as models improve"
- Inspect AI is maintained by UK AISI (AI Safety Institute), not Anthropic; it is an eval harness
- lm-evaluation-harness is EleutherAI's framework; widely used as eval harness backend

### CRITICAL FLAGS

- The execution/evaluation harness binary distinction is NOT found verbatim in Anthropic docs. The article must present this as an editorial framework grounded in real examples. Do not claim Anthropic uses these exact terms.
- No official Anthropic "definition of harness" in a glossary; definitions come from engineering blog posts.
- Cursor is confirmed at cursor.com but is not an Anthropic product — verify the article does not imply Anthropic affiliation.

---

## AEE-701: The Agent Loop (ReAct)

### Sources

- [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629) — CONFIRMED LIVE. Yao et al., submitted October 6, 2022 (arXiv:2210.03629). Published at ICLR 2023. Authors: Shunyu Yao, Jeffrey Zhao, Dian Yu, Nan Du, Izhak Shafran, Karthik Narasimhan, Yuan Cao.
- [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) — CONFIRMED LIVE. Anthropic, December 19, 2024. Covers agent loop, stopping conditions, human-in-the-loop checkpoints.

### Key Claims

- ReAct paper title is confirmed: "ReAct: Synergizing Reasoning and Acting in Language Models"
- Paper abstract: "reasoning traces help the model induce, track, and update action plans as well as handle exceptions, while actions allow it to interface with external sources"
- Anthropic's Building Effective Agents does NOT mention "ReAct" by name. The pattern is described functionally.
- Anthropic guidance on loop termination: "it's also common to include stopping conditions (such as a maximum number of iterations) to maintain control"
- Agents can "pause for human feedback at checkpoints or when encountering blockers"
- "The task often terminates upon completion"
- No specific official max step count recommendation found in Anthropic docs; the guidance is general ("stopping conditions")

### CRITICAL FLAGS

- Building Effective Agents does not mention "ReAct" — the article should not claim Anthropic endorses the ReAct label; it can reference the paper as the academic grounding while noting Anthropic describes the same pattern functionally
- No specific recommended max iteration count found in official Anthropic docs. Any specific number (e.g., "50 steps") stated in the article should be flagged as community convention, not official guidance.

---

## AEE-702: Lifecycle Hooks (CRITICAL)

### Sources

- [Claude Code Hooks Reference](https://code.claude.com/docs/en/hooks) — CONFIRMED LIVE (redirects from docs.anthropic.com/en/docs/claude-code/hooks). Comprehensive official reference.
- [Claude Code Settings](https://code.claude.com/docs/en/settings) — CONFIRMED LIVE. Confirms hooks live in `settings.json` at multiple scopes.

### Key Claims — Hook Event Types (VERIFIED)

The following event types are confirmed from official docs:

**Session Events:** `SessionStart`, `SessionEnd`, `InstructionsLoaded`

**Per-Turn Events:** `UserPromptSubmit`, `Stop`, `StopFailure`

**Tool Events (Agentic Loop):** `PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionRequest`, `PermissionDenied`

**Agent/Task Events:** `SubagentStart`, `SubagentStop`, `TaskCreated`, `TaskCompleted`, `TeammateIdle`

**File/Environment Events:** `FileChanged`, `CwdChanged`, `ConfigChange`

**Other Events:** `Notification`, `WorktreeCreate`, `WorktreeRemove`, `PreCompact`, `PostCompact`, `Elicitation`, `ElicitationResult`

### Key Claims — Configuration Location (VERIFIED)

Hooks are configured in `settings.json` at these scopes:

| Location | Scope |
|---|---|
| `~/.claude/settings.json` | User-level (all projects) |
| `.claude/settings.json` | Project-level (shared via git) |
| `.claude/settings.local.json` | Project-level (local only, gitignored) |
| Managed policy `managed-settings.json` | Organization-wide |
| Plugin `hooks/hooks.json` | When plugin enabled |

The `hooks` key sits at the top level of `settings.json`.

### Key Claims — Hook Body Format (VERIFIED)

Three-level nesting: `hooks` -> `EventName` array -> matcher objects -> `hooks` array of handler objects.

Four hook handler types: `command`, `http`, `prompt`, `agent`.

Exit codes: 0 = success, 2 = blocking error shown to user, other = non-blocking error.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/script.sh",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### LangChain Callbacks Note

LangChain uses a callbacks system (not called "hooks") with similar lifecycle events. The official docs are at https://python.langchain.com/docs/concepts/callbacks/. Comparable events include `on_chain_start`, `on_chain_end`, `on_tool_start`, `on_tool_end`. Architecture is analogous but naming conventions differ.

### CRITICAL FLAGS

- The spec article should use the official event type names exactly as listed above (e.g., `PreToolUse` not `pre_tool_use`).
- The canonical settings file path for user-level hooks is `~/.claude/settings.json` — confirmed.
- The hook system is significantly richer than typical frameworks (20+ event types). The article should not understate the scope.

---

## AEE-703: Context Assembly (CRITICAL)

### Sources

- [Anthropic Messages API](https://platform.claude.com/docs/en/api/messages) — CONFIRMED LIVE (redirects from docs.anthropic.com/en/api/messages). Full API reference.
- [Tool Use: Handle Tool Calls](https://platform.claude.com/docs/en/agents-and-tools/tool-use/handle-tool-calls) — CONFIRMED LIVE. Official docs on `is_error` and tool result format.
- [Tool Use Overview](https://platform.claude.com/docs/en/docs/build-with-claude/tool-use) — CONFIRMED LIVE (redirects from docs.anthropic.com/en/docs/build-with-claude/tool-use). Pricing and token counts for tool use.

### Key Claims — Context Assembly Order (VERIFIED)

The Anthropic API request structure is:

```json
{
  "system": "...",       // top-level system parameter (NOT in messages array)
  "messages": [...],     // conversation history
  "tools": [...],        // SEPARATE top-level parameter (NOT in messages array)
  "model": "...",
  "max_tokens": 1024
}
```

**Tool definitions are in the `tools=` parameter, completely separate from `messages`.** They are NOT injected into the messages array.

**Order of context consumption (from API structure):**
1. `system` parameter — system prompt
2. `tools` parameter — tool definitions (processed separately; token cost is added to input tokens)
3. `messages` array — conversation history

Anthropic automatically includes a "special system prompt for the model which enables tool use" when tools are provided. This adds tokens to the system prompt budget (346 tokens for `auto`/`none` tool_choice on current models).

### Key Claims — Tool Result `is_error` Field (VERIFIED)

The `is_error` field IS part of the official Anthropic protocol. From official docs:

```json
{
  "type": "tool_result",
  "tool_use_id": "toolu_01A09q90qw90lq917835lq9",
  "content": "ConnectionError: the weather service API is not available (HTTP 500)",
  "is_error": true
}
```

`tool_result` fields:
- `tool_use_id: string` (required)
- `type: "tool_result"` (required)
- `content: string | array` (optional)
- `is_error: boolean` (optional) — set to `true` if tool execution failed
- `cache_control: CacheControlEphemeral` (optional)

### Key Claims — Skill/Prompt Injections

No official Anthropic documentation found confirming that skill/prompt injections can be injected as additional system turns in the messages array. The system prompt is a single top-level string parameter. Multi-turn system injections are a harness convention not confirmed in API docs.

### Key Claims — Token Budget Allocation

No official Anthropic guidance found on recommended token budget allocation percentages across context layers (e.g., "X% for system prompt, Y% for tools, Z% for history"). This is not documented in the Messages API reference.

### CRITICAL FLAGS

- Tool definitions are in `tools=` parameter, NOT in `messages`. This is a critical distinction — articles claiming tools are "injected into the system prompt" or "in the messages array" are incorrect.
- There is NO official token budget allocation guidance in Anthropic docs. Any specific percentages cited in the article are editorial recommendations, not official policy.
- "Skill/prompt injections as additional system turns" is NOT confirmed by official docs. The API system parameter is a single string, not an array.

---

## AEE-704: Session Management

### Sources

- [Claude Code Agent SDK — Work with Sessions](https://code.claude.com/docs/en/agent-sdk/sessions) — CONFIRMED LIVE. Official SDK session management reference.
- [Claude Code Memory](https://code.claude.com/docs/en/memory) — CONFIRMED LIVE. Auto memory storage at `~/.claude/projects/<project>/memory/`.
- [Simon Willison: Don't let Claude Code delete your session logs](https://simonwillison.net/2025/Oct/22/claude-code-logs/) — Community source confirming session storage details.

### Key Claims — Session Persistence (VERIFIED)

Session files are stored at: `~/.claude/projects/<encoded-cwd>/<session-id>.jsonl`

Where `<encoded-cwd>` is the absolute working directory with every non-alphanumeric character replaced by `-` (e.g., `/Users/me/proj` becomes `-Users-me-proj`).

Format: JSONL (JSON Lines) — each message is one line, written incrementally as it is generated. This provides crash resilience.

Default cleanup: sessions older than 30 days are deleted at startup. Configurable via `cleanupPeriodDays` in `settings.json` (minimum 1, cannot be 0). Can disable entirely in non-interactive mode with `--no-session-persistence` flag or `persistSession: false` SDK option.

SDK session operations:
- `continue` — resumes most recent session in current directory
- `resume` — resumes specific session by ID
- `fork` — branches session history without modifying original
- `listSessions()`, `getSessionMessages()` — enumerate and read sessions on disk

### Key Claims — Session Security / User Isolation

No official Anthropic documentation found explicitly addressing session security or user isolation guarantees. Sessions are machine-local; they are NOT shared across machines or cloud environments. The Agent SDK docs note that for multi-user apps, you need to track session IDs per user and use `resume`.

### CRITICAL FLAGS

- No official Anthropic security guidance on session isolation was found. Articles making specific isolation guarantees should flag this as inferred rather than documented.
- Session files are plaintext JSONL containing full conversation history including tool call inputs/outputs — this is a sensitive data consideration not explicitly called out in the official docs.

---

## AEE-705: Permission Models (CRITICAL)

### Sources

- [Claude Code Permissions](https://code.claude.com/docs/en/permissions) — CONFIRMED LIVE (redirects from docs.anthropic.com/en/docs/claude-code/settings). Full permissions reference.
- [Claude Code Settings](https://code.claude.com/docs/en/settings) — CONFIRMED LIVE. Settings file locations and permission config structure.

### Key Claims — Permission Model (VERIFIED)

Claude Code uses a **tiered permission system** with deny → ask → allow precedence (deny always wins).

**Permission rule syntax:** `Tool` or `Tool(specifier)`

Examples:
- `Bash(npm run test *)` — allow exact prefix with wildcard
- `Read(./.env)` — deny reading specific file
- `WebFetch(domain:example.com)` — allow fetch to specific domain
- `mcp__puppeteer__puppeteer_navigate` — match specific MCP tool

**Config structure in `settings.json`:**

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run lint)",
      "Bash(npm run test *)",
      "Read(~/.zshrc)"
    ],
    "deny": [
      "Bash(curl *)",
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./secrets/**)"
    ]
  }
}
```

Also supports `ask` array (prompt for confirmation on each use).

**Permission modes (set via `defaultMode`):**
- `default` — prompts on first use of each tool
- `acceptEdits` — auto-accepts file edits in working directory
- `plan` — read-only (analyze but not modify)
- `auto` — auto-approves with background safety classifier (research preview)
- `dontAsk` — auto-denies unless pre-approved
- `bypassPermissions` — skips all prompts (dangerous; for isolated environments only)

### Key Claims — Configuration Location (VERIFIED)

Same scoped hierarchy as settings:

| Location | Scope |
|---|---|
| `~/.claude/settings.json` | User-level (all projects) |
| `.claude/settings.json` | Project-level (committed to git) |
| `.claude/settings.local.json` | Project-level (gitignored) |
| `managed-settings.json` (system paths) | Organization-wide (cannot be overridden) |

The official docs URL for permissions is: `https://code.claude.com/docs/en/permissions`

Managed settings can enforce `allowManagedPermissionRulesOnly: true` to prevent user/project settings from defining any allow/ask/deny rules.

### Key Claims — Principle of Least Privilege

Claude Code's built-in default mode prompts before each tool use — this implements least privilege by default. The `dontAsk` mode combined with explicit `allow` rules is the explicit least-privilege configuration pattern. The `bypassPermissions` mode is explicitly warned against: "Only use this mode in isolated environments like containers or VMs."

The permission system applies at the tool level, not the data level: deny rules on `Read(./.env)` prevent Claude's built-in Read tool from accessing the file but do NOT prevent `cat .env` via Bash. For OS-level enforcement, sandboxing must be enabled.

### CRITICAL FLAGS

- Read/Edit deny rules do NOT prevent Bash subprocess access to the same files. This is explicitly called out in official docs. The article must not overstate permission enforcement scope.
- `bypassPermissions` mode exists and is documented — this is a risk factor the article should acknowledge.

---

## AEE-706: Error Recovery

### Sources

- [Tool Use: Handle Tool Calls](https://platform.claude.com/docs/en/agents-and-tools/tool-use/handle-tool-calls) — CONFIRMED LIVE. Official docs on `is_error`, tool error handling, and retry behavior.
- [Tool Use Overview](https://platform.claude.com/docs/en/docs/build-with-claude/tool-use) — CONFIRMED LIVE.

### Key Claims — `is_error: true` Field (VERIFIED)

`is_error` IS the correct and official field name. It is part of the `tool_result` content block in the Anthropic API. It is optional and boolean.

Usage:
```json
{
  "type": "tool_result",
  "tool_use_id": "toolu_01A09q90qw90lq917835lq9",
  "content": "ConnectionError: the weather service API is not available (HTTP 500)",
  "is_error": true
}
```

When `is_error: true` is set, Claude incorporates the error into its response and may adapt its approach.

### Key Claims — Retry Strategies (VERIFIED from official docs)

Official guidance on retry strategies:

1. **For invalid tool calls (missing parameters):** Claude will "retry 2-3 times with corrections before apologizing to the user" — this is the documented built-in retry behavior.

2. **For tool execution errors:** Return `is_error: true` with a descriptive error message. Official tip: "Write instructive error messages. Instead of generic errors like 'failed', include what went wrong and what Claude should try next, e.g., 'Rate limit exceeded. Retry after 60 seconds.' This gives Claude the context it needs to recover or adapt without guessing."

3. **Strict tool use** (`strict: true` on tool definitions) prevents invalid tool calls by guaranteeing schema conformance — this eliminates one class of retries.

### Key Claims — Server Tool Errors

For server tools (web_search, code_execution, web_fetch): Claude handles errors transparently without requiring `is_error` from the caller. Documented error codes for web_search: `too_many_requests`, `invalid_input`, `max_uses_exceeded`, `query_too_long`, `unavailable`.

### CRITICAL FLAGS

- The official docs do NOT specify a recommended exponential backoff implementation or specific retry count limit for execution errors (only for invalid tool calls: "2-3 times"). Articles recommending specific retry policies (e.g., "retry up to 5 times with exponential backoff") are offering editorial guidance beyond official documentation.
- No official guidance on circuit breaker patterns was found in Anthropic docs.

---

## Summary of URL Verification

| URL | Status |
|---|---|
| `https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents` | LIVE (verified) |
| `https://www.anthropic.com/research/building-effective-agents` | LIVE (verified) |
| `https://www.anthropic.com/engineering/managed-agents` | LIVE (verified) |
| `https://arxiv.org/abs/2210.03629` | LIVE (verified, ReAct paper) |
| `https://docs.anthropic.com/en/docs/claude-code/hooks` | REDIRECTS to `https://code.claude.com/docs/en/hooks` (live) |
| `https://docs.anthropic.com/en/api/messages` | REDIRECTS to `https://platform.claude.com/docs/en/api/messages` (live) |
| `https://docs.anthropic.com/en/docs/build-with-claude/tool-use` | REDIRECTS to `https://platform.claude.com/docs/en/docs/build-with-claude/tool-use` (live) |
| `https://docs.anthropic.com/en/docs/claude-code/settings` | REDIRECTS to `https://code.claude.com/docs/en/settings` (live) |
| `https://inspect.aisi.org.uk/` | LIVE (verified, UK AISI Inspect AI) |
| `https://github.com/EleutherAI/lm-evaluation-harness` | LIVE (verified) |
| `https://cursor.com/` | LIVE (verified) |
| `https://claude.ai/code` | Not directly fetched; product is widely confirmed |

## Top Discrepancies to Watch

1. **AEE-700**: execution/eval harness distinction is editorially constructed; Anthropic uses "harness" without this sub-taxonomy in official docs
2. **AEE-702**: hook event types are more extensive than most frameworks — 20+ events. Use official names exactly (CamelCase: `PreToolUse`, not `pre_tool_use`)
3. **AEE-703**: tools are in `tools=` parameter, NOT messages array. System prompt is a single string, not an array of injections.
4. **AEE-705**: permission deny rules do NOT block Bash subprocesses accessing the same paths — important caveat
5. **AEE-706**: built-in retry is only documented for invalid tool calls (2-3 times); no official retry guidance for execution errors beyond "write instructive error messages"
