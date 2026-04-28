# AEE-507: Superpowers — A Skills-System Case Study — Article Design

**Date:** 2026-04-19
**Status:** Approved design, pending implementation plan
**Article ID:** AEE-507
**Category:** Agent Skills (500s)
**Files:**
- `docs/en/Agent Skills/507.md`
- `docs/zh-tw/Agent Skills/507.md`

## Purpose

Give the 500s articles (500–506) a concrete case study of a mature, actively used skills system in the wild. AEE-500 through AEE-506 describe the skills ecosystem abstractly (skill vs. tool, skill anatomy, design, composition, management, governance). Readers benefit from seeing those concepts realized in one specific, documented system. Superpowers — an open-source multi-platform agent skill plugin — is a defensible study object: actively maintained, publicly documented, used by the article's author, and illustrative of patterns that do not appear in simpler skill systems.

This article is distinct from [AEE-807](../Agentic%20Development%20Workflows/807), which treats Superpowers as one entry in an SDD-framework survey. AEE-807's coverage is short-form and comparative; this article is long-form and illustrative — it uses Superpowers to make the 500s concepts concrete.

## Non-goals

- Not a Superpowers user manual or installation guide. Users should go to the project's own docs for that.
- Not a recommendation to adopt Superpowers. The article treats it as an example of a realized skills system, not a prescriptive choice.
- Not a neutral survey. This is an admitted, disclosed case study from a single framework; readers should apply judgment when generalizing.
- Not a re-statement of AEE-500 through AEE-506. The article points back to those articles for the abstract concepts and uses Superpowers to make them concrete.
- Not a comparison against other skill systems. The article stays focused on Superpowers as one mature example.

## Scope and Boundary vs. Existing 500s Articles

| Concern | Owned by | AEE-507 treatment |
|---|---|---|
| Skills vs. Tools distinction | AEE-500 | Points to 500 |
| Skill anatomy (name, description, invocation condition, body, scope) | AEE-501 | Illustrates with Superpowers' SKILL.md format |
| Ecosystem (where skills come from) | AEE-502 | Illustrates with Superpowers' multi-platform plugin model |
| Skill design principles | AEE-503 | Illustrates with Superpowers' behavior-shaping patterns |
| Skill composition | AEE-504 | Illustrates with Superpowers' 7-stage pipeline |
| Skill management (versioning, lifecycle, retirement) | AEE-505 | Illustrates with writing-skills as TDD + tests/ harness |
| Skill governance | AEE-506 | Illustrates with contributor-discipline stance |
| SDD-framework survey entry for Superpowers | AEE-807 | Points to 807 for the survey-level treatment |

## Core Thesis

Superpowers is a useful case study because it goes further than most skill systems in three ways:

1. **Behavior-shaping as engineering.** Skills are treated as instruments tuned against pressure-tests, not as prose documentation. Patterns like `EXTREMELY-IMPORTANT` tags, Red Flags anti-rationalization tables, `HARD-GATE` approval markers, and `SUBAGENT-STOP` differentiation exist to make skills actually alter agent behavior under rationalization pressure.

2. **Meta-skill closure.** `writing-skills` is itself a skill that treats skill creation as TDD: write a baseline pressure scenario, watch the agent fail, author the skill, verify compliance, refactor to close loopholes. The `tests/` directory holds scenario evals across platforms.

3. **Composed library over large library.** 14 general-purpose skills compose through explicit `REQUIRED SUB-SKILL` pointers, with hard gates between stages. The library stays deliberately small; domain-specific additions are rejected at the contribution boundary.

These three properties realize AEE-503 (design), AEE-504 (composition), and AEE-505/506 (management/governance) concretely.

## Article Structure

Standard AEE template. No `**RFC 2119:**` section label (post-cleanup convention). No emoji.

### Context (~200 words)

- The 500s describe the skills ecosystem abstractly. A single concrete study of a mature skill system teaches what abstract coverage cannot.
- Why Superpowers: open-source, multi-platform, actively maintained, publicly documented, operationally used by the author. The choice is defensible, not arbitrary.
- Disclosure: this article is written by someone with direct operational experience using Superpowers. Every skill referenced in the article has been invoked in the session that produced the article. That is the explicit premise of the case study, not a conflict to hide.
- Scope pointer: AEE-807 surveys Superpowers at framework level. This article goes narrower and deeper, using Superpowers to make the 500s concepts concrete.

### Design Think (~300 words)

- What makes Superpowers a useful study object for the 500s: (1) small skill library with sharp composition rules (14 skills, each focused, explicitly chaining via `REQUIRED SUB-SKILL`), (2) behavior-shaping as engineering (skills tuned against pressure tests), (3) meta-skill closure (`writing-skills` is itself a skill; `tests/` holds scenario evals across platforms).
- How to read the case study: one valid realization, not "the" correct design. The 500s describe a space of designs; Superpowers occupies one point in that space.
- Lead-in + MUST/SHOULD/MAY bullets flowing directly:
  - Readers MUST treat Superpowers as one illustrative example, not as a reference implementation to copy mechanically.
  - Teams building skill systems SHOULD take from this case study the patterns that address problems they actually have, not the patterns that look novel.
  - Engineers MUST NOT assume every behavior-shaping pattern shown here is necessary for every skill library — Superpowers deliberately adopts them because its skills are rigid and process-critical.
  - Readers interested in a neutral survey of skill-distributing systems SHOULD read AEE-502 for the ecosystem view and AEE-807 for framework-level comparisons.

### Deep Dive — six subsections

**1. Framework overview** (~200 words)

- 14 skills grouped into four categories: Testing (test-driven-development), Debugging (systematic-debugging, verification-before-completion), Collaboration (brainstorming, writing-plans, executing-plans, subagent-driven-development, dispatching-parallel-agents, requesting-code-review, receiving-code-review, using-git-worktrees, finishing-a-development-branch), Meta (writing-skills, using-superpowers).
- Multi-platform distribution: Claude Code plugin, Cursor plugin, Codex, OpenCode, Copilot CLI, Gemini CLI. Each target has its own init directory (`.claude-plugin/`, `.codex/`, `.cursor-plugin/`, `.opencode/`, plus `gemini-extension.json` and `GEMINI.md`).
- Interop mechanism: `AGENTS.md -> CLAUDE.md` symlink. Live realization of the AEE-808 interop pattern.
- Session hooks: `hooks/session-start` auto-loads `using-superpowers` at every session. Explains why the skill appears to fire without explicit invocation.

**2. Skill anatomy** (~250 words) — concrete realization of AEE-501

- SKILL.md with YAML frontmatter. Two required fields: `name` (letters, numbers, hyphens) and `description`. Max 1024 characters total.
- `description` describes WHEN to use, not WHAT it does. Phrased as "Use when ..." to focus on triggering conditions, which is what the harness actually matches against user intent.
- Optional supporting files: heavy references (100+ lines), reusable templates, sub-prompts for specialized review agents. Kept separate from the SKILL.md to keep the main document scannable.
- Skill types: Rigid (TDD, debugging — follow exactly) vs. Flexible (patterns — adapt principles). Each skill states its own type.
- Directory pattern: `skills/<skill-name>/SKILL.md` plus optional companion files. Flat namespace — all skills in one searchable set.
- Example: show `using-superpowers`'s frontmatter and the structure of its body as an anchor.
- Contrast with Anthropic's default skill-authoring guidance: Superpowers' CLAUDE.md explicitly notes it differs. The behavior-shaping patterns in the next section are the reason.

**3. Behavior-shaping patterns — the distinctive contribution** (~350 words)

- The problem Superpowers' skills are engineered against: agents under time pressure rationalize around skills. "This feels productive" leads to undisciplined action; "I know what that means" leads to knowing-the-concept without invoking-the-skill.
- `EXTREMELY-IMPORTANT` tags wrapping a "1% chance a skill might apply" rule. Forces checking before any response, including clarifying questions.
- Red Flags tables. Each row names a rationalizing thought and its counter. Example: "This is just a simple question" → "Questions are tasks. Check for skills." The table is exhaustive, not representative — it enumerates the known failure modes observed in pressure tests.
- `HARD-GATE` tags marking approval checkpoints. Brainstorming's hard-gate: "Do NOT invoke any implementation skill ... until you have presented a design and the user has approved it."
- `SUBAGENT-STOP` tags that suppress skill activation when dispatched as a subagent. Example in `using-superpowers`: "If you were dispatched as a subagent to execute a specific task, skip this skill." Subagents get context-appropriate instructions rather than the top-level entry-point behavior.
- Graphviz `digraph` process flow diagrams embedded in SKILL.md. The flow is the canonical decision tree — readers and agents both follow the same graph.
- Deliberate phrasing. "Your human partner" rather than "the user." Superpowers' CLAUDE.md explicitly calls this out as non-interchangeable.
- These patterns are novel relative to plain-markdown skill docs. They exist because Superpowers treats skills as behavioral code that requires engineering discipline, not as optional advice the agent may choose to follow.

**4. Workflow composition** (~200 words) — concrete realization of AEE-504

- Seven-stage pipeline: brainstorming → using-git-worktrees → writing-plans → subagent-driven-development / executing-plans → test-driven-development → requesting-code-review → finishing-a-development-branch.
- Each stage hard-gates before the next. Brainstorming will not transition to writing-plans until the design is approved; writing-plans will not transition to execution until the plan is written and reviewed.
- Skills reference each other by name through explicit `REQUIRED SUB-SKILL` headers. The pipeline is encoded in the skills themselves, not in an external orchestrator.
- Terminal states: each skill's process flow has an explicit terminal node pointing to the next skill. Brainstorming's terminal is "Invoke writing-plans skill"; writing-plans' terminal is "Offer execution choice." The chain is visible in the documentation.
- Alternative paths: `executing-plans` for inline execution, `subagent-driven-development` for fresh-subagent-per-task. Both are hard-gated on a complete plan.

**5. Meta-skill and test harness** (~250 words) — concrete realization of AEE-505

- `writing-skills` treats skill creation as TDD applied to documentation. The mapping: test case = pressure scenario with subagent; production code = skill document; RED = agent violates rule without skill; GREEN = agent complies with skill present; refactor = close rationalization loopholes while maintaining compliance.
- Process: (1) run baseline pressure scenario BEFORE writing the skill to document what the agent does wrong; (2) write the skill addressing those specific violations; (3) re-run the scenario to verify compliance; (4) find new rationalizations, plug them, re-verify.
- `tests/` directory. Subdirectories: brainstorm-server, claude-code, explicit-skill-requests, opencode, skill-triggering, subagent-driven-dev. Scenario evals, not unit tests — they test agent behavior under skill invocation across platforms.
- Skills as versioned code. The project's CLAUDE.md: "Skills are not prose — they are code that shapes agent behavior." PRs that modify skill content without eval evidence are rejected.
- Single specialized subagent: `agents/code-reviewer.md`. Everything else is a generic subagent loading the relevant skill for the task. The specialized-subagent-per-task pattern is rejected; skills compose through general-purpose subagents loading relevant skills instead.

**6. Opinionated philosophy** (~200 words) — concrete realization of AEE-506

- TDD-first without exceptions. `test-driven-development` is a rigid skill; adaptation is not permitted.
- Skills override default system prompt behavior; user instructions (CLAUDE.md, GEMINI.md, AGENTS.md, direct requests) override skills. The precedence is explicit in `using-superpowers`.
- "Evidence over claims." The `verification-before-completion` skill exists to require running verification commands and confirming output before making success claims. It is paired with systematic-debugging, not optional.
- Contributor discipline. The project's CLAUDE.md (the contributor guide) opens with "This repo has a 94% PR rejection rate" and explicitly addresses AI agents attempting drive-by PRs. Domain-specific skills are rejected from core; zero-dependency is a rule; "compliance" reformatting without eval evidence is rejected.
- The library stays deliberately small (14 skills) and general-purpose. Domain-specific work belongs in separate plugins.
- Contrast point for readers: a skill library that grows unchecked without eval discipline or compositional constraints accumulates maintenance debt much faster than this one.

### Best Practices (5–6 items drawn from what the case study teaches)

1. **Write skill descriptions that describe WHEN to use, not WHAT the skill does.** The description field is how the harness matches user intent to the skill. "Use when ..." phrasing focuses the description on triggering conditions instead of capability-catalog prose.
2. **Pressure-test skills against rationalization, not just happy-path usage.** A skill that works when the agent wants to invoke it and fails when the agent is under time pressure is a skill that will not fire when it matters most. Write the failure cases first, then the skill.
3. **Compose by reference, not by copy.** Skills that embed chunks of other skills rot in parallel. Skills that reference other skills through explicit `REQUIRED SUB-SKILL` pointers let each skill evolve independently.
4. **Treat the skill library as code requiring evals.** Any change to skill content risks behavior drift. A library without a scenario test suite cannot detect drift; it will eventually collect skills that work for the author but fail for everyone else.
5. **Keep the library small and general.** Domain-specific skills belong in separate plugins or in project-level CLAUDE.md. A core library that grows unchecked becomes a tax on every new skill authored.
6. **Hard-gate irreversible transitions.** The moment a skill's next step is a file mutation, a branch merge, or a user-visible message, insert an explicit approval gate. Hard gates are not friction; they are the mechanism that makes autonomous operation trustworthy.

### Visual

- Mermaid diagram of the seven-stage pipeline with hard-gates shown as edges. Shows brainstorming → using-git-worktrees → writing-plans → (subagent-driven-dev | executing-plans) → test-driven-development → requesting-code-review → finishing-a-development-branch.
- Small reference table of the behavior-shaping patterns: pattern name, what it solves, example location.

### Related AEEs

- [AEE-500](500) — Skills vs. Tools — the foundational distinction this case study assumes
- [AEE-501](501) — What Is an Agent Skill — skill anatomy; section 2 of this article makes it concrete
- [AEE-502](502) — The Agent Skill Ecosystem — framework overview illustrates the ecosystem
- [AEE-503](503) — Skill Design — behavior-shaping patterns in section 3 extend this
- [AEE-504](504) — Skill Composition — workflow composition in section 4 extends this
- [AEE-505](505) — Skill Management — meta-skill and test harness in section 5 extend this
- [AEE-506](506) — Skill Governance — contributor discipline in section 6 extends this
- [AEE-807](../Agentic%20Development%20Workflows/807) — Spec-Driven Development Frameworks in Practice — Superpowers' framework-level survey entry; this article is the narrower-and-deeper sibling
- [AEE-808](../Agentic%20Development%20Workflows/808) — AGENTS.md and Authoring Best Practices — Superpowers' `AGENTS.md -> CLAUDE.md` symlink is a live example of AEE-808's symlink interop pattern

### References

All references verified at write time via WebFetch.

1. Superpowers GitHub repository (canonical home).
2. Superpowers README.
3. Superpowers CLAUDE.md / contributor guide.
4. `using-superpowers` SKILL.md.
5. `writing-skills` SKILL.md.
6. Jesse Vincent's blog post introducing Superpowers (fsck.com).
7. Anthropic's official skill-authoring guidance (for contrast; Superpowers' CLAUDE.md explicitly notes the framework differs).

Any reference not reachable at write time drops from the list and, if load-bearing, drops from the article body too.

### Changelog

Single entry: `2026-04-19 -- Initial draft`

## Bilingual Plan

EN and zh-TW written in the same session. Tables and mermaid diagrams identical across languages. Terminology policy:

- "Superpowers" stays in English.
- "Skill", "skill system" → `技能` / `技能系統`.
- "Behavior-shaping patterns" → `行為塑形模式`.
- "Hard-gate" → `硬性閘門`.
- "Rationalization" → `合理化`.
- "Pressure test" → `壓力測試`.
- "Red Flags table" → `紅旗表`.
- "Human partner" (Superpowers-specific phrasing) → `人類夥伴`; preserve the deliberate non-interchangeability with `user` / `使用者`.
- MUST / SHOULD / MAY preserved as English keywords inline.
- Frontmatter tags (`EXTREMELY-IMPORTANT`, `HARD-GATE`, `SUBAGENT-STOP`), file names, command syntax, skill names all stay in English.
- Section headings match the corpus pattern established in AEE-807 and AEE-808 zh-TW: `背景脈絡`, `設計思考`, `深度解析`, `最佳實踐`, `視覺`, `相關 AEE`, `參考資料`, `更新記錄`.

## Frontmatter

```yaml
# EN
id: 507
title: "Superpowers: A Skills-System Case Study"
state: draft
```

```yaml
# zh-TW
id: 507
title: "Superpowers：技能系統案例研究"
state: draft
```

## Category file updates

- `docs/en/Agent Skills/500.md` — add AEE-507 to the Related AEEs list or the article enumeration, following the pattern used for 501–506.
- `docs/zh-tw/Agent Skills/500.md` — same update.

## Mandatory polish step

After drafting both language versions but BEFORE committing, run the `polish-documents` skill against each of the two new article files:

- `docs/en/Agent Skills/507.md`
- `docs/zh-tw/Agent Skills/507.md`

`polish-documents` tightens sentences, removes prohibited style patterns (contrastive negation, em-dash chains, precision puffery, unanchored claims, capability stacks), and applies Google Developer Style principles while preserving the author's voice. Per the project's CLAUDE.md writing-style rules, these patterns are prohibited. The polish step is a mandatory gate, not optional. The implementation plan must include it as a separate step for each language version.

## Implementation notes

- Reference AEE-807 and AEE-808 for framework-survey prose cadence.
- Reference 500–506 (both languages) for established terminology.
- No `**RFC 2119:**` bold labels.
- No emoji.
- Author has direct operational experience with Superpowers — the article is written from first-hand use. Cite this as a disclosed premise in Context, not hidden.
- Run `pnpm docs:build` during implementation to catch frontmatter / link errors.

## Out of scope / follow-ups

- Comparison with other skill systems (LangChain agents, Cursor rules as skills, etc.) — belongs in a separate article if warranted.
- Adoption benchmarks, user studies, or performance comparisons — the article is a design case study, not an empirical evaluation.
- Deep dive on any single Superpowers skill beyond what the structural discussion requires.

## Changelog

- 2026-04-19 -- initial spec
