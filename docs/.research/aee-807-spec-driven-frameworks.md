# AEE-807 Research Notes — Spec-Driven Development Frameworks

**Status:** complete
**Date:** 2026-04-17

---

## Verification Status

| Framework | Status | Primary Source URL |
|-----------|--------|-------------------|
| OpenSpec | VERIFIED | https://github.com/Fission-AI/OpenSpec |
| BMAD-METHOD | VERIFIED | https://github.com/bmad-code-org/BMAD-METHOD |
| Kiro AI-DLC spec mode | VERIFIED (partial) | https://kiro.dev/docs/specs/ |
| GitHub Spec Kit | VERIFIED | https://github.com/github/spec-kit |
| Superpowers | VERIFIED | local: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.0.7/skills/` |
| get-shit-done (GSD) | VERIFIED | https://github.com/gsd-build/get-shit-done |
| gstack | VERIFIED | https://github.com/garrytan/gstack |

Notes on verification:
- OpenSpec public canonical repo is `Fission-AI/OpenSpec` (40.7k stars, v1.3.0 as of 2026-04-11). Local installation at `/Users/alive/Projects/dev/openspec/` is a project-local copy of the same system.
- BMAD canonical repo moved from `bmadcode/BMAD-METHOD` to `bmad-code-org/BMAD-METHOD`. Both appear active; the latter is the current org.
- Kiro spec mode docs fetched successfully; regeneration model not explicitly documented — inferred from workflow description.
- GitHub Spec Kit fetched from both the raw README and the rendered GitHub page. The spec-kit README exposed a slightly richer command set than the three-command summary in the AEE outline (see anatomy notes).
- get-shit-done is a real, public, active framework — not a stub or private tool.
- gstack is a real, public framework by Garry Tan (YC president). Public repo confirmed.

---

## Per-Framework Anatomy Notes

---

### 1. OpenSpec

**Source:** `/Users/alive/Projects/dev/openspec/AGENTS.md` (local), `https://github.com/Fission-AI/OpenSpec` (canonical public)

#### 1. What it is
A spec-driven development CLI and workflow system for AI coding assistants. Keeps requirements in version-controlled Markdown under `openspec/`, separating current state (`specs/`) from proposed changes (`changes/`). Designed to prevent requirements from living only in chat history.

#### 2. Spec format
```
openspec/
  project.md                    # Project conventions and context
  specs/<capability>/
    spec.md                     # Requirements + scenarios (SHALL/MUST wording)
    design.md                   # Technical patterns (optional)
  changes/<change-id>/
    proposal.md                 # Why, what, impact
    tasks.md                    # Implementation checklist (checkbox format)
    design.md                   # Technical decisions (optional)
    specs/<capability>/
      spec.md                   # Delta file: ## ADDED|MODIFIED|REMOVED|RENAMED Requirements
  changes/archive/YYYY-MM-DD-<change-id>/
```
Spec deltas use section headers (`## ADDED Requirements`, etc.). Every requirement must include at least one `#### Scenario:` block with WHEN/THEN structure. Change IDs are kebab-case verb-led (`add-`, `update-`, `remove-`, `refactor-`).

#### 3. Lifecycle
Three sequential stages:
1. **Creating Changes** — scaffold proposal, spec deltas, tasks; run `openspec validate --strict`
2. **Implementing Changes** — read proposal → design → tasks; implement sequentially; mark tasks complete
3. **Archiving Changes** — post-deployment, move `changes/<id>/` to `changes/archive/YYYY-MM-DD-<id>/`; update `specs/` to reflect new truth

#### 4. Human gates
- **Approval gate** before Stage 2: "Do not start implementation until the proposal is reviewed and approved." Explicit hard stop.
- **Implicit review** after archiving: `openspec validate --strict` must pass.

#### 5. Regeneration model
**Spec-as-source-of-truth.** `specs/` is "current truth — what IS built." Changes are proposals against that truth. Archiving a change merges deltas back into specs, keeping specs authoritative. One execution cycle per approved change; re-running requires a new change proposal.

#### 6. Where it fits in the AEE 800-series
Closest match to AEE-801 (spec-first planning) but scoped to capability-level requirements management rather than project lifecycle orchestration. Lightweight enough for individual contributors; scales to team use via PR-reviewed proposals.

#### 7. When to choose it
- Teams that want requirements version-controlled alongside code
- Brownfield projects where capability boundaries already exist
- When AI coding assistants need grounded context beyond chat history
- When the team wants a CLI-first, low-ceremony spec discipline without a full framework

---

### 2. BMAD-METHOD

**Source:** `https://github.com/bmad-code-org/BMAD-METHOD`, `https://docs.bmad-method.org/`
**Stars:** ~37k (as of search results)

#### 1. What it is
"Breakthrough Method for Agile AI-Driven Development." A structured multi-agent framework that maps named specialist AI personas to agile roles, guiding projects from initial idea through deployment. Positions itself as replacing informal "vibe coding" with structured specs and quality gates. The key tagline is "agentic agile" — agile ceremonies and artifacts executed by specialized AI agents with humans in the steering role.

#### 2. Spec format
Agent personas defined in `.agent.yaml` files (BMAD v6), compiled to `.md` for IDE consumption. Project artifacts live under:
```
_bmad-output/
  planning-artifacts/
    PRD.md                  # Product Requirements Document
    architecture.md         # Technical decisions, tech stack, schema, source tree
    epics/                  # Epic and story files
  implementation-artifacts/
    sprint-status.yaml      # Sprint tracking
  project-context.md        # Implementation reference
```
Named agents: Mary (Business Analyst), Preston (Product Manager), Winston (Architect), Sally (Product Owner), Simon (Scrum Master), Devon (Developer), Quinn (QA Engineer). 34+ workflow templates. "Party Mode" allows multiple personas in one session.

#### 3. Lifecycle
Sequential agile phases, each owned by a named agent:
1. **Brainstorming / Idea Capture** (Mary/Preston) — business analysis, problem framing
2. **PRD Creation** (Preston) — product requirements document
3. **Architecture** (Winston) — system design, tech stack, data models
4. **Story Refinement** (Sally) — epics and user stories from PRD+architecture
5. **Sprint Planning** (Simon) — story sequencing
6. **Development** (Devon) — implementation per story
7. **QA / Testing** (Quinn) — test strategy and verification
8. "Scale-Domain-Adaptive Intelligence" adjusts planning depth automatically based on project size.

#### 4. Human gates
- Phase transitions require human steering ("human thinking in partnership with the AI")
- `bmad-help` skill can be queried at any point for "what's next" guidance
- Explicit decision points at PRD approval, architecture approval, and story acceptance
- Human responsible for scope direction; agents handle execution within scope

#### 5. Regeneration model
**Spec-as-input with cascade.** PRD drives architecture generation; architecture drives story creation; stories drive implementation. If PRD changes, downstream artifacts must be regenerated through the agent chain. Not fully automated — each regeneration step requires human-agent collaboration.

#### 6. Where it fits in the AEE 800-series
Full project lifecycle coverage — maps to AEE-801 through ~AEE-810 in sequence. Most comprehensive of the frameworks surveyed. More appropriate for greenfield team projects than individual capability specs.

#### 7. When to choose it
- Greenfield projects needing end-to-end structure from idea to deployment
- Teams adopting agile ceremonies with AI augmentation
- When named role accountability across planning, development, and QA is desired
- Organizations already familiar with Scrum/Kanban ceremonies

---

### 3. Kiro AI-DLC Spec Mode

**Source:** `https://kiro.dev/docs/specs/`
Note: The broader AI-DLC methodology (covered in AEE-801) is at `https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/`. The kiro.dev docs focus specifically on the spec mode within the Kiro IDE.

#### 1. What it is
Kiro's built-in "spec mode" — a structured artifact workflow within the Kiro AI IDE. Transforms a high-level feature idea into three sequentially produced documents before any code is written. Distinct from the broader AWS AI-DLC methodology; this is the per-feature planning layer inside the Kiro tool.

#### 2. Spec format
All artifacts stored under `.kiro/specs/<feature-name>/`:
```
.kiro/specs/<feature-name>/
  requirements.md   # User stories with acceptance criteria (feature)
                    # OR bugfix.md — bug analysis documentation (bug fix track)
  design.md         # Technical architecture, sequence diagrams, implementation strategy
  tasks.md          # Discrete, trackable implementation tasks with clear outcomes
```

#### 3. Lifecycle
Three sequential phases (gated — each phase completes before the next begins):
1. **Requirements/Analysis** — define what needs building or fixing; output: `requirements.md` or `bugfix.md`
2. **Design** — technical architecture and implementation approach; output: `design.md`
3. **Tasks** — execution breakdown; output: `tasks.md` with real-time progress tracking

Supports iteration: requirements or design can be modified, with downstream tasks regenerating accordingly.

#### 4. Human gates
- Phase boundary between Requirements and Design (human reviews requirements before design begins)
- Phase boundary between Design and Tasks (human reviews design before task generation)
- Task execution interface allows running "individually or all at once" — human chooses execution mode
- Real-time status display during task execution

#### 5. Regeneration model
**Spec-as-source-of-truth with downstream regeneration.** Design and tasks regenerate when upstream artifacts change. The three-file structure makes traceability explicit: tasks trace to design, design traces to requirements. Regeneration is iterative within a single feature scope.

#### 6. Where it fits in the AEE 800-series
Feature-scoped per-ticket planning within a larger development workflow. Most similar to the story/task planning layer (AEE-802 or equivalent). IDE-native, so it integrates directly into the code editing session rather than living in a separate file system layer.

#### 7. When to choose it
- Teams already using the Kiro IDE
- Feature-by-feature planning within a project already underway
- When IDE-integrated spec tracking (real-time task status) is preferred over file-only workflows
- Bug fix workflows (the `bugfix.md` variant is purpose-built for this)

---

### 4. GitHub Spec Kit

**Source:** `https://github.com/github/spec-kit` (71k stars per search results)

#### 1. What it is
An open-source toolkit from GitHub for introducing Spec-Driven Development to any project. Provides slash commands that transform requirements into specifications, then plans, then executable tasks. Framework-agnostic and tech-stack-agnostic. Philosophy: "specifications become executable, directly generating working implementations rather than just guiding them."

#### 2. Spec format
All artifacts stored under `specs/<feature>/`:
```
.specify/memory/constitution.md   # Governing principles (from /speckit.constitution)
specs/<feature>/
  spec.md                         # Feature requirements, user stories, acceptance criteria
  plan.md                         # Technical implementation approach
  tasks.md                        # Ordered, dependency-tracked task list
```
Supporting docs may include data models, API contracts, research documents.

#### 3. Lifecycle
Five-stage structured progression (slash-command driven):
1. `/speckit.constitution` — establish project principles (one-time or per-project)
2. `/speckit.specify` — define functional requirements; output: `spec.md`
3. `/speckit.clarify` — clarify underspecified areas (recommended before planning)
4. `/speckit.plan` — technical implementation strategy; output: `plan.md`
5. `/speckit.tasks` — dependency-ordered task list; output: `tasks.md`
6. `/speckit.implement` — systematic task execution
7. Optional: `/speckit.taskstoissues` (convert to GitHub Issues), `/speckit.analyze` (cross-artifact consistency), `/speckit.checklist` (quality checklists)

Note: The AEE outline described three stages (/specify, /plan, /tasks). The actual command set is richer — seven primary commands plus optional utilities.

#### 4. Human gates
- Clarification phase before planning: "required before planning to reduce downstream rework"
- Plan validation: human review before task generation
- Review checklist: manual sign-off on acceptance criteria
- Tech stack confirmation: explicit human approval before implementation

#### 5. Regeneration model
**Spec-as-source-of-truth with iterative refinement.** `/speckit.refine` updates specs mid-implementation. Plans regenerate downstream artifacts when modified. Tasks reorganize automatically when plans change. Artifacts are living documents; re-running commands on changed specs produces updated downstream artifacts.

#### 6. Where it fits in the AEE 800-series
Project-level planning layer compatible with any downstream implementation approach. Framework-agnostic positioning means it can sit in front of any AEE execution workflow (subagent, parallel, inline). Similar to OpenSpec in positioning but broader in scope (constitution → spec → plan → tasks vs. capability-centric deltas).

#### 7. When to choose it
- Framework-agnostic projects not committed to a specific IDE or agent runtime
- Teams wanting GitHub-native artifact management (issues integration via `/speckit.taskstoissues`)
- When cross-artifact consistency validation is important
- Projects that need to move from greenfield (0-to-1), to creative exploration, to brownfield enhancement across the same methodology

---

### 5. Superpowers

**Source:** Local skills at `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.0.7/skills/brainstorming/SKILL.md` and `writing-plans/SKILL.md`

#### 1. What it is
The Superpowers skill tree is Claude Code's own built-in SDD framework, distributed as a plugin. It provides a structured brainstorming → spec writing → plan writing → execution pipeline. Each stage is implemented as an invokable skill. The framework enforces a hard gate: no implementation until a written design is approved.

#### 2. Spec format
```
docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md   # Design document (from brainstorming)
docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md    # Implementation plan (from writing-plans)
```
Plans use checkbox syntax (`- [ ]`) for task tracking. Every plan starts with a required header block: Goal, Architecture, Tech Stack. Tasks are bite-sized (2-5 minutes each), TDD-first, with exact file paths and complete code in every step.

#### 3. Lifecycle
Four skills executed in sequence:
1. **brainstorming** — collaborative design dialogue; produces design doc; hard gate before any code
2. **writing-plans** — translates approved design doc into implementation plan with TDD tasks
3. **executing-plans** (or **subagent-driven-development**) — executes the plan task-by-task with checkpoints
4. **verification-before-completion** — confirms work meets spec before claiming completion

Supporting skills: `systematic-debugging`, `receiving-code-review`, `requesting-code-review`, `finishing-a-development-branch`.

#### 4. Human gates
Three explicit gates:
- **Design approval gate** (in brainstorming): "Do NOT invoke any implementation skill... until you have presented a design and the user has approved it." Hard coded as `<HARD-GATE>`.
- **Spec review gate** (post-brainstorming): "Please review the spec file and let me know if you want to make any changes before we start writing out the implementation plan." Agent waits for explicit user approval.
- **Plan approval gate** (writing-plans → executing-plans): Human chooses execution mode (subagent vs. inline); each task has a review checkpoint.

#### 5. Regeneration model
**Spec-as-input, one execution cycle per spec.** The design doc is written once, approved, then consumed by `writing-plans` to produce a plan. Plans are not auto-regenerated when specs change — a spec change requires re-running `brainstorming` or `writing-plans` manually. Within execution, each task is a fresh context window (subagent mode) or a batch with checkpoints (inline mode).

#### 6. Where it fits in the AEE 800-series
Native to the AEE ecosystem — this is the reference implementation of the SDD pattern used in the AEE series itself. Every AEE article that shows a spec-to-plan-to-execute workflow is implicitly demonstrating the Superpowers lifecycle.

#### 7. When to choose it
- Default choice for Claude Code users already in the Superpowers ecosystem
- When tight integration between brainstorming, planning, and execution is wanted in a single tool
- Solo developer or small team workflows (not designed for multi-team artifact handoffs)
- Projects where a human-in-the-loop design approval is non-negotiable

---

### 6. get-shit-done (GSD)

**Source:** `https://github.com/gsd-build/get-shit-done` (GSD v1), `https://github.com/gsd-build/gsd-2` (GSD v2)

#### 1. What it is
A meta-prompting and context engineering framework designed to solve "context rot" — quality degradation as AI fills its context window with accumulated conversation. GSD externalizes project state into versioned Markdown files and distributes work across isolated, fresh subagent contexts. Originally went viral as a prompt framework for Claude Code; now evolving toward a full coding agent system.

#### 2. Spec format
```
.planning/
  PROJECT.md          # Vision statement
  REQUIREMENTS.md     # v1/v2 scoped features with phase mapping
  ROADMAP.md          # Phased delivery timeline
  STATE.md            # Decisions, blockers, position — memory across sessions
  research/           # Domain investigation outputs
  phase-N/
    {N}-CONTEXT.md    # Implementation preferences from discussion
    {N}-RESEARCH.md   # Ecosystem/approach investigation
    {N}-{task}-PLAN.md  # XML-structured atomic task specs
    {N}-SUMMARY.md    # Execution results and git commits
  quick/              # Ad-hoc task tracking
```
Per-task plans use XML structure for machine-readable atomic specs. Three core directories: `commands/` (entry point orchestrators), `agents/` (specialized system prompts), `.planning/` (project state).

#### 3. Lifecycle
Seven-stage repeating cycle with slash commands:
1. `/gsd-new-project` — interview → research → requirements extraction → roadmap creation
2. `/gsd-discuss-phase` — human shapes implementation preferences before technical planning
3. `/gsd-plan-phase` — research + atomic task planning + verification loop
4. `/gsd-execute-phase` — parallel wave execution with fresh context per task
5. `/gsd-verify-work` — manual user acceptance testing; human confirms deliverables
6. `/gsd-ship` — PR creation from verified work
7. `/gsd-complete-milestone` — archive and tag release

Supporting: `/gsd-pause-work` (creates `HANDOFF.json`), `/gsd-resume-work`, `/gsd-map-codebase` (brownfield init), `/gsd-workstreams` (parallel milestones).

#### 4. Human gates
- `/gsd-discuss-phase` is explicitly a human shaping step before any planning begins
- `/gsd-verify-work` requires manual user acceptance testing — human confirms work
- Phase transitions require human confirmation before re-planning or re-executing
- Built-in quality gates: schema drift detection, security enforcement, scope reduction prevention

#### 5. Regeneration model
**Spec-as-source-of-truth, modular regeneration.** "Preserve completed work, regenerate only what changed." Phases can be inserted, removed, or adjusted without rebuilding the full project. Failure recovery spawns debug agents and creates fix plans for re-execution. Session pause/resume via `HANDOFF.json` enables cross-session continuity without context loss.

#### 6. Where it fits in the AEE 800-series
Sits in the execution and orchestration layer (closer to AEE-805 or AEE-810 territory than pure spec planning). Stronger on execution reliability and context management than on upfront spec formalization. The REQUIREMENTS.md and ROADMAP.md provide a lighter spec layer compared to OpenSpec or BMAD.

#### 7. When to choose it
- Projects where context rot across long sessions is the primary pain point
- Developers who want phase-gated, fresh-context execution without a full BMAD-style agent team
- Brownfield projects (explicit `/gsd-map-codebase` support)
- When parallel execution of independent tasks within a phase is needed

---

### 7. gstack

**Source:** `https://github.com/garrytan/gstack` (Garry Tan, YC President)

#### 1. What it is
A collection of 23 specialized AI skills (implemented as Claude Code slash commands in Markdown) that give a solo developer the equivalent of a full startup team. Role-based rather than spec-artifact-based: each skill embodies a specialist's judgment with distinct persona, priorities, and constraints. Philosophy: "Twenty-three specialists and eight power tools, all slash commands, all Markdown, all free, MIT license."

#### 2. Spec format
Skills live in `~/.claude/skills/gstack/` as executable Markdown files. Project state tracked via:
- Design docs (written by `/office-hours`, read by all downstream skills)
- Test plans (generated by `/plan-eng-review`, consumed by `/qa`)
- Learnings (managed via `/learn`) — project-local `.gstack/` and global `~/.gstack/`

No dedicated spec artifact directory per feature (unlike OpenSpec or Kiro). Specs emerge from the skill outputs (office hours notes, eng review plans, design review outputs) rather than being written to a prescribed schema.

#### 3. Lifecycle
Seven-stage sprint structure:
1. **Think** (`/office-hours`) — product interrogation, forcing questions, premise challenge
2. **Plan** (`/plan-ceo-review`, `/plan-eng-review`, `/plan-design-review`) — strategic challenge, architecture lockdown, design rating
3. **Build** — implementation guided by locked plans
4. **Review** (`/review`, `/codex`) — staff-engineer-level code audit with auto-fixes
5. **Test** (`/qa`) — real browser testing, bug discovery, regression testing
6. **Ship** (`/ship`) — test verification, PR creation, documentation sync
7. **Reflect** (`/retro`) — weekly retrospectives with per-person breakdowns

Roles are mutually aware: design review reads the eng plan; QA tests against the design spec; ship verifies review findings are closed.

#### 4. Human gates
gstack surfaces only high-judgment decisions:
- **Taste decisions** — design choices, scope tradeoffs, architectural direction
- **Assumption validation** (`/office-hours`) — forces reframing before code
- **Race conditions and security findings** — human approval before `/review` auto-fixes
- **Scope expansion** (`/plan-ceo-review`) — four modes: Expansion, Hold Scope, Reduction, Pivot

Auto-fixes obvious issues (linting, simple bugs); escalates only items requiring human reasoning.

#### 5. Regeneration model
**Role-cascade with compound learning.** When plans change, regeneration cascades: new CEO review → updated eng review → adjusted test strategy. Learnings compound across sessions via `/learn`. Role isolation means each specialist only receives the context relevant to their function — not a full context replay. This is the most distinctive regeneration approach among the seven frameworks: learning-augmented rather than artifact-versioned.

#### 6. Where it fits in the AEE 800-series
Sprint-level orchestration with role-based quality assurance. Complements spec-formalization frameworks (use OpenSpec or Spec Kit for requirements, gstack for execution-time review quality). Most relevant to AEE articles covering review loops, QA integration, and role-based agent teams.

#### 7. When to choose it
- Solo developers who want startup-team accountability without a real team
- Projects where review quality (security, performance, UX) is as important as spec quality
- When compounding learned project context across sessions is valued
- Greenfield projects where the product direction is still being discovered (office-hours-first model)

---

## Discovery Findings

### Verified Candidates from Sweep

| Name | URL | Description | Recommended Tier |
|------|-----|-------------|-----------------|
| claude-task-master | https://github.com/eyaltoledano/claude-task-master | AI-powered PRD-to-task decomposition system; serves one task at a time to prevent hallucination; TDD autopilot mode as of v0.30.0; 15k+ stars in first 9 weeks | Tier 1 |
| Agent OS | https://github.com/buildermethods/agent-os | Extracts codebase standards, deploys relevant standards per task, shapes implementation specs; lighter-weight than BMAD | Tier 2 |
| spec-driven-agentic-development | https://github.com/marcelsud/spec-driven-agentic-development | Methodology template: context.md + requirements.md + tasks.md with slash commands; minimal framework | Honorable mention |
| Spec Kitty | https://github.com/Priivacy-ai/spec-kitty | CLI workflow: spec → plan → tasks → spec-kitty next (agent loop) → review → merge; Kanban, git worktrees, auto-merge | Tier 2 |
| deeplearning.ai SDD course files | https://github.com/https-deeplearning-ai/sc-spec-driven-development-files | Course materials for "Spec-Driven Development with Coding Agents" (DeepLearning.AI + JetBrains); not a framework but a learning resource | Skip (reference only) |
| GSD v2 | https://github.com/gsd-build/gsd-2 | Evolution of GSD: longer autonomous runs, improved context engineering | Honorable mention (covered under GSD v1 entry) |

### Notable Non-Candidates

- **wshobson/agents** — Agent library (29k stars), not an SDD framework per se
- **Claude-Flow** — Orchestration layer (14k stars); workflow execution, not spec planning
- **AGENTS.md pattern** — Convention for per-repo agent instructions; lightweight, not a full framework

### Star Count Snapshot (for tier calibration)
From Ry Walker's framework comparison (cited in search results):
- Anthropic Skills (Superpowers): 73k
- GitHub Spec Kit: 71k
- Superpowers standalone: 57k
- BMAD Method: 37k
- OpenSpec: 40.7k (Fission-AI/OpenSpec, confirmed)
- wshobson/agents: 29k
- Claude-Flow: 14k

---

## Cross-Framework Synthesis

### Four-Dimension Comparison

| Framework | Spec Granularity | Lifecycle Integration | Human Gate Placement | Regeneration Model |
|-----------|-----------------|----------------------|---------------------|-------------------|
| OpenSpec | Capability-level requirements + scenarios (SHALL/MUST) | Change proposal → implementation → archive | Approval before implementation; explicit hard gate | Spec-as-source-of-truth; delta-merge on archive |
| BMAD-METHOD | Project-level: PRD → architecture → epics → stories | Full project lifecycle (7+ phases) | Phase transitions; PRD approval; architecture approval | Spec-as-input; downstream cascade (PRD → arch → stories) |
| Kiro spec mode | Feature-level: requirements → design → tasks | Per-feature, IDE-native | Between each of the three phases | Spec-as-source-of-truth; downstream regeneration on edit |
| GitHub Spec Kit | Feature-level: constitution → spec → plan → tasks | Per-feature, framework-agnostic | Clarification gate; plan validation; tech stack confirmation | Spec-as-source-of-truth; iterative refinement via /refine |
| Superpowers | Design-level: design doc → implementation plan | Session-scoped; skill chain | Design approval (hard gate); spec review; plan approval | Spec-as-input; one cycle per approved spec |
| GSD | Phase-level: REQUIREMENTS.md + ROADMAP.md + XML task plans | Project lifecycle, session-safe | Phase discussion; verify-work acceptance testing | Spec-as-source-of-truth; modular phase regeneration |
| gstack | Sprint-level: office-hours notes → eng/design plans | Sprint-scoped; role-based | Taste decisions; security findings; scope challenges | Role-cascade + compound learning (no artifact versioning) |

### Pattern Observations

1. **Granularity determines lifecycle fit.** Capability-level frameworks (OpenSpec, Kiro spec mode) integrate naturally at the story/ticket level. Project-level frameworks (BMAD, GSD) own the full lifecycle. Sprint-level frameworks (gstack, Superpowers) attach to the execution phase. Mixing granularity levels (e.g., OpenSpec for requirements + gstack for review) is viable and common.

2. **Human gates cluster at the same transition points across all frameworks.** Every framework places an explicit gate between "define what to build" and "start building." The second most common gate is between "planned" and "shipped" (verify/review). The frameworks differ in how automated the gate handoff is, not in where it falls.

3. **Regeneration splits cleanly into two philosophies.** Four frameworks (OpenSpec, Kiro, Spec Kit, GSD) treat specs as the durable source of truth and regenerate downstream artifacts when specs change. Three frameworks (BMAD, Superpowers, gstack) treat specs as one-time inputs that drive a generation cycle; changes require re-entering the workflow from an earlier stage.

4. **Context management is an orthogonal concern.** GSD was built explicitly to solve context rot; gstack solves it via role isolation; Superpowers solves it via subagent dispatch. OpenSpec, Kiro, and Spec Kit are context-agnostic — they define what gets produced, not how long contexts stay alive. Claude-task-master (discovery candidate) solves context rot at the task-serving layer without owning spec authorship.

5. **Tooling independence varies widely.** Kiro spec mode requires the Kiro IDE. gstack requires Claude Code. Superpowers is Claude Code native but distributed as a plugin. BMAD, OpenSpec, GSD, and Spec Kit are agent-agnostic (work with Claude Code, Cursor, Windsurf, Codex, etc.). For articles emphasizing portability, the agent-agnostic frameworks are safer examples.

### Design Thesis Validation

**Claim:** "SDD frameworks differ along four dimensions that matter more than feature lists."

**Assessment: Y, with one refinement.**

The four-dimension comparison above shows that the frameworks cluster meaningfully along spec granularity, lifecycle integration, human gate placement, and regeneration model — and that these four axes predict fit better than any individual feature (slash commands, file formats, persona names). A team choosing between OpenSpec and BMAD does not benefit from comparing command counts; they benefit from knowing that OpenSpec is capability-scoped with delta-merge regeneration while BMAD is project-scoped with cascade regeneration.

The refinement: **context management** should be considered a fifth dimension. GSD, gstack, and claude-task-master each have distinct context management strategies that are load-bearing parts of their design — not incidental implementation details. As sessions grow longer and multi-agent patterns become standard, how a framework handles context persistence will increasingly drive adoption.

---

## Open Questions for Author

1. **Kiro regeneration model** — kiro.dev docs do not explicitly specify whether design.md and tasks.md auto-regenerate when requirements.md is edited, or whether re-running the phase command is manual. Recommend testing this in the Kiro IDE before publishing or marking as "inferred."

2. **BMAD repo canonical URL** — Two orgs exist: `bmadcode/BMAD-METHOD` (original, appears archived) and `bmad-code-org/BMAD-METHOD` (current). Author should verify which URL to cite as canonical for publication.

3. **claude-task-master tier decision** — This framework has substantial adoption (15k+ stars, 90% error reduction claims, DeepLearning.AI course integration) and a distinct regeneration model (task-serving rather than spec-versioning). It is arguably Tier 1 material. Author should decide whether AEE-807 covers it or defers to a dedicated article.

4. **gstack spec granularity question** — gstack does not use a prescribed spec artifact format; specs emerge from skill outputs. This makes it harder to teach "how to write a gstack spec" — the article may need to frame gstack as a review/execution framework that complements a spec-writing framework rather than a standalone SDD choice.

5. **OpenSpec public vs. local disambiguation** — The local copy at `/Users/alive/Projects/dev/openspec/` is a project-specific installation. The canonical public repo is `Fission-AI/OpenSpec`. The article should cite the public repo; confirm the local version is not a fork with diverged conventions.

6. **Superpowers skill version** — Research based on v5.0.7. Confirm this is the current release before publication; skill SKILL.md content may have changed in later versions.
