# AEE-407 / AEE-408 Source Verification

Verification date: 2026-05-06
Source-input: /Users/alive/Projects/dev/knowledge-base/mcp-local-vs-remote.md (last cross-checked 2026-05-05)

## Required for both articles

| URL | Used for | Status | Notes |
|---|---|---|---|
| https://modelcontextprotocol.io/specification/2025-06-18 | MCP spec, source-input revision | OK 2026-05-06 | Live. Spec page renders normally; this revision is still reachable but is no longer the most-recent revision (see Spec version check below). Cite only when discussing the 2025-06-18-bound shape; otherwise prefer 2025-11-25. |
| https://modelcontextprotocol.io/specification/2025-06-18/basic/transports | Transports spec page (2025-06-18) | OK 2026-05-06 | Quote: "Clients **SHOULD** support stdio whenever possible." Defines stdio + Streamable HTTP, replaces 2024-11-05 HTTP+SSE. Session header in this revision is `Mcp-Session-Id` (mixed case). |
| https://modelcontextprotocol.io/introduction | Primer-level overview for AEE-407 | OK 2026-05-06 | Quote: "MCP (Model Context Protocol) is an open-source standard for connecting AI applications to external systems." Uses USB-C analogy. Names hosts/clients/servers and the resources/prompts/tools triad. |
| https://code.claude.com/docs/en/mcp | Claude Code MCP docs | OK 2026-05-06 | Live. Documents stdio, http, sse type configurations; bearer token + headers; OAuth flow including `oauth.scopes`; `headersHelper` script-based dynamic auth. Confirms shapes used in source-input file. |

## Newer MCP spec revision (added 2026-05-06)

| URL | Used for | Status | Notes |
|---|---|---|---|
| https://modelcontextprotocol.io/specification/2025-11-25 | Current MCP spec revision | OK 2026-05-06 | This is the latest revision listed on the `/specification` index. AEE-407 and AEE-408 SHOULD cite this as the current revision; the source-input file's 2025-06-18 references need version-bound updates (see Spec version check). |
| https://modelcontextprotocol.io/specification/2025-11-25/basic/transports | Transports spec page (2025-11-25) | OK 2026-05-06 | Same two-transport model. Material deltas vs 2025-06-18 listed under Spec version check. |

## AEE-408 specific

| URL | Used for | Status | Notes |
|---|---|---|---|
| https://developers.cloudflare.com/workers/platform/limits/ | Workers CPU/wall-clock limits | OK 2026-05-06 | Quote: "There is no hard limit on duration for HTTP-triggered Workers. As long as the client remains connected, the Worker can continue processing." Default CPU 30s; Paid plan configurable up to 5 min (300,000 ms). Wait on `fetch()` does not count against CPU time. |
| https://developers.cloudflare.com/workers/platform/pricing/ | Workers Paid plan price + included tiers | OK 2026-05-06 | $5/mo flat. Includes 10M requests + 30M CPU-ms. Overage: $0.30 per million requests, $0.02 per million CPU-ms. No data-transfer/egress charges. Matches December-2025 figures in source-input file (no drift). |
| https://developers.cloudflare.com/durable-objects/platform/pricing/ | Durable Objects pricing + WebSocket Hibernation | OK 2026-05-06 | Workers Paid includes 1M DO requests/mo and 400,000 GB-s/mo. Overage: $0.15/million requests, $12.50/million GB-s. Hibernation language: "Durable Objects that are idle and eligible for hibernation are not billed for duration, even before the runtime has hibernated them." Caveat: `accept()` on a WebSocket without using the Hibernation API incurs duration charges for the entire connection. Matches source-input file. |
| https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html | Lambda quotas / 15-min wall-clock | OK 2026-05-06 | Confirms function timeout 900 seconds (15 minutes) hard limit. Memory 128MB-10,240MB. Concurrency default 1,000. Page does not document cold-start latency benchmarks; that is not an AWS-published quota. Cold-start figures in source-input (Node.js 200-800ms typical, p95 1.2-2.8s, Firecracker-NG p99 ~42ms) are NOT verifiable from this AWS page; see Pricing snapshot below for the recommended treatment. |

## Pricing snapshot (verified 2026-05-06)

**Cloudflare Workers Paid (no drift from December-2025 figures in source-input):**
- $5 USD per month flat per account
- Included: 10,000,000 requests/mo + 30,000,000 CPU-ms/mo
- Overage: $0.30 per million additional requests, $0.02 per million additional CPU-ms
- No additional charges for egress / bandwidth
- Source: https://developers.cloudflare.com/workers/platform/pricing/

**Durable Objects on Workers Paid (no drift):**
- Included: 1,000,000 requests/mo + 400,000 GB-seconds/mo
- Overage: $0.15 per million additional requests, $12.50 per million additional GB-seconds
- WebSocket Hibernation: idle and eligible-for-hibernation Durable Objects are not billed for duration; without the Hibernation API, an accepted WebSocket incurs duration charges for the full connection
- Source: https://developers.cloudflare.com/durable-objects/platform/pricing/

**AWS Lambda:**
- Hard wall-clock limit: 900 seconds (15 min) per invocation
- Memory: 128 MB to 10,240 MB
- Default concurrency: 1,000 (region-scoped, soft limit)
- Source: https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html
- Cold-start figures: AWS does not publish quotas for cold-start latency. The source-input file's December-2025 numbers (Node.js typical 200-800ms, p95 1.2-2.8s, Firecracker-NG p99 ~42ms) cannot be verified against an AWS-canonical page. AEE-408 SHOULD soften this to "typically hundreds of milliseconds for Node.js, with newer Lambda runtime variants reducing tail latency; verify against current benchmarks for your runtime and region." OMIT specific Firecracker-NG p99 number unless we re-source it from AWS re:Invent 2025 announcements before publishing.

(Per spec risk #1: pricing drifts. Source-input figures stamped December 2025; verified five months later. Cloudflare Workers Paid plan and Durable Objects pricing structures match the December-2025 snapshot — no material drift to flag. AWS Lambda quotas (15-min, 1,000 concurrency, 10,240 MB memory) also unchanged.)

## CLI-vs-MCP practitioner sources for AEE-407 Best Practices

| URL | Author / outlet | What it supports | Status |
|---|---|---|---|
| https://www.bassimeledath.com/blog/levels-of-agentic-engineering | Bassim Eledath (personal blog, AEE-3 primary source) | "Every token needs to fight for its place in the prompt" — token-efficiency argument; CLI tools more efficient because targeted command output enters context, MCPs inject full tool schemas every turn whether used or not | OK 2026-05-06 |
| https://mariozechner.at/posts/2025-08-15-mcp-vs-cli/ | Mario Zechner (personal practitioner blog, terminalcp benchmark) | Empirical benchmark of MCP vs CLI for coding agents. Headline: "MCP vs CLI truly is a wash: Both terminalcp MCP and CLI versions achieved 100% success rates. The MCP version was 23% faster (51m vs 66m) and 2.5% cheaper ($19.45 vs $19.95)." Provides nuanced counterweight to the pure CLI-wins framing — MCP wins when tool design is good and reduces command-detection overhead. Useful for AEE-407 Best Practices to frame the tradeoff as design-dependent rather than transport-dependent. | OK 2026-05-06 |

Both sources verified live. AEE-407 Best Practices SHOULD cite both: Eledath for the structural-overhead framing, Zechner for the benchmark counterweight. Fallback (descriptive framing without single-source attribution) is no longer needed.

## Spec version check

- Current MCP spec revision at writing time: **2025-11-25** (verified 2026-05-06 via the `/specification` index page Card links pointing to `/specification/2025-11-25/...`).
- The source-input file references 2025-06-18 throughout. AEE-407 and AEE-408 SHOULD cite 2025-11-25 as the current revision unless a paragraph specifically discusses a feature that only existed in 2025-06-18.

### Material deltas: 2025-06-18 → 2025-11-25

These are version-bound details that need updating in any text inherited from the source-input file:

1. **Session header casing changed.** 2025-06-18 used `Mcp-Session-Id`; 2025-11-25 uses `MCP-Session-Id` (uppercase MCP). The source-input file's "the spec uses this exact header name with this casing" note is now stale — the casing changed across revisions. Articles SHOULD use `MCP-Session-Id` and may note the casing migration as a backwards-compat hazard.
2. **DNS-rebinding response code added.** 2025-11-25 adds: "If the `Origin` header is present and invalid, servers **MUST** respond with HTTP 403 Forbidden." Not in 2025-06-18.
3. **Stderr handling clarified.** 2025-11-25 adds: client "**MAY** capture, forward, or ignore the server's `stderr` output and **SHOULD NOT** assume `stderr` output indicates error conditions." 2025-06-18 only stated the server **MAY** write logging to stderr.
4. **SSE keep-alive primitives added.** 2025-11-25 introduces a server-initiated polling pattern: server SHOULD send a primer SSE event with empty data and an event ID, MAY close the connection (without terminating the SSE stream), and SHOULD send a `retry` field. Client SHOULD then poll with `Last-Event-ID`. 2025-06-18 had resumability but no explicit close-and-poll pattern. Useful for AEE-408 when discussing long-lived SSE on serverless platforms with idle-connection killers.
5. **Backwards-compat status codes specified.** 2025-11-25 names "400 Bad Request", "404 Not Found", or "405 Method Not Allowed" as the trigger to fall back to the 2024-11-05 HTTP+SSE GET-then-`endpoint`-event detection. 2025-06-18 said "HTTP 4xx" generically.
6. **Session-Hijacking security best practices doc** referenced from Session Management section in 2025-11-25; not in 2025-06-18. AEE-408 Best Practices SHOULD link to `/specification/2025-11-25/basic/security_best_practices` if that page is also live (verify before citing).

### Unchanged across revisions (safe to cite as either)

- Two transports: stdio and Streamable HTTP. Clients SHOULD support stdio whenever possible.
- Streamable HTTP replaces 2024-11-05 HTTP+SSE; both can coexist for backwards compat.
- `MCP-Protocol-Version` header required on HTTP requests after initialization; default to `2025-03-26` if absent.
- Resumability via `Last-Event-ID`; per-stream event IDs.
- Local HTTP servers SHOULD bind to 127.0.0.1; servers MUST validate `Origin`.

## Additional verified URL (added 2026-05-06)

| URL | Used for | Status | Notes |
|---|---|---|---|
| https://modelcontextprotocol.io/specification/2025-11-25/basic/security_best_practices | Security best practices for AEE-408 | OK 2026-05-06 | Live. Covers Confused Deputy (OAuth proxy), Token Passthrough anti-pattern, SSRF on OAuth metadata discovery, Session Hijacking (prompt injection + impersonation patterns), Local MCP Server Compromise (one-click install attacks), Scope Minimization. Strongly relevant to AEE-408 Best Practices section. Quote on session hijacking mitigation: "MCP servers **MUST NOT** use sessions for authentication." Quote on local-server risk: "An attacker includes a malicious 'startup' command in a client configuration". |

## Open items / followups before writing

- Before AEE-408 cites "Firecracker-NG p99 ~42ms" or any specific cold-start number, re-source from AWS re:Invent 2025 / current AWS Lambda performance announcements; otherwise soften per the Pricing snapshot guidance above.
- **Verify before citing in AEE-408 (Task 3):** Delta #1 (session header casing). Re-fetch `https://modelcontextprotocol.io/specification/2025-06-18/basic/transports` and search for `Mcp-Session-Id` vs `MCP-Session-Id`. If 2025-06-18 is already uppercase in the published HTML, drop Delta #1 from the article — there is no backwards-compat casing migration to call out. Only cite the casing migration if the older revision genuinely used the mixed-case form.
- **Verify before citing in AEE-408 (Task 3):** Delta #4 (SSE close-and-poll / `retry` field). Diff the transports-page section on SSE-stream handling between 2025-06-18 and 2025-11-25. If the close-and-poll pattern was already present in 2025-06-18 and only the `retry`-field requirement is new, narrow the delta to the `retry` field — don't claim the close-and-poll pattern itself was introduced in 2025-11-25.
