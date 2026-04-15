# Multi-Agent and Orchestration — Research Notes

Research pass completed: 2026-04-14
Researcher: Claude Sonnet 4.6 (automated)

---

## AEE-3: Agentic Engineering Levels

### Source
- URL: https://www.bassimeledath.com/blog/levels-of-agentic-engineering
- Author: Bassim Eledath
- Title: "The 8 Levels of Agentic Engineering"
- Status: VERIFIED (page successfully fetched)

### Core Thesis
AI coding capabilities are outpacing engineers' ability to use them effectively. The gap between capability and practice closes through eight progressive levels, each enabling substantially greater output. Team productivity depends on all members advancing levels together.

### The 8 Levels

**Level 1 — Tab Complete**
What to build/do: Use autocomplete suggestions.
Core insight: Context limitations constrain what models can help with.

**Level 2 — Agent IDE**
What to build/do: Integrate AI chat into the IDE for multi-file edits. Use plan mode to translate ideas into structured implementation steps.
Core insight: Plan mode provides human control.

**Level 3 — Context Engineering**
What to build/do: Maximize information density in prompts by curating system prompts, rules files (`.cursorrules`, `CLAUDE.md`), tool descriptions, conversation history, and tool exposure per turn.
Key quote: "Every token needs to fight for its place in the prompt"
Why it matters: MCPs and image inputs consume tokens rapidly; dozens of tools overwhelm models through schema overhead. CLI tools increasingly replace MCPs because agents run targeted commands and only relevant output enters context, versus MCPs injecting full schemas every turn.

**Level 4 — Compounding Engineering**
What to build/do: Execute a plan-delegate-assess-codify loop. After evaluating output, codify lessons back into rules files and documentation so LLMs discover useful context automatically.
Key insight: LLMs are stateless — without explicit encoding of learned patterns, they repeat mistakes.

**Level 5 — MCP and Skills**
What to build/do: Build Model Context Protocol servers and custom skills granting LLM access to databases, APIs, CI pipelines, design systems, browsers, and notifications. Consolidate repeated team skills into shared registries with version control.
Key example: PR review skills that fan out into specialized subagents checking database safety, complexity, prompt standards, and linting.

**Level 6 — Harness Engineering and Automated Feedback Loops**
What to build/do: Wire observability tooling, type systems, tests, linters, and pre-commit hooks into agent runtime so models detect and correct mistakes autonomously. Implement backpressure (constraints preventing harmful actions) and security boundaries separating agents from secrets.
Design principles:
- "Design for throughput, not perfection" — tolerate small non-blocking errors and do final quality passes before release
- Constraints outperform step-by-step instructions; define boundaries rather than checklists
Key framework: Maintain `AGENTS.md` (~100 lines) as a table of contents pointing to structured docs elsewhere; make documentation freshness part of CI.

**Level 7 — Background Agents**
What to build/do: Run agents asynchronously in isolated contexts orchestrated by a dispatcher. Use multiple models for different roles (implementation, research, review). Separate implementer from reviewer to avoid bias — different model instances should not grade their own work.
Key shift: Plan mode fades as models reliably plan without human sign-off. Planning becomes exploration: probing codebases, prototyping in worktrees, mapping solution space.
Tools mentioned: Dispatch (local, rapid development), Ramp's Inspect (cloud-hosted VMs for long-running work).
CI integration: Trigger agents from existing infrastructure — docs bots regenerating on merge, security reviewers scanning PRs, dependency bots upgrading packages.

**Level 8 — Autonomous Agent Teams**
What to build/do: Remove hub-and-spoke bottleneck by enabling agents to coordinate directly, claim tasks, share findings, flag dependencies, and resolve conflicts without routing through a single orchestrator.
Current state: Unmastered frontier. Claude Code's experimental Agent Teams feature and implementations by Anthropic (16 agents building a C compiler) and Cursor (hundreds of agents) reveal seams: risk-aversion without hierarchy, regressions without CI enforcement, multi-agent coordination remains hard.
Author's assessment: Models lack sufficient speed and token efficiency for economical deployment outside moonshot projects. Level 7 offers greater leverage for typical engineering work.

**Level ? (Emerging)**
Voice-to-voice interaction with coding agents — iterative interaction replacing the myth of perfect one-shot composition.

### Cross-Reference Frameworks
- OpenAI's "Harness Engineering" approach: full observability stacks wired into agent runtime (Chrome DevTools, logging, browser control).
- Anthropic's own 16-agent C compiler experiment is cited as evidence of Level 8 being a frontier.

---

## AEE-601: Agent Roles and Topologies

### Sources
- https://code.claude.com/docs/en/agent-teams (VERIFIED — experimental feature, Claude Code v2.1.32+)
- https://code.claude.com/docs/en/sub-agents (VERIFIED)
- https://www.anthropic.com/research/building-effective-agents (VERIFIED)

### Anthropic's Documented Topology Patterns

#### 1. Subagents (Hub-and-Spoke / Orchestrator-Workers)
Source: https://code.claude.com/docs/en/sub-agents

Each subagent runs in its own context window with a custom system prompt, specific tool access, and independent permissions. The main agent delegates work; subagents work independently and return only results (summaries). They do NOT communicate with each other — only with the main agent.

Architecture:
- **Orchestrator (main agent)**: coordinates, assigns tasks, synthesizes results
- **Subagents (workers)**: each has focused instructions, restricted tools, optionally a different model (e.g., Haiku for cost efficiency)

Key quote: "Subagents only report results back to the main agent and never talk to each other."

Use cases: focused tasks where only the result matters; preserving context in the main session; cost control by routing to cheaper models.

Implementation (Claude Agent SDK, Python):
```python
from claude_agent_sdk import query, ClaudeAgentOptions, AgentDefinition

async for message in query(
    prompt="Use the code-reviewer agent to review this codebase",
    options=ClaudeAgentOptions(
        allowed_tools=["Read", "Glob", "Grep", "Agent"],
        agents={
            "code-reviewer": AgentDefinition(
                description="Expert code reviewer for quality and security reviews.",
                prompt="Analyze code quality and suggest improvements.",
                tools=["Read", "Glob", "Grep"],
            )
        },
    ),
):
    ...
```

Messages from subagent contexts include a `parent_tool_use_id` field for tracking.

#### 2. Agent Teams (Peer-to-Peer / Collaborative)
Source: https://code.claude.com/docs/en/agent-teams
Status: EXPERIMENTAL — requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

Architecture:
- **Team lead**: main Claude Code session, creates team, spawns teammates, coordinates via shared task list
- **Teammates**: separate independent Claude Code instances, each with own context window
- **Task list**: shared work items with dependency management; teammates claim and complete tasks
- **Mailbox**: messaging system for direct agent-to-agent communication

Key difference from subagents: "In agent teams, teammates share a task list, claim work, and communicate directly with each other."

Teammate capabilities:
- Send messages to specific teammates by name
- Broadcast to all teammates simultaneously (use sparingly; cost scales linearly)
- Self-claim tasks from shared list
- Request plan approval from lead before implementing

|                   | Subagents                                        | Agent Teams                                         |
| :---------------- | :----------------------------------------------- | :-------------------------------------------------- |
| Communication     | Report results back to main agent only           | Teammates message each other directly               |
| Coordination      | Main agent manages all work                      | Shared task list with self-coordination             |
| Best for          | Focused tasks where only result matters          | Complex work requiring discussion and collaboration |
| Token cost        | Lower: results summarized back                   | Higher: each teammate is a separate Claude instance |

#### 3. Pipeline / Sequential (from Anthropic research)
Source: https://www.anthropic.com/research/building-effective-agents

Documented as "Prompt Chaining": decomposes tasks into sequential steps where each LLM call processes previous output, with programmatic gates to ensure quality.
Use case: well-defined tasks requiring predictability.

#### 4. Evaluator-Optimizer (Review Loop)
Source: https://www.anthropic.com/research/building-effective-agents

One LLM generates responses while another provides iterative feedback in a loop for refinement.
Note: Bassim Eledath's Level 7 explicitly advocates separating implementer from reviewer to avoid bias.

### CRITICAL FLAG
The URL https://docs.anthropic.com/en/docs/build-with-claude/agents redirects (301) to platform.claude.com and returns 404. The canonical multi-agent topology documentation for the Anthropic Messages API (not Claude Code) could not be located. The topology patterns above are sourced from Claude Code docs and Anthropic research blog posts. Readers should check https://platform.claude.com/docs for any updated Messages API multi-agent guide.

---

## AEE-602: Agent Communication

### Sources
- https://code.claude.com/docs/en/agent-teams (VERIFIED)
- https://platform.claude.com/docs/en/agents-and-tools/tool-use/how-tool-use-works (VERIFIED)

### How Agent-to-Agent Communication Works in the Anthropic API

For the **Messages API (orchestrator pattern)**, communication is mediated by the orchestrator:
1. Orchestrator constructs context (system prompt + messages array)
2. Dispatches a fresh API call to the subagent model
3. Subagent responds with text or tool_use blocks
4. Orchestrator collects result, injects it into the next context
5. Each agent interaction is a stateless API call — the orchestrator manages state

The API itself has no native agent-to-agent channel. Communication is always:
`orchestrator builds context → API call → response → orchestrator synthesizes`

### Claude Code Agent Teams Communication (Documented)
Source: https://code.claude.com/docs/en/agent-teams

Direct messaging between teammates via a mailbox system:
- `message`: send to one specific teammate by name
- `broadcast`: send to all teammates simultaneously

Delivery model:
- Automatic delivery — lead does not need to poll
- Idle notifications: when a teammate finishes, it automatically notifies the lead
- Shared task list: all agents see task status

"When teammates send messages, they're delivered automatically to recipients."

### Subagent Context Isolation
Source: https://code.claude.com/docs/en/sub-agents

Each subagent:
- Has its own context window
- Loads the same project context as a regular session (CLAUDE.md, MCP servers, skills)
- Does NOT inherit the lead's conversation history
- Receives the spawn prompt from the lead

### CRITICAL FLAG
No official Anthropic Messages API documentation was found describing agent-to-agent communication patterns (e.g., how to structure a multi-agent system using `client.messages.create()`). The communication patterns above are documented for Claude Code's Agent SDK and agent teams feature, not the raw Messages API.

---

## AEE-604: Parallelism and Synchronization

### Sources
- https://platform.claude.com/docs/en/agents-and-tools/tool-use/parallel-tool-use (VERIFIED)
- https://platform.claude.com/docs/en/agents-and-tools/tool-use/how-tool-use-works (VERIFIED)

### Anthropic API: Parallel Tool Call Behavior

By default, Claude may return multiple `tool_use` blocks in a single response (one API call, many tool calls).

**API signal**: response contains multiple blocks of type `tool_use` with `stop_reason: "tool_use"`.

**Disabling parallel tool use**:
- `disable_parallel_tool_use=true` with `tool_choice: auto` → Claude uses at most one tool
- `disable_parallel_tool_use=true` with `tool_choice: any` or `tool` → Claude uses exactly one tool

### Correct Message History Format for Parallel Tool Calls

CRITICAL: All tool results from parallel calls must be batched in a single user message.

```json
// CORRECT — maintains parallel tool use
[
  {"role": "assistant", "content": [tool_use_1, tool_use_2]},
  {"role": "user", "content": [tool_result_1, tool_result_2]}
]

// WRONG — breaks parallel tool use (teaches Claude to use tools sequentially)
[
  {"role": "assistant", "content": [tool_use_1, tool_use_2]},
  {"role": "user", "content": [tool_result_1]},
  {"role": "user", "content": [tool_result_2]}
]
```

Additional formatting requirement: in the user message containing tool results, `tool_result` blocks must come FIRST in the content array. Any text must come AFTER all tool results.

### Python Pattern for Concurrent Tool Execution

The official docs show synchronous iteration (execute tools sequentially in a loop), but note that asyncio.gather can be applied. The documented pattern:

```python
# From official parallel tool use docs
tool_uses = [block for block in response.content if block.type == "tool_use"]

tool_results = []
for tool_use in tool_uses:
    result = execute_tool(tool_use.name, tool_use.input)  # can be made concurrent
    tool_results.append({
        "type": "tool_result",
        "tool_use_id": tool_use.id,
        "content": result
    })

# All results in ONE user message
messages.extend([
    {"role": "assistant", "content": response.content},
    {"role": "user", "content": tool_results}
])
```

### CRITICAL FLAG
The official Anthropic parallel tool use docs show synchronous tool execution. The `asyncio.gather` pattern for concurrent dispatch is a natural Python extension (used widely in practice) but is NOT explicitly documented in official Anthropic docs as of this research pass. When writing AEE-604, the asyncio.gather pattern should be presented as a developer best-practice extension, not an Anthropic-prescribed API pattern.

### Prompting for Parallel Tool Use (Claude 4 models)

From official docs, system prompt to maximize parallel tool use:
```
<use_parallel_tool_calls>
For maximum efficiency, whenever you perform multiple independent operations, invoke all relevant tools simultaneously rather than sequentially. Prioritize calling tools in parallel whenever possible.
</use_parallel_tool_calls>
```

### Tool Runner SDK Abstraction
Source: https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-runner (VERIFIED)

The Python/TypeScript/Ruby SDKs provide a `tool_runner` (or `toolRunner`) that automates the agentic loop including parallel tool handling. From docs: "For most use cases, Tool Runner automatically handles parallel tool execution with much less code."

---

## AEE-605: Orchestration Patterns

### Sources
- https://www.anthropic.com/research/building-effective-agents (VERIFIED)
- https://code.claude.com/docs/en/agent-teams (VERIFIED)
- https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents (VERIFIED)

### Anthropic-Documented Orchestration Patterns

#### 1. Prompt Chaining (Pipeline)
Sequential LLM calls where each processes prior output. Programmatic gates validate quality between steps.
Best for: tasks that can be cleanly decomposed into ordered subtasks.

#### 2. Routing
Classify input, direct to specialized downstream agent or handler.
Best for: well-differentiated input categories requiring different handling.

#### 3. Parallelization (Fan-Out / Map-Reduce)
Two variants:
- **Sectioning**: independent subtasks run simultaneously, results aggregated (fan-out/fan-in)
- **Voting**: multiple agents attempt the same task, results compared for confidence

Best for: tasks that decompose into independent parts.

#### 4. Orchestrator-Workers
Central LLM dynamically breaks down tasks, delegates to worker LLMs, synthesizes results. Unlike parallelization, subtasks are not pre-defined — determined by the orchestrator based on input.
Anthropic example: "Coding products making complex changes to multiple files."

#### 5. Evaluator-Optimizer (Review Loop)
One LLM generates, another evaluates and provides feedback. Loop continues until quality threshold is met.
Best for: tasks with clear quality criteria that can be evaluated programmatically or by another model.

#### 6. Agent Teams / Collaborative Investigation (Experimental)
Source: https://code.claude.com/docs/en/agent-teams

Multiple agents investigate different hypotheses in parallel and actively debate/challenge each other's findings. Strongest documented use case:
- Root-cause debugging with competing hypotheses
- Code review with multiple specialized reviewers (security, performance, test coverage)

Anthropic example prompt: "Spawn 5 agent teammates to investigate different hypotheses. Have them talk to each other to try to disprove each other's theories, like a scientific debate."

#### 7. Plan-Approve-Implement (Human-in-the-Loop Gate)
Source: https://code.claude.com/docs/en/agent-teams

Teammate works in read-only plan mode, submits plan for approval, lead (or human) approves or rejects with feedback, then teammate implements. Documented in agent teams.

### LangGraph Note
LangGraph's documentation redirected or was inaccessible during this research pass. The handoff pattern (routing from a subgraph node to a parent graph node) was the only explicitly documented pattern found. The supervisor, peer-to-peer, and swarm patterns that appear in LangGraph tutorials could not be verified from official LangGraph docs during this pass.

### CRITICAL FLAG
Map-reduce as a named pattern is not explicitly documented in Anthropic's official docs. The concept is present under "Parallelization / Sectioning" in the building-effective-agents post. Fan-out/fan-in is likewise an engineering term that maps to this pattern, but is not used by Anthropic in their documentation. When writing AEE-605, these terms should be explained as aliases for documented Anthropic patterns, not presented as Anthropic-coined terminology.

---

## AEE-606: Multi-Agent Failure Modes

### Sources
- https://www.anthropic.com/research/building-effective-agents (VERIFIED)
- https://code.claude.com/docs/en/agent-teams (VERIFIED)
- https://www.bassimeledath.com/blog/levels-of-agentic-engineering (VERIFIED)
- https://platform.claude.com/docs/en/agents-and-tools/tool-use/handle-tool-calls (VERIFIED)

### Documented Failure Modes

#### From Anthropic "Building Effective Agents"
1. **Compounding errors**: autonomous agents accumulate errors across steps; each incorrect action raises the probability of downstream failures. Mitigation: "extensive testing in sandboxed environments, along with appropriate guardrails."
2. **Framework abstraction opacity**: extra layers obscure underlying prompts and responses, complicating debugging.
3. **Tool design failures**: poor tool specifications cause model mistakes; analogy to human-computer interface design failures.
4. **Higher costs without proportional benefit**: adding agents where simpler approaches suffice.

#### From Claude Code Agent Teams Docs (Experimental Feature)
Explicitly documented limitations:
1. **Task status lag**: "teammates sometimes fail to mark tasks as completed, which blocks dependent tasks"
2. **Orphaned sessions**: tmux sessions can persist after team cleanup
3. **Lead premature shutdown**: "the lead may decide the team is finished before all tasks are actually complete"
4. **File conflicts**: "two teammates editing the same file leads to overwrites"
5. **No session resumption for in-process teammates**: `/resume` and `/rewind` do not restore teammates
6. **Fixed lead**: cannot promote a teammate to lead or transfer leadership
7. **No nested teams**: teammates cannot spawn their own teams

#### From Bassim Eledath's Level 8 Analysis
1. **Risk-aversion without hierarchy**: autonomous agents lack human-supplied judgment for risky decisions
2. **Regressions without CI enforcement**: agents can break existing behavior if test infrastructure is not wired into the agent runtime
3. **Economic infeasibility for most projects**: models lack sufficient speed and token efficiency; Level 7 (background agents) offers better leverage than Level 8 (autonomous teams) for typical engineering work

#### From Anthropic Tool Use Docs (Single-Agent Failure Modes Applicable to Multi-Agent)
1. **Invalid tool parameters**: Claude retries 2-3 times with corrections before failing; use `strict: true` to eliminate invalid calls
2. **Tool execution errors**: use `is_error: true` in tool_result blocks; write instructive error messages ("Rate limit exceeded. Retry after 60 seconds." rather than generic "failed")
3. **Infinite loops**: agentic loops require explicit iteration limits (reference implementation uses `max_iterations=10`)
4. **pause_turn**: server-side tools that hit iteration limits return `stop_reason: "pause_turn"` — re-send conversation to continue

#### From Computer Use / Long-Running Agent Docs
1. **Prompt injection**: agents can follow commands found in environmental content (webpages, files) that conflict with user instructions
2. **Unverified assumed outcomes**: agents may assume their actions succeeded without explicitly verifying (computer use doc recommends prompting: "After each step, take a screenshot and carefully evaluate if you have achieved the right outcome")

### CRITICAL FLAG
No comprehensive multi-agent failure taxonomy exists in official Anthropic documentation. The failure modes above are assembled from multiple scattered sources. For AEE-606, these should be presented as empirically observed failure categories, not as a single authoritative Anthropic taxonomy.

---

## General Multi-Agent Resources

### Official Anthropic Documentation (Verified URLs)
- Tool use overview: https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview
- Parallel tool use: https://platform.claude.com/docs/en/agents-and-tools/tool-use/parallel-tool-use
- Handle tool calls: https://platform.claude.com/docs/en/agents-and-tools/tool-use/handle-tool-calls
- Tool runner SDK: https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-runner
- How tool use works: https://platform.claude.com/docs/en/agents-and-tools/tool-use/how-tool-use-works
- Computer use: https://platform.claude.com/docs/en/agents-and-tools/computer-use
- MCP connector: https://platform.claude.com/docs/en/agents-and-tools/mcp-connector

### Claude Code (Verified URLs)
- Overview: https://code.claude.com/docs/en/overview
- Sub-agents: https://code.claude.com/docs/en/sub-agents
- Agent teams (experimental): https://code.claude.com/docs/en/agent-teams
- Agent SDK overview: https://code.claude.com/docs/en/agent-sdk/overview

### Anthropic Research and Engineering Blog (Verified)
- Building effective agents: https://www.anthropic.com/research/building-effective-agents
- Effective harnesses for long-running agents: https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- Writing tools for agents: https://www.anthropic.com/engineering/writing-tools-for-agents (referenced but not fetched)

### Third-Party Frameworks
- LangGraph documentation was inaccessible during this research pass (redirects to different URLs, content not rendering). The handoff pattern is the only explicitly documented LangGraph multi-agent pattern found.

### Key Numbers and Constraints
- Tool runner: available in Python, TypeScript, Ruby SDKs (beta)
- Agent teams: require Claude Code v2.1.32+; experimental; enable with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`
- Recommended team size: 3-5 teammates; 5-6 tasks per teammate
- Token cost: each teammate is a separate Claude instance (scales linearly)
- Parallel tool use: all tool_results must be in a single user message
- Agentic loop: standard pattern uses iteration limit (reference implementation: `max_iterations=10`)

---

## Summary of CRITICAL FLAGS

1. **AEE-601**: Anthropic's canonical multi-agent topology page for the Messages API (docs.anthropic.com/en/docs/build-with-claude/agents) is inaccessible — 404 after redirect. Topology patterns sourced from Claude Code docs and research blog, not the Messages API reference.

2. **AEE-602**: No official Anthropic Messages API documentation found for agent-to-agent communication patterns. The pattern "orchestrator constructs context, dispatches fresh API call" is accurate mechanistically but is not the subject of a dedicated Anthropic doc page.

3. **AEE-604**: The `asyncio.gather` pattern for concurrent tool dispatch is not explicitly documented in official Anthropic docs. It is a natural extension of the documented synchronous pattern and is widely used in practice.

4. **AEE-605**: Map-reduce and fan-out/fan-in are not Anthropic terminology. They correspond to the "Parallelization / Sectioning" pattern in Anthropic docs. LangGraph topology patterns could not be verified from official LangGraph docs during this pass.

5. **AEE-606**: No single comprehensive multi-agent failure taxonomy exists in official Anthropic documentation. Failure modes are assembled from scattered sources across tool use docs, agent teams docs, and research posts.
