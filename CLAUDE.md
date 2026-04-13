# CLAUDE.md

## Repository Overview

This is a VitePress-based bilingual (EN + zh-TW) documentation site for Agentic Engineering Essentials (AEE).

## Commands

- `pnpm docs:dev` -- Start VitePress development server
- `pnpm docs:build` -- Build documentation for production
- `pnpm docs:preview` -- Preview built documentation

## Architecture

- **VitePress 1.3.x** with custom theme (violet branding, `#7c3aed`)
- **Bilingual**: EN content in `docs/en/`, zh-TW content in `docs/zh-tw/`
- **Dynamic sidebar**: Generated from markdown frontmatter at build time
- **Mermaid diagrams**: Used for architecture and flow diagrams
- **PWA support**: Offline-capable with service worker

## Content Conventions

- Each AEE file uses frontmatter: `id` (number), `title`, `state` (draft/reviewing/approved), `overview` (boolean, only for category overviews)
- File names match the AEE id: e.g., `700.md` for AEE-700
- AEE articles follow the template: Context, Design Think, Deep Dive (optional), Best Practices (optional), Visual (optional), Examples (optional), Related AEEs, References, Changelog
- `Design Think` is required and replaces "Principle" from FEE/BEE -- signals architectural framing, not prescriptive rules
- `Changelog` is required -- format: `YYYY-MM-DD -- description`
- Uses RFC 2119 keywords (MUST, SHOULD, MAY) for guidance levels
- EN and zh-TW content are parallel -- every EN file has a zh-TW counterpart

## Content Quality

Every article MUST be researched against authoritative sources. AI internal knowledge alone is insufficient. References must contain real, verified URLs.

## Content Neutrality

This project is vendor-neutral. Do not include company-specific references, internal URLs, or product names. When a specific tool (e.g., Claude Code) is used as an example, label it clearly as an example.
