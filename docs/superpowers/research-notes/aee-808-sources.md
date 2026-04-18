# AEE-808 Reference Verification Notes

## Verified sources

| # | URL | What it supports | Status |
|---|---|---|---|
| 1 | https://agents.md/ | Canonical convention site; lists 20+ adopters; confirms root placement, nested files, nearest-wins precedence; describes AGENTS.md as "a simple, open format for guiding coding agents" that "complements README files" | verified |
| 2 | https://developers.openai.com/codex/guides/agents-md | OpenAI Codex official AGENTS.md docs; 3-level discovery chain (global `~/.codex`, walk from git root, nested); `AGENTS.override.md` overrides; later files take precedence | verified |
| 3 | https://github.com/openai/codex/blob/main/AGENTS.md | Codex's own AGENTS.md — ~213 lines — concrete example of comprehensive-pole authoring | verified |
| 4 | https://code.claude.com/docs/en/memory | Claude Code CLAUDE.md scope hierarchy (managed/user/project/local); full `@import` semantics (relative paths, up to 5 hops); explicit "Claude Code reads CLAUDE.md, not AGENTS.md" note with recommended interop pattern: `@AGENTS.md` inside CLAUDE.md | verified |
| 5 | https://cursor.com/docs/rules | Cursor rules + explicit AGENTS.md section: "plain markdown file without metadata or complex configurations"; supports root and subdirectory files; nested instructions combine with parents, more specific wins | verified |
| 6 | https://docs.factory.ai/cli/configuration/agents-md | Factory CLI hierarchical discovery (cwd → nearest parent to repo root → subfolders → `~/.factory/AGENTS.md` personal override); proximity-wins precedence | verified |
| 7 | https://zed.dev/docs/ai/agent-panel | Zed Agent Panel and agent functionality (AGENTS.md standard is listed by agents.md as adopted in Zed; Zed's own docs center on Agent Panel and external agents via ACP) | verified (Zed has agent mode; AGENTS.md adoption sourced via agents.md authoritative list) |
| 8 | https://aider.chat/docs/usage/conventions.html | Aider's conventions file mechanism — supports any markdown via `--conventions-file`, default is `CONVENTIONS.md`; AGENTS.md can be used with the flag; listed as adopter on agents.md | verified (via conventions-file flag; not default filename) |
| 9 | https://github.com/awslabs/aidlc-workflows | AWS AI-DLC steering rules repo — `core-workflow.md` anchor, phase subdirectories under `aws-aidlc-rule-details/` (inception, construction, extensions, operations); mentions AGENTS.md as fallback filename for coding agents | verified |
| 10 | https://github.com/agentsmd/agents.md/blob/main/AGENTS.md | Representative short AGENTS.md — ~65-70 lines — from the canonical spec repo itself. Five sections: introduction, dev-server rule, dependency sync, coding conventions, commands recap | verified |
| 11 | https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/ | GitHub's analysis of 2,500+ repos' AGENTS.md files: iterative growth philosophy, six core sections (commands, testing, project structure, code style, git workflow, boundaries), specificity over vagueness, anti-patterns | verified — especially strong for Best Practices |

## Key claims by source

- **AGENTS.md is a convergence convention, not a formal spec** — supported by agents.md, GitHub blog post
- **Repo-root placement with optional nested files + nearest-wins precedence** — supported by agents.md, OpenAI Codex docs, Cursor docs, Factory docs (all confirm same pattern)
- **Claude Code reads CLAUDE.md not AGENTS.md; interop via `@AGENTS.md` import** — supported directly by Claude Code memory docs
- **Start minimal, grow only when agent repeats mistakes** — supported by GitHub blog post (iterative growth philosophy)
- **Six common section types (commands, testing, project structure, code style, git workflow, boundaries)** — supported by GitHub blog post
- **Comprehensive example: openai/codex at ~213 lines** — observed directly
- **Short example: agentsmd/agents.md at ~65-70 lines** — observed directly
- **Stewarded by Agentic AI Foundation under Linux Foundation** — sourced via Cursor docs context

## Dropped or rewritten claims

- Windsurf-specific AGENTS.md docs — not directly verified; agents.md lists Windsurf (now Cognition) as adopter. Mention via agents.md only, no separate citation.
- Jules-specific AGENTS.md docs — listed on agents.md; Jules is confirmed as adopter via the agents.md curated list. No separate citation beyond agents.md.
- RooCode-specific AGENTS.md docs — listed on agents.md; cited via agents.md only.
- GitHub Copilot AGENTS.md support — listed on agents.md; cited via agents.md + GitHub blog post.

## Final adopter list

Direct docs verified (cite tool's own URL):
- **OpenAI Codex** — `https://developers.openai.com/codex/guides/agents-md`
- **Cursor** — `https://cursor.com/docs/rules`
- **Factory** — `https://docs.factory.ai/cli/configuration/agents-md`

Listed on agents.md curated adopter roster (cite via agents.md and/or the GitHub blog):
- Google Jules
- Cognition Devin / Windsurf
- JetBrains Junie
- UiPath
- Amp
- Zed
- RooCode
- Aider (via `--conventions-file`)
- VS Code (with GitHub Copilot)
- GitHub Copilot
- Gemini CLI
- goose
- opencode
- Warp
- Kilo Code
- Phoenix
- Semgrep
- Ona
- Augment Code

Claude Code interop (not a native reader):
- **Claude Code** — reads CLAUDE.md, supports AGENTS.md via `@AGENTS.md` import inside a minimal CLAUDE.md, per Anthropic memory docs.
