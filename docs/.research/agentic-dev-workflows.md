# Agentic Development Workflows — Research Notes

Research conducted: 2026-04-15
Covers: AEE-801 through AEE-806

---

## AEE-801: The AI-Driven Development Lifecycle

### Primary Sources

- AWS Blog (original): https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/
  - PAGE FETCHED: returned CSS/metadata only — body text not accessible. Title confirmed: "AI-Driven Development Life Cycle: Reimagining Software Engineering." Categorized under Amazon Q Developer and Generative AI.
- AWS Blog (open-source announcement): https://aws.amazon.com/blogs/devops/open-sourcing-adaptive-workflows-for-ai-driven-development-life-cycle-ai-dlc/
  - PAGE FETCHED: partial content extracted; technical detail thin.
- GitHub repo: https://github.com/awslabs/aidlc-workflows
  - PAGE FETCHED: directory structure and file formats confirmed (see below).
- AWS re:Invent 2025 session notes (third-party): https://dev.to/kazuya_dev/aws-reinvent-2025-introducing-ai-driven-development-lifecycle-ai-dlc-dvt214-32b
  - PAGE FETCHED: rich detail — used as primary narrative source.

### The Three Phases

AI-DLC structures software delivery into three phases containing approximately nine adaptive stages total. The framework is adaptive: not all stages apply to every workflow type. A defect fix skips most stages; a new business-rich feature in a brownfield system runs nearly all of them.

**Inception Phase**
Goal: transform business intent into validated requirements and units of work.
Mechanism: "Mob Elaboration" — the entire team (humans + AI) co-elaborates requirements. AI proposes questions, stories, and acceptance criteria; humans actively validate and correct in real time. AI cannot proceed until the team confirms understanding.
Output: detailed requirements, user stories, acceptance criteria, and a semantic context model.

**Construction Phase**
Goal: design, implement, and test the system using the validated inception artifacts.
Mechanism: "Mob Construction" — AI proposes logical architecture, domain models, code solutions, and tests. Humans clarify technical decisions and architectural choices in real time. Each stage follows a plan-verify-generate cycle: AI creates a plan, humans validate, AI executes, humans verify results.
Output: working, tested code with traceable audit trail.

**Operations Phase**
Goal: deployment, infrastructure-as-code, and monitoring using accumulated context from prior phases.
AI applies context to manage IaC and deployments; team provides oversight. Specialized agent reviewers (code quality, FinOps, security) review PRs; human reviewer validates business intent and approves merge.

### Human Oversight

Human approvals are embedded at every decision gate. Reported figures: 10 to 26 human verification points per "bolt" (the AI-DLC unit of work, roughly equivalent to a sprint). AI cannot transition between phases autonomously — explicit human approval is required at each gate.

Each stage uses a plan-verify-generate cycle ensuring "AI's brain and human brain move to the same level" before execution proceeds.

### What "Steering Rules" Are Technically

Steering rules are markdown (and YAML-fronted markdown) files placed in tool-specific directories that instruct an AI coding agent to follow the AI-DLC workflow. They are not runtime code — they are context injected into the agent at session start.

Format varies by tool:

| Tool | Directory | File format |
|------|-----------|-------------|
| Kiro | `.kiro/steering/aws-aidlc-rules/` | Markdown steering files |
| Amazon Q Developer | `.amazonq/rules/aws-aidlc-rules/` | YAML-based rules |
| Cursor | `.cursor/rules/ai-dlc-workflow.mdc` | MDC (Markdown + YAML frontmatter) |
| Cline / Claude Code | `.clinerules/` or `CLAUDE.md` / `AGENTS.md` at root | Markdown |

All platforms share a `aws-aidlc-rule-details/` directory with subdirectories:
```
aws-aidlc-rule-details/
├── common/
├── inception/
├── construction/
├── extensions/
└── operations/
```

The core workflow file referenced across all platforms is `core-workflow.md`.

Cursor example frontmatter:
```yaml
---
description: "AI-DLC adaptive workflow for software development"
alwaysApply: true
---
```

### What "Adaptive Workflow" Means Precisely

The workflow is adaptive in two senses:
1. **Stage selection**: the framework detects workspace context and task complexity and automatically recommends which stages to include. A defect fix does not run all nine stages.
2. **Brownfield reverse engineering**: for existing codebases, AI-DLC automatically builds a semantic-rich context model (architecture, data flow, component relationships, library structures) before any construction begins, preventing unconstrained AI changes.

### Relationship to Kiro IDE

Kiro is Amazon's agentic IDE (kiro.dev). AI-DLC steering rules are natively implemented as Kiro Steering Files placed in `.kiro/steering/`. Kiro also implements spec-driven development (requirements.md, design.md, tasks.md) as its primary workflow. The GitHub repo `awslabs/aidlc-workflows` explicitly lists Kiro as a first-class target alongside Amazon Q Developer.

Kiro provides a "Vibe mode" vs. specification mode toggle; platform controls allow enabling/disabling rule files during development. Verification: `/context show` command lists active rules.

**FLAG**: The dev.to re:Invent notes mention "Kiro (likely Codeium's tool)" — this attribution is incorrect. Kiro is Amazon/AWS's own agentic IDE (kiro.dev, GitHub: kirodotdev/Kiro). The session note author was confused. All other sources confirm Kiro is AWS's product.

**FLAG**: The original AWS blog body text was not accessible (CSS/metadata only returned). Phase descriptions are reconstructed from: the open-source announcement blog, re:Invent session notes, and the ELEKS analysis. Claims about specific stage names and counts (9 stages, 10–26 verification points) come from the re:Invent session notes and secondary analyses, not directly from the primary AWS blog post.

---

## AEE-802: Spec-Driven Development

### Primary Sources

- Kiro specs documentation: https://kiro.dev/docs/specs/ — PAGE FETCHED, confirmed.
- Martin Fowler / Birgitta Böckeler SDD tool analysis: https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html — PAGE FETCHED, confirmed.
- Addy Osmani on good specs for AI agents: https://addyosmani.com/blog/good-spec/ — PAGE FETCHED, confirmed.
- GitHub spec-kit: https://github.com/github/spec-kit/blob/main/spec-driven.md

### What Makes a Spec Agent-Executable vs. Human-Readable

A human-readable spec prioritizes narrative clarity — the "what and why." An agent-executable spec prioritizes machine parsability and structural completeness.

Characteristics of agent-executable specs (from Addy Osmani's analysis of 2,500+ agent config files):

1. **Commands**: full executable commands with flags (`npm test`, `pytest -v`)
2. **Testing**: framework name, file locations, coverage expectations
3. **Project structure**: explicit paths (`src/` for application code, `tests/` for unit tests)
4. **Code style**: one real code snippet beats paragraphs of description
5. **Git workflow**: branch naming, commit format, PR requirements
6. **Boundaries**: three-tier graduated constraints:
   - Always do (safe, no approval needed)
   - Ask first (high-impact, needs human review)
   - Never do (hard stops — "never commit secrets" was the single most common useful constraint)

Additional format signals: OpenAPI schemas for APIs the agent will consume, `llms.txt` files summarizing documentation, explicit type definitions.

**The core distinction**: agent-executable specs define external behavior precisely — input/output mappings, preconditions/postconditions, invariants, constraints, interface types, integration contracts, sequential logic/state machines. Human-readable specs are narrative; agent-executable specs are behavioral contracts.

### Kiro's Spec-Driven Development Implementation

Every Kiro spec produces three artifacts stored under `.kiro/specs/`:

- `requirements.md` (or `bugfix.md` for bug fixes): user stories in "As a..." format with GIVEN/WHEN/THEN acceptance criteria
- `design.md`: component architecture, sequence diagrams, data flow, error handling strategy, testing strategy
- `tasks.md`: discrete, trackable implementation tasks with real-time status updates

Two entry points: Requirements-First (define stories then design) or Design-First (start with architecture then requirements).

`tasks.md` is the agent-execution layer: Kiro's task execution interface reads it, manages state transitions (in-progress/completed), and can run tasks individually or all at once. Spec files are committed to the repo alongside code, accumulating a collection of spec documents that document intent and design history.

Steering files (`.kiro/steering/`): default generated files are `product.md`, `structure.md`, `tech.md`. These provide persistent project context to every agent session.

**Kiro note from Fowler analysis**: Kiro proved oversized for small problems — generates excessive documentation for minor bug fixes. Teams should reserve full spec workflow for appropriately complex features.

### Known Failure Modes with Incomplete or Vague Specs

From Fowler/Böckeler analysis and Osmani's research:

1. **Agent misinterpretation**: agents treat existing code descriptions as new specifications, creating duplicate implementations
2. **Instruction non-compliance**: agents ignore detailed requirements even with large context windows — this is a shared context failure, not just a communication failure
3. **Over-eagerness**: agents follow instructions too literally, violating architectural constraints not explicit in the spec
4. **Non-determinism**: identical specs produce different code across multiple runs
5. **Separation challenges**: developers struggle maintaining functional vs. technical specification boundaries
6. **Context overload ("curse of instructions")**: performance drops as requirements accumulate; models struggle to satisfy many simultaneous constraints
7. **Missing specificity**: "React project" vs. "React 18 with TypeScript, Vite, and Tailwind CSS" — vague specs produce imprecise outputs

### DESIGN.md as a Spec/Steering Pattern

In Kiro's implementation, `design.md` (lowercase) is the second of three spec artifacts. It covers system architecture, component design, sequence diagrams, data flow, error handling, and testing strategy. This is the technical design layer that bridges requirements (what) and tasks (how).

GitHub spec-kit uses a similar pattern: `Constitution.md` (immutable high-level principles) + `Spec.md` (quality/requirement checklists) + supporting files (research, API descriptions, component descriptions, data models, plans, tasks) — totaling 8+ files per spec.

**FLAG**: No published standalone "DESIGN.md as a pattern" document was found. The pattern exists in practice within Kiro specs and spec-kit, but there is no dedicated "DESIGN.md spec pattern" RFC or canonical definition beyond these tool implementations.

---

## AEE-803: Steering Rules and Agent Instructions

### Primary Sources

- `awslabs/aidlc-workflows` GitHub: PAGE FETCHED, confirmed directory structure and formats (see AEE-801).
- Claude Code memory documentation: https://code.claude.com/docs/en/memory — PAGE FETCHED, full detail confirmed.
- Cursor rules documentation: https://docs.cursor.com/context/rules — redirected; supplemented with search results from official docs and community forum posts.

### How AWS AI-DLC Steering Rules Work Technically

See AEE-801 directory structure table. The key technical mechanism: steering rule files are placed in tool-specific directories and loaded as context at session start. They use markdown prose supplemented with YAML frontmatter (for Cursor) to instruct agents on which AI-DLC phase to operate in and what mandatory behaviors to follow. The `alwaysApply: true` frontmatter in Cursor MDC files ensures the workflow rule is included in every agent interaction regardless of what files are open.

### How Cursor Rules Work

Format: MDC files (`.mdc`) in `.cursor/rules/` directories. Frontmatter fields:

```yaml
---
description: "rule description"
globs: "src/**/*.ts"   # file patterns this rule applies to
alwaysApply: true/false
---
```

Four rule types:

| Type | Behavior |
|------|----------|
| Always | Applied to every prompt regardless of context |
| Auto Attached | Applied when files matching `globs` pattern are referenced |
| Agent Requested | AI decides when to include based on user intent; description field drives this |
| Manual | Only applied when explicitly attached by user |

Nested rules: rules in subdirectories auto-attach when files in that directory are referenced. Rules can be placed throughout the project hierarchy; more specific rules apply when their matching files are in context.

**Precedence**: no explicit "outer overrides inner" documented — Cursor loads all matching rules and concatenates them. The `alwaysApply: true` flag is the primary mechanism for ensuring a rule is never missed.

### How CLAUDE.md Works in Claude Code

Source: official docs at https://code.claude.com/docs/en/memory (fully fetched).

CLAUDE.md files are loaded into Claude's context window at session start as a user message (not system prompt). They are context, not enforced configuration — Claude reads and tries to follow them, but compliance is not guaranteed. Specificity and conciseness improve adherence.

**Scope hierarchy** (most specific wins for conflicts):

| Scope | Location | Shared with |
|-------|----------|-------------|
| Managed policy | `/Library/Application Support/ClaudeCode/CLAUDE.md` (macOS) | All org users; cannot be excluded |
| Project | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Team via source control |
| User | `~/.claude/CLAUDE.md` | Just you, all projects |
| Local | `./CLAUDE.local.md` (gitignored) | Just you, current project |

**Loading behavior**: Claude walks up the directory tree from the current working directory. All discovered CLAUDE.md and CLAUDE.local.md files are concatenated into context (not overridden). Within a directory, CLAUDE.local.md is appended after CLAUDE.md. Files in subdirectories load on demand when Claude reads files in those subdirectories (lazy loading).

**Path-scoped rules**: `.claude/rules/*.md` files can include YAML frontmatter with `paths` field (glob patterns) to scope rules to specific file types. Rules without `paths` load at launch alongside CLAUDE.md.

**Import syntax**: `@path/to/file` anywhere in CLAUDE.md imports another file, resolved at launch. Max depth 5 hops. Supports relative and absolute paths.

**Precedence model**: all files concatenate; no explicit override. More specific locations (local > user > project > managed) are listed later in context, so they are read last — Claude may favor later-appearing instructions for conflicts. Managed policy files cannot be excluded by any setting.

**Size guidance**: target under 200 lines per file. CLAUDE.md content loads in full (no truncation); MEMORY.md loads first 200 lines or 25KB. Longer files reduce adherence.

### Precedence When Multiple Rule Files Exist

For Claude Code: all files concatenate in order (managed → user → project → local → subdirectory). No override semantics; last-read instruction for a conflict may take precedence.

For Cursor: all matching rules (by type and globs) are concatenated. `alwaysApply: true` ensures inclusion. No documented override hierarchy between rule files.

For Kiro: steering files in `.kiro/steering/` are all loaded; no documented precedence among them.

### Three Verified Real Implementations

**1. AWS AI-DLC (awslabs/aidlc-workflows)**
Format: platform-specific (Kiro steering files, Amazon Q YAML rules, Cursor MDC, CLAUDE.md markdown). Verified via GitHub page fetch. Uses `core-workflow.md` as the primary workflow document with phase-specific subdirectories.

**2. Claude Code CLAUDE.md (this project)**
Format: markdown files at `./CLAUDE.md`, `~/.claude/CLAUDE.md`, `.claude/rules/*.md`. Verified directly — this session loads CLAUDE.md from `~/Projects/dev/CLAUDE.md` and `~/.claude/CLAUDE.md`. Documented behavior confirmed from official docs.

**3. Kiro steering files (kiro.dev documentation)**
Format: markdown files in `.kiro/steering/`. Default generated files: `product.md`, `structure.md`, `tech.md`. Verified via Kiro docs page fetch and search results. Used for persistent project context and AI-DLC integration.

**FLAG**: Cursor rules documentation at `docs.cursor.com/context/rules` returned a redirect; full rule format details were supplemented from search results and community forum posts, not directly from the official docs page. The four rule types and frontmatter fields are corroborated by multiple independent sources but the exact current official spec was not directly fetched.

---

## AEE-804: Human Oversight Patterns

### Primary Sources

- AWS re:Invent 2025 session notes: https://dev.to/kazuya_dev/ (fetched)
- Web search results from primary AWS blog and analysis articles

### Human Oversight Patterns in AI-DLC

**Mob Elaboration and Mob Construction** are the primary oversight rituals. These are not code review after the fact — they are concurrent validation during elaboration and construction, ensuring human judgment shapes AI output before it becomes canonical.

**Plan-Verify-Generate Cycle**: every AI-DLC stage follows this loop:
1. AI creates a plan
2. Humans validate the plan
3. AI executes
4. Humans verify outputs match intent

This cycle is the mechanism ensuring "AI's brain and human brain move to the same level" before each stage proceeds.

**Approval gates**: explicit human approval is required for every phase transition. AI cannot autonomously advance from Inception to Construction or Construction to Operations. Reported density: 10 to 26 verification points per bolt (work unit).

**Operations phase PR review**: the agent runs all required linters and opens a PR; specialized review agents (code quality, FinOps, security) review it; a human reviewer validates business intent and approves the merge. Human is the final gate before any merge.

**Semantic context building**: for brownfield projects, AI-DLC generates a semantic context model before any construction. This is an oversight mechanism — it prevents the AI from making sweeping undirected changes by anchoring it to an explicit model of the existing system.

### Approval Gate Patterns in Documented Agentic Systems

Beyond AI-DLC, documented patterns include:

- **Claude Code**: `permissions.deny` in managed settings enforces hard tool and command blocks regardless of what Claude decides. CLAUDE.md provides behavioral guidance (soft). The distinction between hard enforcement (settings) and behavioral guidance (CLAUDE.md) is explicit in the official docs.
- **Kiro**: "Vibe mode" vs. specification mode — users select their oversight level. Steering files can be toggled on/off per session.
- **Bassim Level 6 (harness engineering)**: security boundaries separate agents from credentials; human review at PR merge remains the final gate even in highly automated pipelines.

### Oversight Fatigue

**FLAG**: "Oversight fatigue" as a named concept specific to AI-DLC is not documented in any fetched primary source. The term appears in discussions about the volume of human verification points (10–26 per bolt). The AI-DLC methodology's response to this concern is: structured, time-boxed mob rituals (Mob Elaboration, Mob Construction) that batch human attention at defined checkpoints rather than requiring continuous monitoring. Bassim's Level 6 addresses this differently: automated backpressure (tests, linters, type systems) reduces the need for human intervention by enabling agents to self-correct, preserving human attention for final gates.

---

## AEE-805: Workflow Codification

### Primary Source

- Bassim Eledath: https://www.bassimeledath.com/blog/levels-of-agentic-engineering — PAGE FETCHED, confirmed.

### Bassim's Level 4: The "Codify" Step

Level 4 is called "Compounding Engineering," popularized by Kieran Klaassen. It operates as a **plan → delegate → assess → codify** loop.

The four steps:
1. **Plan**: define the task with enough context for the LLM to succeed
2. **Delegate**: hand off to the agent
3. **Assess**: evaluate the output
4. **Codify**: document what you learned — what worked, what broke, what pattern to follow next time

The codify step is what makes it "compound." The most common implementation: update your `CLAUDE.md` (or equivalent rules file) so the lesson is baked into every future session without re-specification. The agent discovers the pattern automatically going forward.

Practitioners of Level 4 are "hyper-aware of the context being fed to their LLM." When an agent makes a mistake, the instinct is to ask "what context was missing?" before assuming model incompetence.

### Formats for Codified Workflows That Agents Can Follow

From all sources synthesized:

- **CLAUDE.md / steering files / `.cursor/rules/*.mdc`**: prose-based rules injected at session start. Best for conventions, constraints, and "always/never" behaviors.
- **`tasks.md` (Kiro)**: structured task lists with completion state. Best for discrete, sequential work items.
- **`constitution.md` (GitHub spec-kit)**: immutable high-level principles applied to all changes. Best for invariants that must survive across feature iterations.
- **OpenAPI schemas**: machine-readable API contracts consumed directly by agents. Best for integration boundaries.
- **`llms.txt`**: condensed documentation summaries for LLM consumption. Best for large codebases where full docs exceed context.

Key principle from Addy Osmani: use an "extended TOC with summaries" for large codified workflows — hierarchical outlines agents can reference without token bloat. Avoid monolithic instruction dumps.

### Maintaining Codified Workflows Over Time

From Bassim Level 4 and Claude Code docs:

- Treat rules files as living documents. Update them every time the plan-delegate-assess-codify loop produces a new lesson.
- Trigger for update: Claude (or any agent) makes the same mistake twice. That's the signal a rule is missing.
- Use `.claude/rules/` for modular, topic-scoped rules rather than a single growing CLAUDE.md (Claude Code guidance: target under 200 lines per file).
- For Cursor: split rules by concern (testing.md, api-design.md, security.md) in `.cursor/rules/`.
- For AI-DLC: the `aws-aidlc-rule-details/` directory structure (common/, inception/, construction/, operations/, extensions/) provides a maintenance model — phase-specific rules are updated independently.
- Periodic audit: review for outdated or conflicting instructions. Conflicting rules produce arbitrary agent behavior.

---

## AEE-806: Agentic Quality Gates

### Primary Sources

- Bassim Eledath Level 6 (fetched): https://www.bassimeledath.com/blog/levels-of-agentic-engineering
- AI-DLC operations phase (via search and re:Invent notes)
- Addy Osmani spec failure modes (fetched)

### Automated Quality Gates in AI-DLC and Related Methodologies

**AI-DLC quality gates** (verified from multiple sources):
- Documentation is a mandatory deliverable, not optional — every stage produces traceable artifacts
- Operations phase: agent runs all PR-required linters before opening a PR
- Specialized review agents (code quality, FinOps, security) review PRs as automated gates
- Human reviewer is the final gate (business intent validation + merge approval)
- Built-in audit trail for every decision across all phases

**Bassim Level 6 (Harness Engineering) quality gates**:
- Tests, linters, and type systems as automated feedback mechanisms
- Agents are expected to run these tools and iterate until they pass, without human intervention
- CI pipelines prevent regressions in autonomous agent output
- Security boundaries: agents are separated from credentials as a hard constraint

**Spec-kit / Kiro quality gates**:
- Acceptance criteria in GIVEN/WHEN/THEN format are testable — they can map directly to automated tests
- Tasks in `tasks.md` have "clear outcomes" — completion criteria are explicit, not fuzzy
- Four-phase gated workflow (Specify → Plan → Tasks → Implement) with validation at each phase transition

### What "Backpressure" Means in This Context

Source: Bassim Level 6 description (confirmed).

Backpressure in agentic systems refers to the automated mechanisms that push back against agent output — the signals that tell an agent its work is not yet acceptable and it must iterate. Specifically: test failures, linter errors, type errors, and CI failures.

In contrast to human review (which is sparse and high-latency), automated backpressure is dense and immediate. The agent receives failure signals, diagnoses them, and retries within the same session — all without human involvement.

Bassim's framing: automated quality gates enable agents to "detect and correct errors independently." The design principle is "constraints over instructions" — hard automated checks are more reliable than prose instructions telling agents what to do. The methodology also recommends "tolerating non-blocking errors before final quality gates" — agents should not halt on warnings, only on hard failures.

### Role of Test Suites, Linters, and Type Systems as Agent Feedback

Synthesized from Bassim Level 6 and AI-DLC operations:

- **Test suites**: primary correctness signal. Agents run tests after each implementation step. Failing tests are the main driver of agent self-correction loops. In AI-DLC operations, the agent must run all required checks before opening a PR.
- **Linters**: style and pattern enforcement. In AI-DLC, linters run as a mandatory pre-PR gate. In harness engineering, linter output is part of the backpressure loop.
- **Type systems**: compile-time correctness. Type errors provide dense, precise feedback agents can act on without ambiguity. Particularly valuable because type errors identify specific locations and expected types.
- **CI pipelines**: prevent regressions from propagating. At Bassim Level 8, CI is explicitly noted as necessary to prevent regressions when autonomous agent teams operate without orchestrator oversight.

**The key insight**: these tools serve dual roles — they enforce quality for human developers AND they serve as the feedback signal that drives agent self-improvement loops. Designing quality gates with agent consumption in mind (clear error messages, machine-parseable output) improves agent effectiveness.

**FLAG**: specific quantitative claims about backpressure efficiency (e.g., "X% reduction in human review cycles") were not found in any fetched primary source. The backpressure concept as documented is qualitative.

---

## Source Index

Verified primary sources (pages fetched or confirmed accessible):
- https://github.com/awslabs/aidlc-workflows
- https://aws.amazon.com/blogs/devops/open-sourcing-adaptive-workflows-for-ai-driven-development-life-cycle-ai-dlc/
- https://dev.to/kazuya_dev/aws-reinvent-2025-introducing-ai-driven-development-lifecycle-ai-dlc-dvt214-32b
- https://kiro.dev/docs/specs/
- https://code.claude.com/docs/en/memory
- https://www.bassimeledath.com/blog/levels-of-agentic-engineering
- https://addyosmani.com/blog/good-spec/
- https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html

Primary sources not successfully fetched (body text inaccessible):
- https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/ (CSS/metadata only)
- https://repost.aws/articles/ARWSOgROfUTFq4vv5y2hTglw/ (403 Forbidden)
- https://docs.cursor.com/context/rules (redirect; content sourced from search results)
- https://kiro.dev/docs/ (root; specific sub-pages fetched successfully)
