---
title: Expand Multi-Agent and Orchestration with named agent-communication protocols
date: 2026-04-28
status: Approved, in execution
strategic_spec: 2026-04-28-agent-comms-protocols-expand-design.md
category: Multi-Agent and Orchestration
branch: expand/multi-agent-and-orchestration-2026-04-28
---

# Expand Multi-Agent and Orchestration — operational topic list

This document is the operational manifest for the `expanding-category-articles`
skill run on 2026-04-28. The strategic rationale (why these three protocols,
what is out of scope, follow-up plans) lives in
[`2026-04-28-agent-comms-protocols-expand-design.md`](2026-04-28-agent-comms-protocols-expand-design.md)
on `main`.

## Adapter notes (BEE template to AEE conventions)

The skill template was authored for the BEE corpus and conflicts with AEE's
established conventions in five places. AEE conventions win per the user's
`CLAUDE.md` priority over skill defaults. The new articles deviate from the
skill template as follows:

- **Filename**: numeric `<id>.md` (per AEE), not `<slug>.md`.
- **Frontmatter**: `id`, `title`, `state` per AEE, plus `slug: <numeric-id>` to
  satisfy the validator's required-field check. Numeric strings pass the
  validator's kebab-case regex.
- **Section structure**: AEE-canonical sections (Context, Design Think,
  Deep Dive, Best Practices, Visual, Examples, Related AEEs, References,
  Changelog) plus one genuine topic-specific section per article.
- **Section names**: "Design Think" not "Design Thinking", "Examples" not
  "Example", "Related AEEs" not "Related Topics".
- **No `:::info` summary block**: existing AEE articles do not use it.
- **H1**: `# [AEE-<id>] Title`.

The skill's validator gates still pass under this adaptation: the numeric slug
matches the basename and the kebab regex; the AEE-specific section names read
as non-canonical (i.e. "topic-specific") to the validator's regex, satisfying
the `>=1 topic-specific section` rule. Each article also includes a true
topic-specific section beyond the AEE canonical set.

## Topics, IDs, and slugs

| ID  | Slug | Title (working)                                   | Topic-specific section (working)            |
|-----|------|---------------------------------------------------|---------------------------------------------|
| 608 | 608  | A2A: The Agent2Agent Protocol                     | Wire Format and Message Envelope            |
| 609 | 609  | ACP: The Agent Communication Protocol             | Session and Streaming Semantics             |
| 610 | 610  | AG-UI: The Agent-User Interaction Protocol        | Frontend Event Surface                      |

Reading order: 608 (canonical inter-agent) -> 609 (inter-agent contrast) ->
610 (cross-axis: agent to frontend).

## Files this run will produce

Per topic:

- `docs/en/Multi-Agent and Orchestration/<id>.md`
- `docs/zh-tw/Multi-Agent and Orchestration/<id>.md`
- `docs/superpowers/research/<id>.md`

Cross-cutting (handled at end of run):

- `docs/en/Multi-Agent and Orchestration/600.md` — append AEE-608/609/610
  entries to its Related AEEs enumeration.
- `docs/zh-tw/Multi-Agent and Orchestration/600.md` — same update.
- `docs/en/list.md` — append three numeric entries.
- `docs/zh-tw/list.md` — append three numeric entries.

## Per-article gate sequence (AEE-adapted)

For each article:

1. Research subagent (PER-ARTICLE mode) -> findings doc at
   `docs/superpowers/research/<id>.md`.
2. Writer subagent -> EN article. Writer prompt instructs AEE conventions
   verbatim, overriding the skill's template-VERBATIM rule per the priority
   resolution above. Writer is given findings doc, AEE structural rules, id,
   slug, title, topic-specific section name. Writer has no Web access.
3. Translator subagent -> zh-TW article. Translator gets only the EN article.
4. `polish-documents` skill on EN file.
5. `polish-documents` skill on zh-TW file.
6. Gates: `validate-frontmatter.sh` (EN, zh-TW), `validate-structure.sh`
   (EN, zh-TW), `check-references.sh` (EN), findings-coverage grep (>=3 URLs
   present in both findings doc and EN article).
7. Commit per article: EN + zh-TW + findings doc, message
   `docs(multi-agent): add <title> (AEE-<id>)`.

## Final-pass commits

After all three articles land:

8. Update `docs/en/Multi-Agent and Orchestration/600.md` and the zh-TW
   counterpart to enumerate AEE-608, 609, 610 in the existing Related AEEs
   list. One commit.
9. Update `docs/en/list.md` and `docs/zh-tw/list.md` with three new entries
   each, slot ordered by numeric id. One commit.
10. Phase 5 batch validate: `id:` uniqueness across the category directory.
11. Phase 6 handoff: print worktree path, branch, ID list, next step.

## Changelog

- 2026-04-28 -- initial operational manifest
