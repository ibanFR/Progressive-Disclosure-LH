# Research: YouTube MCP Server (`zubeid-youtube-mcp-server`) for How-To + Reference Docs

**Date**: 2026-07-19 | **Researcher**: nw-researcher (Nova) | **Confidence**: High | **Sources**: 8 (all trusted-list authorities)

## Executive Summary

The `youtube` MCP server registered in this repository's `.mcp.json` is [`zubeid-youtube-mcp-server`](https://www.npmjs.com/package/zubeid-youtube-mcp-server), an MIT-licensed, TypeScript, stdio MCP server authored by Zubeid Hendricks. It is fetched on demand via `npx -y zubeid-youtube-mcp-server` (no global install needed), wraps the **YouTube Data API v3** through the `googleapis` SDK, and reads its API key from the `YOUTUBE_API_KEY` environment variable. The repo (`github.com/ZubeidHendricks/youtube-mcp-server`) is actively maintained: latest npm version is **1.0.2**, published **2026-07-16**, with the package first created **2025-04-26**.

The server exposes **10 tools** across four groups — videos, transcripts, channels, and playlists. The 10 tools documented in the repo/README match **exactly** the 10 tools connected in this session (`videos_searchVideos`, `videos_getVideo`, `transcripts_getTranscript`, `channels_searchChannels`, `channels_findCreators`, `channels_getChannel`, `channels_getChannels`, `channels_listVideos`, `playlists_getPlaylist`, `playlists_getPlaylistItems`), so there are no missing or extra tools to reconcile.

This repo's config uses Claude Code's environment-variable expansion — `"YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}"` — a documented, supported pattern where Claude Code expands `${YOUTUBE_API_TOKEN}` from the host environment and passes it to the server process as `YOUTUBE_API_KEY`.

**Two items remain UNVERIFIED and require direct source confirmation before publishing**: (1) the **minimum Node.js version** (confirm via `engines.node` in the package's `package.json`), and (2) the **exact per-operation YouTube Data API v3 quota costs** — the default daily quota of 10,000 units is well-corroborated, but the WebFetch summarizer returned internally inconsistent per-method figures (notably for `search.list`), so exact per-call unit costs must be confirmed against the live cost table. A third, related caveat: the extended tool filter parameters were single-sourced from the README and not all confirmed in the source files spot-checked (see Finding 9).

## Research Methodology

**Search Strategy**: Direct retrieval from the four authoritative domains named in the task brief — the GitHub repo page and raw README, the npm registry JSON API (the human npm page returned HTTP 403 to WebFetch), Google's YouTube Data API v3 docs, and the Claude Code MCP documentation.
**Source Selection**: Official/technical-docs only (github.com, npmjs.com/registry.npmjs.org, developers.google.com, code.claude.com). No community or excluded-tier sources used.
**Quality Standards**: Tool list cross-referenced across three independent points (GitHub repo page, raw README, live session tool list). Config/env behavior corroborated by Claude Code docs. Single-source claims explicitly labeled.

## Findings

### Finding 1: Canonical repository, author, license, and description
**Evidence**: Repo description — "MCP Server for YouTube API, enabling video management, Shorts creation, and advanced analytics"; License: MIT; Author: ZubeidHendricks; primary language TypeScript.
**Source**: [GitHub — ZubeidHendricks/youtube-mcp-server](https://github.com/ZubeidHendricks/youtube-mcp-server) - Accessed 2026-07-19
**Confidence**: High
**Verification**: [raw README.md](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md) ("licensed under the MIT License"); [npm registry JSON](https://registry.npmjs.org/zubeid-youtube-mcp-server) (author "Zubeid Hendricks", license MIT, description "YouTube MCP Server Implementation").
**Analysis**: Canonical repo URL confirmed as `github.com/ZubeidHendricks/youtube-mcp-server`; the npm package `zubeid-youtube-mcp-server` is the published artifact of that repo.

### Finding 2: Exact npm package name and `npx -y` fetch behavior
**Evidence**: Package name `zubeid-youtube-mcp-server`; documented install options include "Via NPX (no installation): `npx -y zubeid-youtube-mcp-server`" and global `npm install -g zubeid-youtube-mcp-server`. The package `bin` maps `zubeid-youtube-mcp-server` → `dist/cli.js`.
**Source**: [raw README.md](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md) - Accessed 2026-07-19
**Confidence**: High
**Verification**: [npm registry JSON](https://registry.npmjs.org/zubeid-youtube-mcp-server) (bin entry); [GitHub repo page](https://github.com/ZubeidHendricks/youtube-mcp-server).
**Analysis**: `npx -y` downloads (if not cached) and runs the package's `bin` without a global install; the `-y` flag auto-confirms the npx package-install prompt. This matches this repo's `.mcp.json` `args: ["-y", "zubeid-youtube-mcp-server"]`.

### Finding 3: Maintenance status and versioning
**Evidence**: Latest npm version **1.0.2**, published **2026-07-16 14:13:35 UTC**; package created **2025-04-26**. Dependencies: `@modelcontextprotocol/sdk ^1.1.1`, `googleapis ^173.0.0`, `youtube-transcript ^1.3.1`, `dotenv ^17.3.1`.
**Source**: [npm registry JSON](https://registry.npmjs.org/zubeid-youtube-mcp-server) - Accessed 2026-07-19
**Confidence**: High (version/dates from registry API, the authoritative publish record)
**Verification**: [GitHub repo page](https://github.com/ZubeidHendricks/youtube-mcp-server) reported an active repo. Star/fork/commit counts from the GitHub page summary are NOT independently re-verified — see Knowledge Gaps.
**Analysis**: A publish within days of this research (2026-07-16) indicates active maintenance. The `googleapis` and MCP SDK deps confirm it wraps the official Google API client and current MCP protocol.

### Finding 4: Installation prerequisites and Claude Code stdio registration
**Evidence**: A stdio server in Claude Code is registered with `command`, `args`, and `env` in `.mcp.json` at the project root (project scope, shared via version control). Example standardized format: `{"mcpServers": {"shared-server": {"command": "/path/to/server", "args": [], "env": {}}}}`.
**Source**: [Claude Code — Connect to tools via MCP](https://code.claude.com/docs/en/mcp) - Accessed 2026-07-19
**Confidence**: High
**Verification**: This repo's [`.mcp.json`](../../.mcp.json) `youtube` entry matches the stdio shape (command `npx`, args `["-y","zubeid-youtube-mcp-server"]`, env map).
**Analysis**: `npx` requires Node.js/npm on PATH. Exact minimum Node.js version is not stated in the README fetch (see Knowledge Gaps); `googleapis ^173` and modern npx practically imply Node 18+, but treat the specific floor as UNVERIFIED. Note a divergence: the README's own example uses the global-install binary as `command` (`"command": "zubeid-youtube-mcp-server"`) and names the server key after the package, whereas this repo uses `command: "npx"` and the shorter key `youtube`; both are valid stdio configurations.

### Finding 5: Environment-variable mapping `"YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}"`
**Evidence**: Claude Code "supports environment variable expansion in `.mcp.json`" with syntax `${VAR}` (expands to the value of `VAR`) and `${VAR:-default}`. Expansion is supported in `command`, `args`, and `env` (and `url`/`headers` for HTTP). "If a referenced environment variable isn't set and has no default value, the config still loads: Claude Code reports a missing-variable warning ... in `claude mcp list`."
**Source**: [Claude Code — MCP, "Environment variable expansion in `.mcp.json`"](https://code.claude.com/docs/en/mcp) - Accessed 2026-07-19
**Confidence**: High
**Verification**: Server side — [raw README.md](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md) confirms the server reads `YOUTUBE_API_KEY` from its environment.
**Analysis**: The pattern works in two stages: (1) Claude Code expands `${YOUTUBE_API_TOKEN}` from your host/shell environment; (2) it passes the result to the spawned server process under the key `YOUTUBE_API_KEY`, which is the variable the server reads. So the host must export `YOUTUBE_API_TOKEN`; if unset, Claude Code loads the config but warns about the missing variable and the server will lack a key.

### Finding 6: Obtaining a YouTube Data API v3 key (Google Cloud Console)
**Evidence**: Steps: (1) have a Google Account; (2) create a project in the Google Cloud / API Console; (3) enable "YouTube Data API v3" (Enabled APIs page → status ON); (4) create credentials — an API key — for the project. OAuth 2.0 is only needed for methods requiring user authorization (not required for the public read/search/list operations this server uses).
**Source**: [Google — YouTube Data API v3, Getting Started](https://developers.google.com/youtube/v3/getting-started) - Accessed 2026-07-19
**Confidence**: High (single authoritative source; this is the canonical Google doc)
**Verification**: Single authoritative source (Google official docs). Consistent with the server's use of an API key (not OAuth) for read-only search/metadata/list operations per README env vars.
**Analysis**: For a coding-agent read-only use case (search, metadata, transcripts, channels, playlists), a simple API key is sufficient; OAuth is not required. Restrict the key (API restriction to YouTube Data API v3; optionally application restrictions) in Cloud Console as a best practice.

### Finding 7: Quota considerations
**Evidence**: Projects receive a default quota; the recurring figure across Google's docs is **10,000 units per day**. Costs are per method: read/list operations are cheap and `search.list` is the notably expensive read operation. `captions` operations are heavier than plain list calls.
**Source**: [Google — YouTube Data API v3, Determine quota cost](https://developers.google.com/youtube/v3/determine_quota_cost) and [Getting Started](https://developers.google.com/youtube/v3/getting-started) - Accessed 2026-07-19
**Confidence**: Medium — default 10,000 units/day is well-corroborated; exact per-method unit costs returned inconsistently from the summarizer (see Conflicting Information). Widely published values (to confirm against the live table): `search.list` = 100 units; `videos.list`/`channels.list`/`playlists.list`/`playlistItems.list` = 1 unit each; `captions.list` ≈ 50 units.
**Analysis**: Because `search.list` (used by `videos_searchVideos`, `channels_searchChannels`, `channels_findCreators`) is the dominant quota consumer, heavy search usage exhausts quota far faster than metadata/list calls. The server's support for `YOUTUBE_API_KEY2`/`YOUTUBE_API_KEY3` fallback keys is a mitigation for quota exhaustion — the README states verbatim: "At least one of `YOUTUBE_API_KEY`, `YOUTUBE_API_KEY2`, or `YOUTUBE_API_KEY3` must be set. When a request fails because a key has exhausted its quota, the server retries the same request with the next configured key." Source: [raw README.md](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md), accessed 2026-07-19. Confidence: High (verified against README).

### Finding 8: API key security best practices
**Evidence**: A `${VAR}` reference keeps the literal secret out of `.mcp.json` (the file holds only the variable reference), so the checked-in project config carries no secret. Claude Code loads the config even when the variable is unset (warning only), avoiding hard failures in shared repos.
**Source**: [Claude Code — MCP env var expansion](https://code.claude.com/docs/en/mcp) - Accessed 2026-07-19
**Confidence**: High (mechanism); general "never commit keys" is standard practice
**Verification**: Corroborated by the repo pattern itself — `.mcp.json` stores `${YOUTUBE_API_TOKEN}`, not a key value.
**Analysis**: Best practices: never commit the raw key; export `YOUTUBE_API_TOKEN` via shell profile or a secrets manager; restrict the key in Cloud Console; rotate keys and use the server's multi-key fallback rather than sharing one key widely.

### Finding 9: Capabilities / tools — full list, cross-checked against this session
**Evidence**: The repo and README document exactly 10 tools; all 10 match the tools connected in this session with no additions or omissions. Parameters below are from the GitHub repo/README.

| Tool | Group | Key parameters | Purpose |
|------|-------|----------------|---------|
| `videos_searchVideos` | videos | `query`, `maxResults`, `order`, `publishedAfter/Before`, `channelId`, `uniqueChannels`, `channelMin/MaxSubscribers`, `channelLastUploadAfter/Before`, `creatorOnly`, `sortBy` | Search videos with rich filters |
| `videos_getVideo` | videos | `videoId`, `parts` (optional) | Detailed metadata for one video |
| `transcripts_getTranscript` | transcripts | `videoId`, `language` (optional) | Extract a video transcript |
| `channels_getChannel` | channels | `channelId` | Details for one channel |
| `channels_getChannels` | channels | `channelIds`, `includeLatestUpload` (optional) | Details for multiple channels |
| `channels_searchChannels` | channels | `query`, `maxResults`, `order`, `channelType`, `min/maxSubscribers`, `lastUploadAfter/Before`, `creatorOnly`, `sortBy` | Search channels |
| `channels_findCreators` | channels | `query`, `videoPublishedAfter/Before`, `channelMin/MaxSubscribers`, `channelLastUploadAfter/Before`, `creatorOnly`, `sortBy`, `sampleVideosPerChannel` | Discover creator channels from a video query |
| `channels_listVideos` | channels | `channelId`, `maxResults` (optional) | List a channel's videos |
| `playlists_getPlaylist` | playlists | `playlistId` | Playlist metadata |
| `playlists_getPlaylistItems` | playlists | `playlistId`, `maxResults` (optional) | Items within a playlist |

**Source**: [GitHub repo page](https://github.com/ZubeidHendricks/youtube-mcp-server) and [raw README.md](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md) - Accessed 2026-07-19
**Confidence**: High for tool names (three-way cross-check: repo page, README, live session). **Mixed for parameters** — see verification note below.

**Parameter verification note (method + confidence)**: I spot-checked parameter names against the actual repo source at `src/functions/videos.ts` and `src/functions/channels.ts` (raw.githubusercontent.com), accessed 2026-07-19. Results:
- **Confirmed against source** (High confidence): `videoId` and `parts` (get-video); `query` and `maxResults` (search-videos); `channelId` (get-channel); `channelId` + `maxResults` (list channel videos).
- **NOT found in those two source files** (downgraded to Low/UNVERIFIED — single-sourced from the README summary only): the extended filter parameters `order`, `channelId` (on search), `creatorOnly`, `sortBy`, `min/maxSubscribers`, `channelLastUploadAfter/Before`, `sampleVideosPerChannel`, `includeLatestUpload`, and `uniqueChannels`.
- **Naming/registration nuance**: the source `functions/*.ts` files register bare function names (`getVideo`, `searchVideos`, `getChannel`, `getChannelVideos`, plus a `getVideoStats` not exposed here), whereas the tools connected in this session are namespaced/prefixed (`videos_getVideo`, `channels_listVideos`, etc.). The prefixed names and the richer filter schemas therefore appear to be assembled at a separate registration/aggregation layer (e.g. `src/server.ts` / `src/functions/index.ts`) that I did not exhaustively read within budget. The extended parameters may well be real but are not confirmed by the two files checked — treat them as README-sourced pending a read of the registration layer or the published npm tarball's `dist/`.

**Verification**: Live session tool list (task brief) — identical set of 10 tool names; core parameters cross-checked against `src/functions/videos.ts` and `src/functions/channels.ts`.
**Analysis**: The repo description mentions "Shorts creation, and advanced analytics," and the source tree indeed contains `src/functions/analytics.ts`, `src/functions/content/shorts.ts`, `captions.ts`, `download.ts`, `thumbnails.ts`, and `translations.ts` — but none of these are exposed among the 10 connected tools, and no write/upload tools appear in the exposed set. The shipped/connected tools are read/search only; treat "video management / Shorts creation / analytics" as present-in-source-but-not-exposed rather than usable capabilities in this session (UNVERIFIED as connected tools).

### Finding 10: Verifying the server is connected
**Evidence**: Management/verification commands: `claude mcp list` (list all configured servers), `claude mcp get <name>` (details), and, inside Claude Code, `/mcp` (server status; "shows the tool count next to each connected server"). Project-scoped `.mcp.json` servers awaiting approval appear as "⏸ Pending approval (run `claude` to approve)"; a server with bad credentials shows `failed`.
**Source**: [Claude Code — MCP, "Managing your servers"](https://code.claude.com/docs/en/mcp) - Accessed 2026-07-19
**Confidence**: High
**Verification**: Same doc, "Practical examples" ("run `/mcp` and check that the server shows `connected`").
**Analysis**: For this repo, expect `claude mcp list` to show `youtube` and `/mcp` to report it connected with a tool count of 10. Because it is project-scoped in `.mcp.json`, first use requires approving the project server (trust dialog / `claude`), and a missing `YOUTUBE_API_TOKEN` yields a missing-variable warning in `claude mcp list`.

## Sample Prompts (for the How-To guide)

Natural-language prompts a user would give the coding agent to exercise each capability:

- **Video search** (`videos_searchVideos`): "Find the 5 most-viewed videos about 'Rust async runtime' published in the last year."
- **Video metadata** (`videos_getVideo`): "Get the title, channel, view count, and duration for the YouTube video with ID `dQw4w9WgXcQ`."
- **Transcript** (`transcripts_getTranscript`): "Fetch the transcript of video `dQw4w9WgXcQ` and summarize the three main points."
- **Channel lookup** (`channels_getChannel` / `channels_searchChannels`): "Find the channel 'Fireship' and tell me its subscriber count and recent upload cadence."
- **Multi-channel** (`channels_getChannels`): "Compare subscriber counts for these channel IDs and show their latest uploads."
- **Creator discovery** (`channels_findCreators`): "Find active creators who make videos about 'homelab kubernetes' with at least 10k subscribers."
- **List channel videos** (`channels_listVideos`): "List the 10 most recent videos from the channel `UCsBjURrPoezykLs9EqgamOA`."
- **Playlist** (`playlists_getPlaylist` / `playlists_getPlaylistItems`): "Show me all the videos in playlist `PL9tY0BWXOZF... ` with their titles and positions."

**Source basis**: Prompts constructed from the tool parameter list in Finding 9; illustrative, not quoted from a source.

## Source Analysis
| Source | Domain | Reputation | Type | Access Date | Cross-verified |
|--------|--------|------------|------|-------------|----------------|
| ZubeidHendricks/youtube-mcp-server (repo page) | github.com | 0.95 | Official (project) | 2026-07-19 | Y |
| youtube-mcp-server README (raw) | raw.githubusercontent.com | 0.95 | Official (project) | 2026-07-19 | Y |
| zubeid-youtube-mcp-server (registry JSON) | registry.npmjs.org | 0.90 | Official (package) | 2026-07-19 | Y |
| YouTube Data API v3 — Getting Started | developers.google.com | 0.98 | Official | 2026-07-19 | Partial |
| YouTube Data API v3 — Determine quota cost | developers.google.com | 0.98 | Official | 2026-07-19 | Partial |
| Claude Code — Connect to tools via MCP | code.claude.com | 0.95 | Official | 2026-07-19 | Y |
| youtube-mcp-server src/functions/videos.ts | raw.githubusercontent.com | 0.95 | Official (source) | 2026-07-19 | Y |
| youtube-mcp-server src/functions/channels.ts | raw.githubusercontent.com | 0.95 | Official (source) | 2026-07-19 | Y |

Reputation: High (>=0.9): 8 (100%) | Avg: ~0.95. All sources on the trusted allowlist; no excluded-tier sources used.

## Knowledge Gaps

### Gap 1: Exact YouTube Data API v3 per-method quota costs
**Issue**: The `determine_quota_cost` page's per-method cost table returned inconsistently via WebFetch (summarizer repeatedly asserted `search.list` = 1 unit, conflicting with the long-documented 100-unit cost). **Attempted**: Two fetches of `developers.google.com/youtube/v3/determine_quota_cost` plus the getting-started page. **Recommendation**: Before publishing exact numbers, read the live cost table directly in a browser; the default 10,000 units/day figure is safe to state, and `search.list` being much costlier than list calls is safe qualitatively.

### Gap 2: Minimum Node.js version
**Issue**: README fetch did not state a required Node.js version. **Attempted**: raw README + npm registry JSON. **Recommendation**: Check `engines` in the package's `package.json`; state "Node.js (LTS recommended, likely 18+)" until confirmed. Marked UNVERIFIED.

### Gap 3: Repository popularity/activity metrics
**Issue**: Star/fork/commit counts and "latest release date" came only from the GitHub page WebFetch summary and were not independently re-verified; such summarized numbers can be stale or approximate. **Attempted**: GitHub repo page fetch. **Recommendation**: Treat exact stars/forks as indicative only; the npm publish date (2026-07-16, from registry API) is the reliable recency signal.

### Gap 4: Extended tool filter parameters not fully confirmed in source
**Issue**: The rich filter parameters in the Finding 9 table (`creatorOnly`, `sortBy`, subscriber ranges, `sampleVideosPerChannel`, `includeLatestUpload`, etc.) were single-sourced from the README and were NOT found in `src/functions/videos.ts` or `src/functions/channels.ts` when spot-checked. **Attempted**: raw source reads of those two files (core params confirmed; extended params absent). **Recommendation**: Read the registration/aggregation layer (`src/server.ts`, `src/functions/index.ts`) or the published npm tarball's `dist/` to confirm the extended schemas before documenting them as authoritative in the reference doc.

### Gap 5: npm human page inaccessible
**Issue**: `npmjs.com/package/...` returned HTTP 403 to WebFetch. **Attempted**: direct fetch. **Mitigation**: Used `registry.npmjs.org` JSON API (authoritative equivalent) instead — no data lost.

## Conflicting Information

### Conflict 1: `search.list` quota cost
**Position A**: `search.list` costs 1 unit — Source: WebFetch summary of [determine_quota_cost](https://developers.google.com/youtube/v3/determine_quota_cost), Reputation 0.98, but summary appears to reflect an outdated/pre-2019 quota model.
**Position B**: `search.list` is the expensive read operation (widely documented at 100 units), consistent with Google's current unit-cost model.
**Assessment**: The summarizer likely conflated an old quota page. The current authoritative model treats `search.list` as far costlier than list calls. Do not publish "1 unit" for `search.list`; confirm against the live table (expected 100 units) — flagged UNVERIFIED pending direct read.

## Recommendations for Further Research
1. Open `developers.google.com/youtube/v3/determine_quota_cost` in a browser and transcribe the exact cost column for `search.list`, `videos.list`, `channels.list`, `playlists.list`, `playlistItems.list`, `captions.list`.
2. Confirm `engines.node` in the package `package.json` for the minimum Node.js version.
3. Confirm whether `transcripts_getTranscript` consumes YouTube Data API quota or uses the `youtube-transcript` dependency (which scrapes captions and may not count against Data API quota) — relevant for the reference doc's quota section.

## Full Citations
[1] ZubeidHendricks. "youtube-mcp-server". GitHub. Accessed 2026-07-19. https://github.com/ZubeidHendricks/youtube-mcp-server
[2] ZubeidHendricks. "youtube-mcp-server README". GitHub (raw). Accessed 2026-07-19. https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md
[3] Zubeid Hendricks. "zubeid-youtube-mcp-server (registry metadata)". npm registry. Accessed 2026-07-19. https://registry.npmjs.org/zubeid-youtube-mcp-server
[4] Google. "YouTube Data API v3 — Getting Started". developers.google.com. Accessed 2026-07-19. https://developers.google.com/youtube/v3/getting-started
[5] Google. "YouTube Data API v3 — Determine quota cost". developers.google.com. Accessed 2026-07-19. https://developers.google.com/youtube/v3/determine_quota_cost
[6] Anthropic. "Connect Claude Code to tools via MCP". Claude Code Docs. Accessed 2026-07-19. https://code.claude.com/docs/en/mcp
[7] ZubeidHendricks. "youtube-mcp-server src/functions/videos.ts". GitHub (raw). Accessed 2026-07-19. https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/src/functions/videos.ts
[8] ZubeidHendricks. "youtube-mcp-server src/functions/channels.ts". GitHub (raw). Accessed 2026-07-19. https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/src/functions/channels.ts

## Research Metadata
Examined: 8 sources (11 fetches; 1 npm page 403, mitigated via registry API) | Cited: 8 | Cross-refs: tool list 3-way, config/env 2-way, core tool params confirmed against source (videos.ts, channels.ts), fallback keys confirmed against README | Confidence: High 8 findings, Medium 2 (quota, Node version); extended tool params downgraded to UNVERIFIED | Revised 2026-07-19 per reviewer NEEDS_REVISION (fallback-keys citation, parameter source spot-check, exec-summary caveats) | Output: docs/research/youtube-mcp-server-for-howto-reference-doc.md
</content>
</invoke>
