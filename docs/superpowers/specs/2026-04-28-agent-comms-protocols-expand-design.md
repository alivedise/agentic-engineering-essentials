---
title: Agent Communication Protocols Expansion (AEE-608/609/610)
date: 2026-04-28
status: Approved design, ready for expanding-category-articles
category: Multi-Agent and Orchestration (600s)
ids: [608, 609, 610]
mechanism: expanding-category-articles skill, --topics flag
files:
  - docs/en/Multi-Agent and Orchestration/608.md
  - docs/zh-tw/Multi-Agent and Orchestration/608.md
  - docs/en/Multi-Agent and Orchestration/609.md
  - docs/zh-tw/Multi-Agent and Orchestration/609.md
  - docs/en/Multi-Agent and Orchestration/610.md
  - docs/zh-tw/Multi-Agent and Orchestration/610.md
---

# Agent Communication Protocols Expansion

## Purpose

Add three articles to the Multi-Agent and Orchestration (600s) category covering the named 2025-era wire protocols for agent communication. AEE-602 (Agent Communication) is the conceptual article in the corpus; readers currently have no dedicated coverage of the specific protocols that implement those concepts.

## Scope

Three articles, IDs assigned up front:

| ID  | Working title                              | Protocol | Sponsor / Steward          |
|-----|--------------------------------------------|----------|----------------------------|
| 608 | A2A: The Agent2Agent Protocol              | A2A      | Google to Linux Foundation |
| 609 | ACP: The Agent Communication Protocol      | ACP      | IBM to Linux Foundation    |
| 610 | AG-UI: The Agent-User Interaction Protocol | AG-UI    | CopilotKit                 |

Reading order: 608 then 609 (inter-agent peers, contrasting design) then 610 (cross-axis: agent to frontend).

## Out of Scope

- MCP (Model Context Protocol). Tool-use protocol; belongs in the 400s "Tool Use & Execution" expansion. The 400s currently lacks any MCP coverage; tracked as a follow-up expansion candidate, handled separately.
- A2A sub-features (Agent Cards, streaming/SSE semantics, discovery) as standalone articles. These live inside AEE-608.
- Adoption surveys, market-share analysis, or empirical performance comparisons. The articles cover protocol design and trade-offs.
- Edits to AEE-602 (Agent Communication). Cross-link only; do not rewrite.

## Why these three

- **A2A** is the canonical inter-agent protocol with Google's weight, multiple reference implementations, and Linux Foundation governance. The cluster requires it.
- **ACP** is the deliberate contrast: also inter-agent, also Linux Foundation, with different choices on session, streaming, and state. Pairing with A2A teaches the design space rather than a single point.
- **AG-UI** covers the orthogonal axis (agent to frontend). Without it the cluster only addresses agent-to-agent and misses half the protocol problem readers actually face.

Three articles is the smallest set that teaches both axes.

## Mechanism

Use the `expanding-category-articles` skill with `--topics="..."` to bypass gap discovery (topics are pre-determined). The skill enforces:

- Bilingual EN + zh-TW per article.
- Research subagent produces findings doc; writer subagent works only from the findings doc (no internal-knowledge fabrication).
- `polish-documents` skill on each language file.
- Frontmatter, structure, and reference-coverage gates.
- Worktree isolation; one commit per article.

## Category placement

- Category directory: `Multi-Agent and Orchestration` (matches both `docs/en/` and `docs/zh-tw/`).
- Existing articles 600 to 607 are conceptual and pattern-level. Adding 608 to 610 as named-protocol articles extends the category along its existing axis.
- `docs/en/Multi-Agent and Orchestration/600.md` and its zh-TW counterpart enumerate category articles; both need entries appended for 608, 609, 610.

## Article structure

Standard AEE template per `expanding-category-articles/templates/article.md`. Each article includes:

- **Context** — what this protocol is, when it shipped, who runs it now.
- **Design Think** — the design choices the protocol makes and the trade-offs against alternatives. RFC 2119 keywords inline (no `**RFC 2119:**` section label, per post-cleanup convention).
- **Deep Dive** — transport, message envelope, session and streaming semantics, security and auth model, discovery, error handling.
- **Best Practices** — when to choose this protocol; integration patterns; common mistakes.
- **Visual** — Mermaid sequence diagram showing a representative interaction.
- **Examples** — minimal request/response or message pair.
- **Related AEEs** — cross-references to AEE-602 and to the other two protocol articles.
- **References** — canonical spec URL, reference implementation, key blog posts. All verified at write time.
- **Changelog** — initial draft entry only.

## Bilingual terminology

- "Agent2Agent Protocol" / "A2A" — keep both. A2A is the canonical short form.
- "Agent Communication Protocol" / "ACP" — same pattern.
- "Agent-User Interaction Protocol" / "AG-UI" — keep AG-UI as the canonical short form. Note "A2UI" as informal alias in Context.
- zh-TW: keep all protocol short forms in English. Long-form translations on first mention only:
  - A2A: `代理對代理協定 (A2A)`
  - ACP: `代理通訊協定 (ACP)`
  - AG-UI: `代理對使用者介面協定 (AG-UI)`
- Section headings match the corpus pattern established in AEE-807/AEE-808 zh-TW.

## Assumptions

- The user's "A2UI" maps to AG-UI (CopilotKit's Agent-User Interaction Protocol). If a different protocol is meant, this design needs revision.
- The 600s category and its zh-TW counterpart are the correct home for these three articles. AG-UI is borderline (agent-to-UI rather than agent-to-agent), but the category title "Multi-Agent and Orchestration" generalizes to "agent communication patterns" and AEE-602 already lives in this category as the umbrella article.
- The polish-documents skill remains available and produces consistent output across the three articles.

## Risks and Mitigations

- **Specification churn.** A2A and ACP are evolving between revisions. Mitigation: research subagent cites versioned spec URLs; articles ship with `state: draft` until reviewer verifies stability.
- **AG-UI naming ambiguity.** "A2UI" is an informal alias; the canonical name is AG-UI. Mitigation: title uses "AG-UI" with "A2UI" called out as alias in Context.
- **Cross-protocol comparison drift.** Each article risks duplicating comparison content. Mitigation: comparison content lives in AEE-608 only; AEE-609 and AEE-610 reference back rather than re-state.
- **MCP confusion.** Readers may expect MCP coverage in the same cluster. Mitigation: each article's Context includes a one-line scope statement clarifying the protocol's axis (agent-to-agent vs. agent-to-tool vs. agent-to-UI), pointing to the eventual MCP article when it lands.

## Preconditions to satisfy before invoking the skill

The skill requires a clean working tree. Current state:

- `docs/en/list.md` and `docs/zh-tw/list.md` have uncommitted edits (likely build regeneration).
- `docs/superpowers/specs/2026-04-19-aee-507-superpowers-case-study-design.md` and `docs/superpowers/plans/2026-04-19-aee-507-superpowers-case-study.md` are untracked.

Resolution: commit or stash all four before invoking the skill. The list.md edits should be committed since they are corpus index updates. The AEE-507 spec/plan should be committed since they belong on main. This design spec (this file) becomes part of the same prep commit.

## Implementation handoff

After this spec is approved:

1. Resolve preconditions (commit list.md edits, commit AEE-507 spec/plan and this file together on main).
2. Invoke `expanding-category-articles` with:
   - category slug: `Multi-Agent and Orchestration`
   - flags: `--topics="A2A: The Agent2Agent Protocol,ACP: The Agent Communication Protocol,AG-UI: The Agent-User Interaction Protocol"`
3. Confirm the topic list when the skill prompts at the Phase 2 to Phase 3 boundary.
4. Skill creates worktree at `../agentic-engineering-essentials-expand-multi-agent-and-orchestration-2026-04-28/` on branch `expand/multi-agent-and-orchestration-2026-04-28`.
5. Skill runs Phases 4 through 6: research, write EN, translate zh-TW, polish, gates, commit (per topic, sequential).
6. After all three articles are committed, manually update `docs/en/Multi-Agent and Orchestration/600.md` and the zh-TW counterpart to enumerate 608, 609, 610. This step is outside the skill's scope.
7. Reviewer runs `pnpm docs:build` in the worktree, then pushes / opens PR / discards.

## Follow-ups (not part of this expansion)

- MCP coverage in the 400s "Tool Use & Execution" category. Likely a separate expand-category run with `--topics="Model Context Protocol"` plus 2 to 3 related tool-use protocol or registry topics to satisfy the skill's 3-to-10 article range.
- Updates to AEE-602 (Agent Communication) once 608 to 610 land: add a "Named protocols" subsection that points outward to the new articles.

## Changelog

- 2026-04-28 -- initial spec
