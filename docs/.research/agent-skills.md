# Agent Skills Research Notes

Research date: 2026-04-14
Researcher: Claude Sonnet 4.6 (automated pass)

---

## AEE-501: What Is an Agent Skill

### Verified claims

- The term "agent skill" has an official open standard definition at https://agentskills.io
- agentskills.io defines: "Agent Skills are folders of instructions, scripts, and resources that agents can discover and use to do things more accurately and efficiently."
- The format was originally developed by Anthropic, then released as an open standard adopted by the broader ecosystem (confirmed from agentskills.io overview).
- As of April 2026, 30+ tools implement the standard, including Claude Code, Gemini CLI, GitHub Copilot, Cursor, OpenCode, Junie (JetBrains), VS Code, Amp, Roo Code, and others.
- Open standard GitHub repo: https://github.com/agentskills/agentskills
- The structural unit of a skill is a directory containing `SKILL.md` as the required entrypoint, with optional supporting files (templates, examples, scripts, reference docs).

### CRITICAL FLAGS

None. The foundational definition is well-documented at the open standard site.

---

## AEE-502: The Agent Skill Ecosystem

### Superpowers (obra/superpowers)

- **What it is**: An open-source agentic skills framework and software development methodology, NOT developed by Anthropic. Created by Jesse Vincent (obra) and community.
- **Official plugin listing**: https://claude.com/plugins/superpowers (Anthropic marketplace entry, accepted January 2026)
- **GitHub repo**: https://github.com/obra/superpowers
- **Installation via Claude Code plugin system**: `/plugin install superpowers@claude-plugins-official`
  - Source: GitHub README (fetched) and claude.com/plugins/superpowers page
- **npm package name**: CRITICAL FLAG — no dedicated npm package name confirmed from official sources. Distribution is via the Claude Code plugin system (`/plugin install`) and GitHub, not npm. The search found no `superpowers` or `obra-superpowers` package on npmjs.com for this project. (A community Chinese edition `superpowers-zh` exists on npm but that is a derivative.)
- **Skill invocation**: Skills trigger automatically when their description matches the task context. Users can also invoke directly with `/skill-name` (e.g., `/brainstorming`, `/execute-plan`). Both auto-invocation and manual slash invocation are supported.
  - Source: https://claude.com/plugins/superpowers, https://github.com/obra/superpowers
- **Note on "superpowers" terminology in Claude Code docs**: The old term "superpowers" (as a plugin system name) has been superseded. Claude Code now uses the generic term "skills" for this mechanism, conforming to the Agent Skills open standard. The `obra/superpowers` project is a specific community plugin that installs a set of skills.

### Claude Code Skills (formerly "slash commands" / "custom commands")

- **Official docs URL**: https://code.claude.com/docs/en/skills
  - (The old URL https://docs.anthropic.com/en/docs/claude-code/slash-commands now permanently redirects to https://code.claude.com/docs/en/slash-commands, which in turn serves the skills page.)
- **Current system**: "Custom commands" have been merged into "skills" as of the current docs. The `.claude/commands/` path still works but skills (`.claude/skills/`) are the recommended approach.
- **Storage paths — CONFIRMED**:
  - Personal (user-level): `~/.claude/skills/<skill-name>/SKILL.md` — confirmed
  - Project-level: `.claude/skills/<skill-name>/SKILL.md` — confirmed
  - Legacy custom commands path still works: `.claude/commands/<name>.md` — confirmed as still supported; "Your existing `.claude/commands/` files keep working."
  - Note: The old `~/.claude/commands/` path (user-level custom commands) is implicitly still supported but the docs now direct users to `~/.claude/skills/`. The docs do NOT explicitly call out `~/.claude/commands/` as the user-level commands path in current documentation.
- **CRITICAL FLAG — ~/.claude/commands/ user-level path**: The current Claude Code docs (fetched from code.claude.com/docs/en/skills, April 2026) show the user-level path is now `~/.claude/skills/`. The old `~/.claude/commands/` user-level path is not confirmed in current docs; only the project-level `.claude/commands/` backward-compat path is mentioned. If AEE-502 claims `~/.claude/commands/` as the user-level storage, that claim needs qualification: it may have been accurate historically but the current docs point to `~/.claude/skills/` for new usage.
- **Invocation**: `/skill-name` syntax — confirmed. Users type `/` followed by the skill name. Skills can also be invoked automatically by Claude when their description matches the task.
- **Commands reference**: https://code.claude.com/docs/en/commands

### Agent Skills Open Standard (cross-platform)

- **Standard URL**: https://agentskills.io
- **Specification**: https://agentskills.io/specification
- **Claude Code implements this standard**: confirmed (listed on agentskills.io with instructionsUrl pointing to code.claude.com/docs/en/skills)
- **Cursor also implements this standard**: confirmed — Cursor is listed on agentskills.io with instructionsUrl `https://cursor.com/docs/context/skills`
- **GitHub**: https://github.com/agentskills/agentskills

### Cursor Rules

- **Official docs URL for "rules"**: https://cursor.com/docs/rules
  - Note: The original URL https://docs.cursor.com/context/rules-for-ai now returns a 308 redirect to https://cursor.com/docs. The rules page is at cursor.com/docs/rules.
- **Current file location — CONFIRMED**: Project rules live in `.cursor/rules` as markdown files. Both `.md` and `.mdc` extensions are supported.
  - Direct quote from fetched docs: "Project rules live in `.cursor/rules` as markdown files and are version-controlled."
- **MDC format — CONFIRMED**: MDC files are markdown files with YAML frontmatter. Cursor docs explicitly state: "Cursor supports `.md` and `.mdc` extensions. Use `.mdc` files with frontmatter to specify `description` and `globs`."
  - Frontmatter fields include: `description`, `alwaysApply`, `globs`
- **`.cursorrules` (legacy)**: The fetched docs page does NOT mention `.cursorrules` as a supported location. CRITICAL FLAG — whether `.cursorrules` (the older single-file format) is still supported cannot be confirmed from the current docs fetch. The current canonical path is `.cursor/rules/*.mdc` (or `.md`).
- **Cursor also has a "skills" page**: The agentskills.io standard lists Cursor's skills docs at https://cursor.com/docs/context/skills — this appears to be separate from or an evolution of the "rules" system, worth verifying for AEE-502.
- **CRITICAL FLAG — Cursor skills vs rules distinction**: Cursor appears to implement BOTH the Agent Skills standard (at cursor.com/docs/context/skills) AND has its own "rules" system (at cursor.com/docs/rules). The relationship between these two features is not fully clear from the research. If the article covers Cursor, it should distinguish between Cursor Rules (`.cursor/rules/*.mdc`) and Cursor Skills (Agent Skills standard implementation).

### Custom GPT Instructions (OpenAI)

- **Official docs URL — creating a GPT**: https://help.openai.com/en/articles/8554397-creating-a-gpt
  - Note: Direct fetch returned HTTP 403 (OpenAI Help Center blocks automated fetching). URL confirmed from search results against help.openai.com domain.
- **Official docs URL — key guidelines for instructions**: https://help.openai.com/en/articles/9358033-key-guidelines-for-writing-instructions-for-custom-gpts
  - Same 403 restriction applies; URL confirmed from search.
- **How instructions load**: GPT Builder instructions are applied as a persistent system-level prompt to every conversation with the Custom GPT. They define behavior, tone, constraints, and decision-making for all user interactions. Unlike "Knowledge" (uploaded files used as retrieval context), instructions are structural — they define how the GPT behaves, not what it knows.
  - Source: OpenAI search results citing help.openai.com articles
- **Character limits**: Instructions field has up to 8,000 characters (GPT Builder). Standard ChatGPT Custom Instructions have a 1,500-character cap per field.
- **CRITICAL FLAG — direct URL verification blocked**: Both OpenAI Help Center URLs returned HTTP 403 during fetch verification. The URLs are confirmed as real from search engine results indexing help.openai.com, but the page content could not be directly verified by fetching. The description of how instructions load is sourced from search result snippets, not direct page fetch.

### CRITICAL FLAGS (summary)

1. **Superpowers npm package name**: No dedicated npm package found. Distribution is via `/plugin install superpowers@claude-plugins-official` (Claude plugin system) or git clone. Do not claim an npm package name in the article without further verification.

2. **`~/.claude/commands/` as user-level storage**: The current Claude Code docs (April 2026) have migrated to `~/.claude/skills/` as the user-level path. The `~/.claude/commands/` path is not confirmed as the current user-level storage location. If used in AEE-502, qualify as "legacy" or "also supported."

3. **`.cursorrules` legacy path**: Current Cursor docs do not mention `.cursorrules`. The canonical path is `.cursor/rules/*.mdc`. Any claim that `.cursorrules` is still supported needs verification against current Cursor docs.

4. **OpenAI Custom GPT docs — fetch blocked**: OpenAI help.openai.com returns 403 to automated fetches. URL existence confirmed via search index; content description sourced from search snippets only.

5. **Cursor Rules vs Cursor Skills**: Cursor appears to have two overlapping features — "Rules" (cursor.com/docs/rules, `.cursor/rules/*.mdc`) and "Skills" (cursor.com/docs/context/skills, Agent Skills standard). The article should clarify which one it is discussing and whether they are the same feature under different names.

---

## AEE-503 through AEE-506

Note: These are principle articles (e.g., skill authoring best practices, versioning, naming conventions). No external platform citations are required beyond the following well-known references:

- Semantic Versioning: https://semver.org — well-known, stable URL, no verification needed
- Keep a Changelog: https://keepachangelog.com — well-known, stable URL, no verification needed
- Agent Skills open standard specification: https://agentskills.io/specification — fetched and confirmed live

No CRITICAL FLAGS for AEE-503 through AEE-506.

---

## Source URLs fetched during research

| URL | Status | Notes |
|-----|--------|-------|
| https://code.claude.com/docs/en/skills | 200 OK | Primary Claude Code skills docs (full content retrieved) |
| https://code.claude.com/docs/en/overview | 200 OK | Claude Code overview page |
| https://code.claude.com/docs/en/commands | 200 OK | Commands reference |
| https://cursor.com/docs/rules | 200 OK | Cursor rules docs |
| https://agentskills.io | 200 OK | Agent Skills open standard |
| https://claude.com/plugins/superpowers | 200 OK (partial) | Plugin listing; npm package name not found |
| https://github.com/obra/superpowers README | 200 OK (partial) | No npm package name; plugin install command found |
| https://help.openai.com/en/articles/8554397 | 403 Forbidden | Confirmed URL via search; content unavailable |
| https://help.openai.com/en/articles/9358033 | 403 Forbidden | Confirmed URL via search; content unavailable |
| https://docs.anthropic.com/en/docs/claude-code/slash-commands | 301 → code.claude.com | Redirect confirmed |
| https://docs.cursor.com/context/rules-for-ai | 308 → cursor.com/docs | Redirect confirmed; rules page at cursor.com/docs/rules |
