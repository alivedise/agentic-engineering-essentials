# AEE-808: AGENTS.md and Authoring Best Practices — Article Design

**Date:** 2026-04-17
**Status:** Approved design, pending implementation plan
**Article ID:** AEE-808
**Category:** Agentic Development Workflows (800s)
**Files:**
- `docs/en/Agentic Development Workflows/808.md`
- `docs/zh-tw/Agentic Development Workflows/808.md`

## Purpose

Introduce the AGENTS.md convention to the AEE corpus and present authoring best practices that explicitly address the live short-vs-comprehensive debate among practitioners. Serve as the narrower sibling of AEE-803 ("Steering Rules and Agent Instructions"), which covers the general steering-rules concept and cross-tool comparison.

## Non-goals

- Not a general treatment of steering rules — AEE-803 owns that ground.
- Not a tool-by-tool rule-system comparison matrix — AEE-803 already has one.
- Not a deep dive into Claude Code's scope hierarchy, Cursor's `.mdc` rule types, or the DESIGN.md pattern — pointed to AEE-803 for those.
- Not an endorsement of one authoring philosophy — the short-vs-comprehensive debate is presented neutrally with decision criteria.

## Scope and Boundary vs. AEE-803

| Concern | AEE-803 | AEE-808 (this article) |
|---|---|---|
| General steering-rules concept | Owns | Points to 803 |
| Tool ecosystem matrix (Kiro/Cursor/Cline/Claude Code) | Owns | Points to 803 |
| Claude Code scope hierarchy details | Owns | Brief callout for interop only |
| Rule-vs-guidance authoring test | Owns | Points to 803 |
| DESIGN.md pattern | Owns | Not mentioned |
| AGENTS.md as a convergence convention | Not covered | Owns |
| Cross-tool AGENTS.md adoption landscape | Not covered | Owns |
| Short-vs-comprehensive authoring debate | Not covered | Owns |
| AGENTS.md section anatomy and "earns its place" test | Not covered | Owns |
| CLAUDE.md ↔ AGENTS.md interop patterns (symlink vs `@import`) | Not covered | Owns |

## Article Structure

Standard AEE template (Context, Design Think, Deep Dive, Best Practices, Visual, Related AEEs, References, Changelog). No `**RFC 2119:**` section label — follows the post-cleanup convention: MUST/SHOULD/MAY bullets flow directly from the preceding paragraph.

### Context (~200 words)

Frames the fragmentation problem: every agent tool introduced its own instruction file — CLAUDE.md (Anthropic), .cursorrules / .cursor/rules (Cursor), .windsurfrules (Windsurf), GEMINI.md (Google), COPILOT.md (GitHub), and others. Multi-tool teams ended up duplicating the same conventions across five files that drifted out of sync. AGENTS.md is the proposed convention to consolidate: one file at the repo root that any agent tool can read.

The Context paragraph also clarifies what AGENTS.md is NOT — it is not a formal RFC, not a mandated schema, not a standard with an enforcement body. It is a convergence convention: tools agree on the filename and location, nothing more. That lightweight agreement is its power.

### Design Think (~300 words)

Positions AGENTS.md along two axes:

1. A *discovery convention* — a contract between repo and any agent tool about where to find operating instructions.
2. An *authoring contract* — what the repo promises about itself, in a form agents can act on without human interpretation.

Introduces the short-vs-comprehensive tension as a central design decision, not a footnote. Notes that the same file must serve both a human reviewer (who needs brevity to stay current) and an agent in a context-constrained session (which benefits from explicit rules when conventions are not otherwise discoverable).

Ends with MUST/SHOULD/MAY bullets (flowing directly from the paragraph, no label) covering:
- AGENTS.md MUST live at the repository root (subdirectory AGENTS.md files MAY supplement but not replace the root file).
- Machine-verifiable instructions (exact build/test commands, required env vars) MUST be separated from style preferences in the file structure.
- Projects with existing CLAUDE.md / GEMINI.md / .cursorrules SHOULD migrate to AGENTS.md with an interop shim (symlink or `@import`) rather than maintaining parallel files.
- AGENTS.md SHOULD be under 100 lines at the repo root; longer content SHOULD live in nested AGENTS.md files loaded on demand by the harness.
- Sections that no longer reflect the codebase MUST be removed, not left to rot. A stale rule is not neutral — it actively misleads agents.

### Deep Dive — four subsections

**1. The AGENTS.md convention and adoption**

What the file is (plain markdown, no mandated schema), where it lives (repo root, optionally nested subdirectories), format freedom (free-form prose and lists; some tools support frontmatter, most do not).

Adopter landscape — enumerate, with verified docs links in References:
- OpenAI Codex — formalized AGENTS.md as first-class input.
- Jules — Google's agentic coding tool, reads AGENTS.md.
- Cursor — supports AGENTS.md alongside `.cursor/rules/`.
- Factory — reads AGENTS.md as session context.
- RooCode — reads AGENTS.md.
- Zed — agent mode reads AGENTS.md.
- Aider — reads AGENTS.md.

(Final list pruned to tools with verifiable public docs at write time. Any tool whose AGENTS.md support cannot be confirmed drops out of the article.)

Anthropic Claude Code does not read AGENTS.md natively at time of writing; integration uses the interop patterns in subsection 2.

**2. Interop with CLAUDE.md and tool-specific files**

Two interop patterns for teams with mixed toolchains:

- **Symlink pattern**: `CLAUDE.md -> AGENTS.md` (and similarly for other tool-specific files). Pros: zero duplication, always in sync. Cons: requires filesystem symlink support (Windows repos may need config); some tools may not follow symlinks.
- **Import pattern**: Claude Code supports `@path/to/file` imports inside CLAUDE.md. A minimal `CLAUDE.md` containing `@AGENTS.md` delegates to the canonical file. Pros: portable across filesystems; can layer Claude Code-specific additions on top of the shared AGENTS.md. Cons: two files in the repo root; depends on each tool's import semantics.

The article recommends the import pattern when the repo has Claude Code-specific additions (e.g., permission config references), and the symlink pattern when the content is 100% shared.

Where Claude Code's scope hierarchy still matters: user-level `~/.claude/CLAUDE.md` (personal preferences) and local `CLAUDE.local.md` (gitignored) are orthogonal to AGENTS.md — they stay as-is. AGENTS.md replaces only the project-level CLAUDE.md. Full scope hierarchy details are in AEE-803, not duplicated here.

**3. The short-vs-comprehensive spectrum** (centerpiece)

Frame as a spectrum with two poles.

*Short pole — "minimal AGENTS.md":*
- Typical size: 20–80 lines.
- What it contains: build/test commands, non-obvious local setup, 3–5 hard "never" constraints.
- What it deliberately omits: style preferences (let the agent read existing code), widely-known conventions, anything verifiable by tooling (linters, formatters, type checkers).
- Optimizes for: context cost, low staleness risk, fast human review.
- Fails when: agent repeatedly violates conventions that are not visible from the code alone, or the codebase is too large for "read existing code" to be reliable within a single context window.

*Comprehensive pole — "exhaustive AGENTS.md":*
- Typical size: 200–1000+ lines, often split across nested AGENTS.md files.
- What it contains: full conventions, architectural decisions, tribal knowledge, review etiquette, security constraints, domain terminology.
- Optimizes for: consistency across many agents and contributors, capturing institutional memory, reducing recurring mistakes.
- Fails when: files rot faster than they are maintained, context budget is squeezed, agents skim and miss key items as signal-to-noise drops.

*Decision criteria table:*

| Factor | Favors short | Favors comprehensive |
|---|---|---|
| Team size | Small (1–3) | Large (10+) |
| Codebase age | New, conventions still fluid | Mature, conventions hardened |
| Recurring agent mistake frequency | Low | High |
| Human review bandwidth on agent output | High (tight feedback loop) | Low (need pre-emptive rules) |
| Tooling coverage (linters/formatters/types) | High | Low |
| Context budget pressure | High (long-running agents, multi-file tasks) | Low |

*Hybrid pattern (the article's usual-case recommendation):*
A short root `AGENTS.md` (under 100 lines) carrying project-wide essentials, plus subdirectory `AGENTS.md` files that load lazily when the agent works in that directory. This gives the short pole's virtues at the top level and the comprehensive pole's virtues where depth actually matters. Nested AGENTS.md also aligns with how Claude Code treats CLAUDE.md files in subdirectories (lazy loading — see AEE-803 for mechanics).

*Explicit neutrality note:* the article does not declare one pole "correct." Teams pick based on the criteria above. The Best Practices section contains rules that apply to *both* styles.

**4. Section anatomy**

The sections that typically earn their place in an AGENTS.md file:

- **Build / run commands** — exact commands, not descriptions. "Run `pnpm dev` to start the dev server" earns its place; "the project uses a build system" does not.
- **Test commands** — exact test invocation, any coverage thresholds, any test-writing conventions specific to this repo.
- **Project structure** — only non-obvious parts. Agents can walk the tree; the section should explain non-intuitive layouts, not re-document `src/` vs `tests/`.
- **Coding conventions** — only the ones not enforced by tooling. If a linter catches it, omit it.
- **Security and permission boundaries** — never-rules (e.g., "never commit to main directly," "never modify files under `infra/prod/`").
- **PR / commit etiquette** — commit message format, PR description requirements, review expectations.
- **Known pitfalls** — the traps that cost past contributors (human or agent) real time.

The "earns its place" test: does omitting this section cause a recurring agent (or human) mistake? If not, it does not belong in the file. This test applies regardless of short-vs-comprehensive pole; a comprehensive AGENTS.md is still bounded by this test, just with a lower threshold.

### Best Practices — 6 numbered items

1. **Keep machine-verifiable instructions separate from style preferences.** Commands, env vars, and hard boundaries go in one section; style and convention guidance goes in another. This lets a skimming agent grab what it needs without parsing prose.
2. **Write commands as copy-pasteable shell lines, not prose.** `pnpm test` beats "run the test suite." The agent does not need to interpret.
3. **Prefer nested AGENTS.md over one mega-file.** A root AGENTS.md under 100 lines plus subdirectory files scales better than a 500-line root file for both agents (context budget) and humans (review fatigue).
4. **Make it dual-use for human onboarding.** An AGENTS.md that a new team member can read in ten minutes and act on is usually also one an agent can apply correctly. If a human needs to ask about a convention that should be in the file, the file is incomplete.
5. **Audit on every major architectural change.** Rule files that reference removed paths, deprecated tools, or old conventions actively mislead agents. When architecture changes, the file change is part of the same work, not a follow-up task.
6. **Migrate from tool-specific files using an interop shim, not duplication.** Symlink or `@import` — do not maintain parallel CLAUDE.md / GEMINI.md / .cursorrules files. Parallel files drift; a single AGENTS.md with interop shims does not.

### Visual

One mermaid diagram showing the discovery flow:

```
agent session starts
   ↓
walk repo tree from cwd
   ↓
find AGENTS.md at root (+ nested AGENTS.md in current subdir if any)
   ↓
concatenate / prepend to agent context
   ↓
agent reads task instruction
```

One repeated decision table (the short-vs-comprehensive matrix from Deep Dive subsection 3) — table remains identical; serves as the at-a-glance reference.

### Related AEEs

- AEE-803 Steering Rules and Agent Instructions — parent/general treatment; this article is its narrower sibling.
- AEE-204 System Prompt Engineering — distinguishes AGENTS.md (persistent repo state) from system prompts (per-session configuration).
- AEE-703 Context Assembly — how the harness loads and prepends AGENTS.md into the agent's context.
- AEE-805 Workflow Codification — AGENTS.md as a primary codification artifact.
- AEE-800 Agentic Development Workflows — category overview.

### References

Every reference MUST be a real, verified URL at write time. If a source cannot be verified, the claim it supports drops from the article or is flagged as inference.

Planned primary sources (all to be fetched and verified during the implementation plan's research phase):

1. `https://agents.md/` — canonical spec site for the AGENTS.md convention. Verify it is live.
2. OpenAI Codex AGENTS.md documentation — Codex was the first major tool to formalize AGENTS.md.
3. Anthropic Claude Code memory / `@import` documentation — supports the interop-pattern claims.
4. Cursor documentation — verify the current stance on AGENTS.md vs. `.cursor/rules/*.mdc`.
5. Jules / Factory / RooCode / Zed / Aider public docs — adoption evidence; prune to the subset with clearest public docs at write time.
6. One representative "comprehensive" AGENTS.md — AWS AI-DLC rule repository or a similarly well-documented project.
7. One representative "short" AGENTS.md — a well-known open-source project publishing a minimalist AGENTS.md (pick at research time).

Not accepted as primary sources: tweets, LinkedIn posts, Substack takes. These may appear as illustrations only if still live and clearly attributable — never as the load-bearing citation for a factual claim.

### Changelog

Single entry: `2026-04-17 -- Initial draft`

## Bilingual Plan

EN and zh-TW written in the same session to avoid drift. Tables, mermaid diagrams, and section order are identical across languages. Prose is translated rather than machine-translated without review. Terminology policy:

- "AGENTS.md" stays in English in both locales.
- "CLAUDE.md" and other tool-file names stay in English.
- "short-vs-comprehensive" rendered as `精簡 vs 完備` in zh-TW (consistent with style elsewhere in the corpus; final wording verified against adjacent articles during implementation).
- Command samples stay in English.

## Frontmatter

```yaml
# EN
id: 808
title: AGENTS.md and Authoring Best Practices
state: draft
```

```yaml
# zh-TW
id: 808
title: AGENTS.md 與撰寫最佳實踐
state: draft
```

## Category file updates

- `docs/en/Agentic Development Workflows/800.md` — add AEE-808 entry to the article list (pattern already used for 801–807). Verify location and formatting during implementation.
- `docs/zh-tw/Agentic Development Workflows/800.md` — same update.
- `docs/en/list.md` and `docs/zh-tw/list.md` — auto-generated; will be regenerated by the existing generation step, not hand-edited.

## Implementation notes

- Use the Read tool against AEE-803 (already on-disk) and AEE-807 (most recent sibling) when writing to match tone, section depth, and prose cadence.
- Run `pnpm docs:build` at least once during implementation to catch frontmatter / link errors before commit.
- Use the post-RFC-2119-cleanup convention: no `**RFC 2119:**` bold labels — MUST/SHOULD/MAY bullets flow directly from the preceding paragraph.
- Do not introduce emoji.

## Out of scope / follow-ups

- A fuller AGENTS.md vs. `.mcp.json` / MCP server registration comparison — separate concern, not AGENTS.md content itself.
- AGENTS.md for non-coding agents (e.g., research agents, browser agents) — deferred; current audience is development-flow agents.
- Template files committed under a `templates/` directory — deferred unless explicitly requested.

## Changelog

- 2026-04-17 -- initial spec
