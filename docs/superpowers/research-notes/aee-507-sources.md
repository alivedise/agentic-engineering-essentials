# AEE-507 Reference Verification Notes

## Verified sources

| # | URL | Claim it supports | Status |
|---|---|---|---|
| 1 | https://github.com/obra/superpowers | Canonical Superpowers plugin repository | verified |
| 2 | https://github.com/obra/superpowers/blob/main/README.md | 7-stage workflow (brainstorming → using-git-worktrees → writing-plans → SDD/executing-plans → TDD → requesting-code-review → finishing-a-development-branch); four categories (Testing, Debugging, Collaboration, Meta); multi-platform install (Claude Code marketplace, Cursor marketplace, Codex, OpenCode, Copilot CLI, Gemini CLI) | verified |
| 3 | https://github.com/obra/superpowers/blob/main/CLAUDE.md | "This repo has a 94% PR rejection rate"; "Skills are not prose — they are code that shapes agent behavior"; stance on domain-specific skills, third-party dependencies, and compliance changes; "'your human partner' is deliberate, not interchangeable with 'the user'" | verified |
| 4 | https://github.com/obra/superpowers/blob/main/skills/using-superpowers/SKILL.md | 1% rule verbatim: "If you think there is even a 1% chance a skill might apply to what you are doing, you ABSOLUTELY MUST invoke the skill."; Red Flags table; SUBAGENT-STOP tag | verified |
| 5 | https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md | TDD mapping for skills: test case = pressure scenario; RED = agent violates without skill; GREEN = compliance with skill; refactor = close loopholes | verified |
| 6 | https://github.com/obra/superpowers/blob/main/skills/brainstorming/SKILL.md | HARD-GATE verbatim: "Do NOT invoke any implementation skill, write any code, scaffold any project, or take any implementation action until you have presented a design and the user has approved it." | verified |
| 7 | https://blog.fsck.com/2025/10/09/superpowers/ | Jesse Vincent's Oct 9, 2025 blog post introducing Superpowers; framing of skills as the mechanism that gives agents their "superpowers" | verified |
| 8 | https://code.claude.com/docs/en/skills | Anthropic's canonical Claude Code skills documentation; serves as contrast reference (Superpowers' CLAUDE.md explicitly notes it differs from Anthropic's skill-authoring guidance) | verified |

## Verified quotations to cite

- "This repo has a 94% PR rejection rate." — CLAUDE.md
- "Skills are not prose — they are code that shapes agent behavior." — CLAUDE.md
- "If you think there is even a 1% chance a skill might apply to what you are doing, you ABSOLUTELY MUST invoke the skill." — using-superpowers SKILL.md
- "Do NOT invoke any implementation skill, write any code, scaffold any project, or take any implementation action until you have presented a design and the user has approved it." — brainstorming SKILL.md
- "'your human partner' is deliberate, not interchangeable with 'the user'" — CLAUDE.md

## Final reference list

- [Superpowers — obra/superpowers](https://github.com/obra/superpowers) — canonical plugin repository.
- [Superpowers README](https://github.com/obra/superpowers/blob/main/README.md) — framework overview, seven-stage workflow, skill categories, multi-platform install.
- [Superpowers contributor guide — CLAUDE.md](https://github.com/obra/superpowers/blob/main/CLAUDE.md) — opinionated contribution stance; "skills are not prose — they are code" philosophy.
- [using-superpowers skill](https://github.com/obra/superpowers/blob/main/skills/using-superpowers/SKILL.md) — entry-point skill; 1% rule, Red Flags table, SUBAGENT-STOP.
- [writing-skills skill](https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md) — meta-skill; TDD mapping for skill creation.
- [brainstorming skill](https://github.com/obra/superpowers/blob/main/skills/brainstorming/SKILL.md) — HARD-GATE pattern in action.
- [Superpowers for Claude Code — Jesse Vincent](https://blog.fsck.com/2025/10/09/superpowers/) — author's blog post introducing the framework.
- [Extend Claude with skills — Claude Code docs](https://code.claude.com/docs/en/skills) — Anthropic's canonical skill-authoring documentation; contrast reference.

## Notes

All sources fetched and verified 2026-04-19. Local read of plugin v5.0.7 files during brainstorming confirmed GitHub content matches for the key quoted passages.
