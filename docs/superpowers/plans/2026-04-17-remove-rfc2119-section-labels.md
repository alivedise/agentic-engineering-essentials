# Remove RFC 2119 Section Labels Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Remove the repeated `**RFC 2119:**` bold label (and bilingual variants) from every AEE article body, while preserving the MUST/SHOULD/MAY bullet content and the RFC 2119 keyword convention itself.

**Architecture:** Three distinct transformations. (1) AEE-600 lead-in sentence rewrite in EN + zh-TW. (2) AEE-800 inline label strip in EN + zh-TW. (3) Bulk regex removal of standalone label lines across the remaining ~98 files. Each transformation ends with a grep-based verification and a focused commit.

**Tech Stack:** Markdown content under `docs/en/` and `docs/zh-tw/`. VitePress 1.3.x for rendering. pnpm for build. Python 3 standard library for the bulk transformation script.

---

## File Inventory

### Special cases (manual edit, 4 files)

| File | Line | Transform |
|------|------|-----------|
| `docs/en/Multi-Agent and Orchestration/600.md` | 19 | Lead-in rewrite |
| `docs/zh-tw/Multi-Agent and Orchestration/600.md` | 19 | Lead-in rewrite |
| `docs/en/Agentic Development Workflows/800.md` | 37 | Strip inline label prefix |
| `docs/zh-tw/Agentic Development Workflows/800.md` | 37 | Strip inline label prefix |

### Standalone label files (bulk script, 100 files)

AEE-800 in both languages is excluded here — it is handled by Task 2 because its label is inline rather than standalone.

**EN (50 files)** — `docs/en/`:
- `Foundations and Mental Models/`: 103, 104, 105, 106, 107, 108, 109, 110, 111
- `Model and Context Layer/`: 201, 202, 203, 204, 205, 206
- `Prompt Engineering and Interaction/`: 301, 302, 303, 304, 305, 306
- `Tool Use and Execution/`: 401, 402, 403, 404, 405, 406
- `Agent Skills/`: 501, 503, 504, 505, 506
- `Multi-Agent and Orchestration/`: 601, 602, 603, 604, 606
- `Harness Engineering/`: 701, 702, 703, 704, 705, 706
- `Agentic Development Workflows/`: 801, 802, 803, 804, 805, 806, 807

**zh-TW (50 files)** — `docs/zh-tw/` — same file IDs as above.

Label variants the script must match (all in bold, each on its own line, followed by a blank line):
- `**RFC 2119:**` (EN, half-width colon)
- `**RFC 2119：**` (zh-TW, full-width colon)
- `**RFC 2119:**` (some zh-TW files use half-width colon)
- `**RFC 2119 requirements:**` (EN, 5 files in Model and Context Layer)
- `**RFC 2119 requirements for production systems:**` (EN, AEE-201 only)
- `**RFC 2119 要求：**` (zh-TW, 5 files in Model and Context Layer)
- `**生產系統的 RFC 2119 要求：**` (zh-TW, AEE-201 only)

### Untouched (verified in spec section 4)

`docs/en/Foundations and Mental Models/100.md`, `docs/zh-tw/Foundations and Mental Models/100.md`, `docs/en/AEE Overall/0.md`, `docs/zh-tw/AEE Overall/0.md`, `docs/en/faq.md`, `CLAUDE.md`, any changelog entries.

---

## Task 1: Rewrite AEE-600 lead-in sentence (EN + zh-TW)

**Files:**
- Modify: `docs/en/Multi-Agent and Orchestration/600.md:19`
- Modify: `docs/zh-tw/Multi-Agent and Orchestration/600.md:19`

**Context:** AEE-600 uses the lead-in `The RFC 2119 rules for this category:` followed by a bullet list. With the RFC 2119 label retired, the lead-in reads awkwardly. Rewrite to a neutral lead-in.

- [ ] **Step 1: Read EN file context around line 19**

Run: use the Read tool on `docs/en/Multi-Agent and Orchestration/600.md`, offset 15, limit 10.
Expected: confirm line 19 reads `The RFC 2119 rules for this category:`.

- [ ] **Step 2: Apply EN edit**

Edit `docs/en/Multi-Agent and Orchestration/600.md`:
- `old_string`: `The RFC 2119 rules for this category:`
- `new_string`: `The rules for this category:`

- [ ] **Step 3: Read zh-TW file context around line 19**

Run: use the Read tool on `docs/zh-tw/Multi-Agent and Orchestration/600.md`, offset 15, limit 10.
Expected: confirm line 19 reads `本類別的 RFC 2119 規則：`.

- [ ] **Step 4: Apply zh-TW edit**

Edit `docs/zh-tw/Multi-Agent and Orchestration/600.md`:
- `old_string`: `本類別的 RFC 2119 規則：`
- `new_string`: `本類別的規則：`

- [ ] **Step 5: Verify no regressions in AEE-600 files**

Run via Grep tool: pattern `RFC 2119` with path `docs/en/Multi-Agent and Orchestration/600.md` and again with path `docs/zh-tw/Multi-Agent and Orchestration/600.md`, output_mode content.
Expected: zero matches in both files.

- [ ] **Step 6: Commit**

```bash
git add "docs/en/Multi-Agent and Orchestration/600.md" "docs/zh-tw/Multi-Agent and Orchestration/600.md"
git commit -m "$(cat <<'EOF'
docs: rewrite AEE-600 lead-in to drop RFC 2119 naming

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```

---

## Task 2: Strip inline RFC 2119 label from AEE-800 (EN + zh-TW)

**Files:**
- Modify: `docs/en/Agentic Development Workflows/800.md:37`
- Modify: `docs/zh-tw/Agentic Development Workflows/800.md:37`

**Context:** AEE-800 places the label inline with its rule text on a single line, not as a standalone header. The bulk script cannot safely handle this case because removing the line would also remove the rule content. Edit manually.

- [ ] **Step 1: Read EN file context around line 37**

Run: use the Read tool on `docs/en/Agentic Development Workflows/800.md`, offset 33, limit 8.
Expected: confirm line 37 reads `**RFC 2119:** Agentic workflows MUST include at least one human checkpoint for tasks with irreversible consequences. Steering rules SHOULD be versioned alongside code.`.

- [ ] **Step 2: Apply EN edit**

Edit `docs/en/Agentic Development Workflows/800.md`:
- `old_string`: `**RFC 2119:** Agentic workflows MUST include at least one human checkpoint for tasks with irreversible consequences. Steering rules SHOULD be versioned alongside code.`
- `new_string`: `Agentic workflows MUST include at least one human checkpoint for tasks with irreversible consequences. Steering rules SHOULD be versioned alongside code.`

- [ ] **Step 3: Read zh-TW file context around line 37**

Run: use the Read tool on `docs/zh-tw/Agentic Development Workflows/800.md`, offset 33, limit 8.
Expected: confirm line 37 reads `**RFC 2119:** 代理工作流程在執行具有不可逆後果的任務時，MUST（必須）至少包含一個人工檢查點。引導規則 SHOULD（應當）與程式碼一起進行版本控制。`.

- [ ] **Step 4: Apply zh-TW edit**

Edit `docs/zh-tw/Agentic Development Workflows/800.md`:
- `old_string`: `**RFC 2119:** 代理工作流程在執行具有不可逆後果的任務時，MUST（必須）至少包含一個人工檢查點。引導規則 SHOULD（應當）與程式碼一起進行版本控制。`
- `new_string`: `代理工作流程在執行具有不可逆後果的任務時，MUST（必須）至少包含一個人工檢查點。引導規則 SHOULD（應當）與程式碼一起進行版本控制。`

- [ ] **Step 5: Verify label removal**

Run via Grep tool: pattern `\*\*RFC 2119` with path `docs/en/Agentic Development Workflows/800.md`, output_mode content.
Expected: zero matches.

Repeat with path `docs/zh-tw/Agentic Development Workflows/800.md`.
Expected: zero matches.

Follow up: run Grep for pattern `Agentic workflows MUST include` on the EN file — expected 1 match on line 37 with the leading bold marker gone.

- [ ] **Step 6: Commit**

```bash
git add "docs/en/Agentic Development Workflows/800.md" "docs/zh-tw/Agentic Development Workflows/800.md"
git commit -m "$(cat <<'EOF'
docs: strip inline RFC 2119 label from AEE-800

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```

---

## Task 3: Bulk-remove standalone RFC 2119 labels

**Files:**
- Create (then delete): `scripts/remove_rfc2119_labels.py`
- Modify: 100 article files listed in the File Inventory (50 EN + 50 zh-TW). AEE-800 in each language is already handled by Task 2 and will not be touched here.

**Context:** All standalone labels match a single regex pattern: a bold label starting with `RFC 2119` (or `生產系統的 RFC 2119`) on its own line, followed by a blank line. Because the label and the blank line below it are the only two lines to remove, a multiline regex substitution is safe and uniform across all 100 files. A throwaway Python script handles this atomically.

The script must NOT match:
- AEE-800 inline case (already handled in Task 2) — regex requires `\n\n` after the closing `**`, which AEE-800 lacks.
- AEE-600 lead-in sentence (already handled in Task 1) — regex requires the label to start with `**`, which the lead-in lacks.
- Prose references in AEE-100 and AEE-0 meta — regex requires `**` markers, which those prose lines lack.

- [ ] **Step 1: Pre-flight — snapshot current match count**

Because Task 2 has already stripped the AEE-800 inline label in both languages, only the standalone labels remain.

Run via Grep tool: pattern `^\*\*(RFC 2119|生產系統的 RFC 2119)` with path `docs/en`, output_mode count.
Expected: 50 matches (one per EN article, AEE-800 already stripped by Task 2).

Repeat with path `docs/zh-tw`.
Expected: 50 matches.

Record both numbers. After the script runs, they must both be 0.

- [ ] **Step 2: Check scripts directory exists**

Run: `ls scripts/` at repo root.
Expected: directory listing, or "No such file or directory" (in which case next step creates it).

- [ ] **Step 3: Write the transformation script**

Create file `scripts/remove_rfc2119_labels.py` with exactly this content:

```python
#!/usr/bin/env python3
"""One-shot script: remove standalone **RFC 2119...** section labels from AEE articles.

Removes the label line plus the single blank line that follows it. Leaves inline
labels (those with content on the same line) untouched, because the regex
requires \\n\\n immediately after the closing **.

Scope: docs/en/**/*.md and docs/zh-tw/**/*.md, excluding docs/.vitepress/.
"""

from __future__ import annotations

import pathlib
import re
import sys

ROOT = pathlib.Path(__file__).resolve().parent.parent
DOC_ROOTS = [ROOT / "docs" / "en", ROOT / "docs" / "zh-tw"]

LABEL_RE = re.compile(
    r"\*\*(?:RFC 2119|生產系統的 RFC 2119)[^\n*]*\*\*\n\n",
)


def process(path: pathlib.Path) -> bool:
    text = path.read_text(encoding="utf-8")
    new_text, n = LABEL_RE.subn("", text)
    if n == 0:
        return False
    if n > 1:
        print(f"WARN: {path} had {n} matches (expected 0 or 1)", file=sys.stderr)
    path.write_text(new_text, encoding="utf-8")
    print(f"modified: {path.relative_to(ROOT)} ({n} label removed)")
    return True


def main() -> int:
    total = 0
    for root in DOC_ROOTS:
        for md in root.rglob("*.md"):
            if process(md):
                total += 1
    print(f"done: {total} files modified")
    return 0


if __name__ == "__main__":
    sys.exit(main())
```

- [ ] **Step 4: Dry-run sanity check — inspect regex against a known case**

Run:
```bash
python3 -c "
import re
p = re.compile(r'\*\*(?:RFC 2119|生產系統的 RFC 2119)[^\n*]*\*\*\n\n')
samples = [
    '**RFC 2119:**\n\n- bullet\n',
    '**RFC 2119：**\n\n- bullet\n',
    '**RFC 2119 requirements:**\n\n- bullet\n',
    '**RFC 2119 requirements for production systems:**\n\n- bullet\n',
    '**RFC 2119 要求：**\n\n- bullet\n',
    '**生產系統的 RFC 2119 要求：**\n\n- bullet\n',
    '**RFC 2119:** Agentic workflows MUST include...\n',  # inline, must NOT match
    'The RFC 2119 rules for this category:\n',             # lead-in, must NOT match
]
for s in samples:
    hit = bool(p.search(s))
    print(f'{hit!s:5}  {s.splitlines()[0]!r}')
"
```
Expected output (order matches input):
```
True   '**RFC 2119:**'
True   '**RFC 2119：**'
True   '**RFC 2119 requirements:**'
True   '**RFC 2119 requirements for production systems:**'
True   '**RFC 2119 要求：**'
True   '**生產系統的 RFC 2119 要求：**'
False  '**RFC 2119:** Agentic workflows MUST include...'
False  'The RFC 2119 rules for this category:'
```

If any line's True/False value does not match, STOP. Do not run the script.

- [ ] **Step 5: Run the script**

Run: `python3 scripts/remove_rfc2119_labels.py`
Expected: 100 lines of `modified: docs/...` followed by `done: 100 files modified`. No `WARN:` lines.

If the count is not exactly 100, STOP and investigate. Do not commit partial results.

- [ ] **Step 6: Verify zero labels remain in article bodies**

Run via Grep tool: pattern `^\*\*(RFC 2119|生產系統的 RFC 2119)` with path `docs/en`, output_mode count.
Expected: 0 matches.

Repeat with path `docs/zh-tw`.
Expected: 0 matches.

- [ ] **Step 7: Verify legitimate RFC 2119 references survive**

Run via Grep tool: pattern `RFC 2119` with path `docs/en/Foundations and Mental Models/100.md`, output_mode content.
Expected: 2 matches (line 38 prose boundary sentence, line 94 changelog entry).

Repeat with path `docs/zh-tw/Foundations and Mental Models/100.md`.
Expected: 2 matches (same two lines in zh-TW).

Repeat with path `docs/en/AEE Overall/0.md`.
Expected: 1 match on the Design Think template description.

Repeat with path `docs/en/faq.md`.
Expected: at least 1 match (the FAQ heading about RFC 2119 keywords).

- [ ] **Step 8: Spot-check one modified file**

Run: use the Read tool on `docs/en/Foundations and Mental Models/103.md`, offset 19, limit 10.
Expected: line 21 reads `The engineering consequences follow directly:`, line 22 is blank, and line 23 now begins with `- A chatbot MAY have memory...` (previously the label, now the first bullet directly).

- [ ] **Step 9: Delete the one-shot script**

Run: `rm scripts/remove_rfc2119_labels.py`
Expected: no output.

Also check whether the `scripts/` directory is now empty and was created by this task:
```bash
ls scripts/ 2>/dev/null | wc -l
```
If the output is `0` and `scripts/` was created in Step 2, remove it:
```bash
rmdir scripts/
```
If it was pre-existing with other contents, leave it in place.

- [ ] **Step 10: Commit**

```bash
git add docs/en docs/zh-tw
git commit -m "$(cat <<'EOF'
docs: remove standalone RFC 2119 section labels from all AEE articles

Strips the repeated **RFC 2119:** bold label (and zh-TW / variant forms)
from every article's Design Think section. MUST/SHOULD/MAY bullet content
is preserved. Meta-docs (faq.md, AEE-0, CLAUDE.md) are untouched.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```

---

## Task 4: Whole-corpus verification + build check

**Files:**
- No file modifications expected. This task verifies the state after Tasks 1–3.

- [ ] **Step 1: Whole-corpus label scan**

Run via Grep tool: pattern `\*\*RFC 2119` with path `docs`, output_mode files_with_matches.
Expected: 0 files (empty result).

If any file matches, STOP and inspect. Every hit must be justified against the spec's section 4 "Out of scope" list.

- [ ] **Step 2: Whole-corpus broader RFC 2119 scan**

Run via Grep tool: pattern `RFC 2119` with path `docs`, output_mode content, head_limit 50.
Expected matches only in:
- `docs/en/Foundations and Mental Models/100.md` (prose + changelog)
- `docs/zh-tw/Foundations and Mental Models/100.md` (prose + changelog)
- `docs/en/AEE Overall/0.md` (template description)
- `docs/zh-tw/AEE Overall/0.md` (template description)
- `docs/en/faq.md` (FAQ heading + body)
- `docs/.vitepress/dist/**` (build artifacts — ignore)

No hit should appear in any other article body.

- [ ] **Step 3: VitePress build**

Run: `pnpm docs:build`
Expected: build succeeds with exit code 0. No errors about broken structure, unterminated bold, or missing sections.

If the build fails, read the error output, fix the offending file with the Edit tool, and rerun until it succeeds. Common failure mode: a stray blank line or malformed bullet after the removed label — the fix is usually to tidy whitespace in one file.

- [ ] **Step 4: Manual spot-check of three random articles**

For each of these three articles, open the file and visually confirm the Design Think section reads correctly (no orphan blank lines, no bullets stranded after a deleted label, no missing paragraph breaks):

1. Use the Read tool on `docs/en/Harness Engineering/703.md`, offset 15, limit 20.
2. Use the Read tool on `docs/zh-tw/Prompt Engineering and Interaction/303.md`, offset 40, limit 15.
3. Use the Read tool on `docs/en/Model and Context Layer/201.md`, offset 15, limit 20.

Expected: each Design Think section flows from paragraph to bullet list without the former label intervening. Blank-line spacing is consistent (one blank line between paragraph and bullet list).

- [ ] **Step 5: No commit unless the build produced fixes**

If Step 3 required any Edit-tool fixes, stage and commit them:
```bash
git status
git add <specific files fixed>
git commit -m "$(cat <<'EOF'
docs: tidy whitespace after RFC 2119 label removal

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```
If no fixes were needed, skip this step. Do not create an empty commit.

---

## Rollback plan

If any task produces unacceptable output, revert with:
```bash
git reset --hard HEAD~<N>
```
where `<N>` is the number of commits produced by this plan so far. The bulk transformation in Task 3 is a single commit, so reverting it reverts all 102 file changes in one operation.
