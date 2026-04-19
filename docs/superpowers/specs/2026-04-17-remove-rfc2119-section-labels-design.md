# Remove RFC 2119 Section Labels From AEE Articles

**Date:** 2026-04-17
**Status:** Approved design, pending implementation plan
**Scope:** Bilingual (EN + zh-TW) AEE article corpus under `docs/en/` and `docs/zh-tw/`

## Problem

Every AEE article in the corpus carries a repeated `**RFC 2119:**` (or variant) bold label inside its `Design Think` section. The label is not sanctioned by the article template in `CLAUDE.md` — the template lists `Context, Design Think, Deep Dive (optional), Best Practices (optional), Visual (optional), Examples (optional), Related AEEs, References, Changelog`, with RFC 2119 mentioned only as a keyword convention (MUST, SHOULD, MAY). The label was adopted during drafting and propagated uniformly. It now reads as redundant visual clutter in every article.

## Goal

Remove the RFC 2119 section label from every article body, keeping the underlying MUST/SHOULD/MAY bullet content intact. Preserve the RFC 2119 keyword convention itself — only the repeated section label is being retired.

## Non-goals

- Not retiring the RFC 2119 keyword convention. Bullets containing MUST / SHOULD / MAY stay.
- Not editing meta-documentation (`faq.md`, `AEE-0`, `CLAUDE.md`). Those describe the convention rather than apply it.
- Not altering changelog entries that historically reference RFC 2119.
- Not altering template order or any other section in `Design Think`.

## Patterns to remove

### English (`docs/en/`)

| Pattern | Occurrences |
|---------|-------------|
| `**RFC 2119:**` (standalone line) | ~46 |
| `**RFC 2119 requirements:**` | 5 |
| `**RFC 2119 requirements for production systems:**` | 1 (AEE-201) |
| `**RFC 2119:** …` (inline with content on same line) | 1 (AEE-800) |

### Traditional Chinese (`docs/zh-tw/`)

| Pattern | Occurrences |
|---------|-------------|
| `**RFC 2119：**` (full-width colon, standalone) | ~35 |
| `**RFC 2119:**` (half-width colon, standalone) | ~10 |
| `**RFC 2119 要求：**` | 5 |
| `**生產系統的 RFC 2119 要求：**` | 1 (AEE-201) |
| `**RFC 2119:** …` (inline with content) | 1 (AEE-800) |

### Lead-in sentences (rewrite, do not delete)

- `docs/en/Multi-Agent and Orchestration/600.md` line 19: `The RFC 2119 rules for this category:` → `The rules for this category:`
- `docs/zh-tw/Multi-Agent and Orchestration/600.md` line 19: `本類別的 RFC 2119 規則：` → `本類別的規則：`

## Transformations

### 1. Standalone label lines (default case, ~95%)

Remove the label line and the blank line that follows so the bullet list flows directly after the preceding paragraph.

Before:
```
…prior paragraph ending sentence.

**RFC 2119:**

- Systems MUST …
- Agents SHOULD …
```

After:
```
…prior paragraph ending sentence.

- Systems MUST …
- Agents SHOULD …
```

Applies to EN `**RFC 2119:**`, `**RFC 2119 requirements:**`, `**RFC 2119 requirements for production systems:**`, and their zh-TW analogues (`**RFC 2119：**`, `**RFC 2119:**`, `**RFC 2119 要求：**`, `**生產系統的 RFC 2119 要求：**`).

### 2. AEE-800 inline label + content on same line

EN (`docs/en/Agentic Development Workflows/800.md:37`):
- Before: `**RFC 2119:** Agentic workflows MUST include at least one human checkpoint for tasks with irreversible consequences. Steering rules SHOULD be versioned alongside code.`
- After: `Agentic workflows MUST include at least one human checkpoint for tasks with irreversible consequences. Steering rules SHOULD be versioned alongside code.`

zh-TW (`docs/zh-tw/Agentic Development Workflows/800.md:37`):
- Before: `**RFC 2119:** 代理工作流程在執行具有不可逆後果的任務時，MUST（必須）至少包含一個人工檢查點。引導規則 SHOULD（應當）與程式碼一起進行版本控制。`
- After: `代理工作流程在執行具有不可逆後果的任務時，MUST（必須）至少包含一個人工檢查點。引導規則 SHOULD（應當）與程式碼一起進行版本控制。`

### 3. AEE-600 lead-in sentence rewrite

EN (`docs/en/Multi-Agent and Orchestration/600.md:19`):
- Before: `The RFC 2119 rules for this category:`
- After: `The rules for this category:`

zh-TW (`docs/zh-tw/Multi-Agent and Orchestration/600.md:19`):
- Before: `本類別的 RFC 2119 規則：`
- After: `本類別的規則：`

### 4. Explicitly out of scope (leave untouched)

- `docs/en/Foundations and Mental Models/100.md:38` — prose sentence "This RFC 2119 boundary exists…" (not a label, load-bearing prose)
- `docs/en/Foundations and Mental Models/100.md:94` — changelog entry referencing RFC 2119 (historical record)
- `docs/zh-tw/Foundations and Mental Models/100.md:38` and `:94` — same as above
- `docs/en/AEE Overall/0.md:45` and zh-TW counterpart — meta description of Design Think section
- `docs/en/faq.md:32` — meta FAQ explaining the keyword convention
- `CLAUDE.md:28` — meta convention statement

## Implementation approach

Per-file edit using a tight set of string replacements. Because the labels sit on their own line followed by a blank line and a bullet list, the transformation is mechanically safe. Two edits are not mechanical (AEE-600 lead-in rewrite, AEE-800 inline label) and must be handled individually in both EN and zh-TW.

Ordering: EN first, then zh-TW. Within each language, category by category. Verify after each category.

## Verification

1. After all edits, `grep "\*\*RFC 2119"` across `docs/en/` and `docs/zh-tw/` (excluding `.vitepress/dist`) must return zero hits.
2. `grep "RFC 2119"` (without the bold markers) must return only the prose / changelog / meta references listed in section 4 above.
3. Run `pnpm docs:build` to confirm markdown still renders and no section structure was accidentally broken.
4. Spot-check 3 random EN articles and their zh-TW counterparts to confirm the bullet list now flows directly from the preceding paragraph and reads naturally.

## Out of scope / follow-ups

- Whether to amend `CLAUDE.md` line 28 to remove the RFC 2119 convention reference entirely — deferred; current fix only targets visible section labels.
- Whether `faq.md:32` heading should be rephrased — deferred for the same reason.

## Changelog

- 2026-04-17 -- initial spec
