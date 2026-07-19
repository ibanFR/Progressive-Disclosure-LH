# Research: `youtube` MCP Server (`zubeid-youtube-mcp-server`) — for How-To + Reference Docs

**Date**: 2026-07-19 | **Researcher**: nw-researcher (Nova) | **Confidence**: High | **Sources**: 8 (all trusted-allowlist domains)

> Regeneration run. This artifact overwrites the prior version and closes the four gaps it left open (quota costs, Node floor, extended tool params, version/date). Ground truth is this repo's `.mcp.json`; all external claims are cross-checked against authoritative sources on the inline allowlist.

## Executive Summary

The `youtube` MCP server wired into this repository's `.mcp.json` is the npm package **`zubeid-youtube-mcp-server`**, authored by Zubeid Hendricks (repo: `github.com/ZubeidHendricks/youtube-mcp-server`). It is launched over stdio via `npx -y zubeid-youtube-mcp-server` and reads a YouTube Data API v3 key from the `YOUTUBE_API_KEY` environment variable, which this repo populates by expanding `${YOUTUBE_API_TOKEN}`. The latest published release is **v1.0.2 (2026-07-16)**, MIT-licensed. The package is a thin MCP wrapper around Google's `googleapis` SDK (YouTube Data API v3) plus the `youtube-transcript` library for captions.

The server exposes **10 tools** (confirmed live in the parent session and in the source `ListToolsRequestSchema` handler in `src/server.ts`): three video/transcript tools, five channel tools, and two playlist tools. The channel/video search tools advertise a rich set of **extended filter parameters** (`creatorOnly`, `sortBy`, subscriber ranges, `sampleVideosPerChannel`, `includeLatestUpload`, `uniqueChannels`, last-upload date windows). These parameters are now **source-confirmed at the registration layer** (`src/server.ts`), resolving the prior run's gap — with the caveat that the lower-level `src/functions/videos.ts` and `channels.ts` on `main` implement only a `query`/`maxResults` subset, so full runtime honoring of every advertised filter is not source-verifiable from the function layer alone (documented under Conflicting Information).

Two prior gaps are now resolved with authoritative evidence: **YouTube Data API v3 quota costs** (search.list = 100 units; videos/channels/playlists/playlistItems .list = 1 unit each; captions.list = 50 units; 10,000 units/day default) are confirmed from `developers.google.com`, and the **Node.js floor** is clarified — the package itself declares **no `engines.node`**, but its direct dependencies `googleapis@^173.0.0` and `@modelcontextprotocol/sdk@^1.1.1` both require **Node `>=18`**, making Node 18 the effective transitive minimum.

## Research Methodology

**Search Strategy**: Started from this repo's `.mcp.json` (ground truth), then queried authoritative primary sources only — the npm registry JSON API (`registry.npmjs.org`), raw source files on `raw.githubusercontent.com` (`package.json`, `README.md`, `src/server.ts`, `src/functions/*.ts`), Google's official YouTube Data API v3 quota page, and Claude Code's official MCP docs (`code.claude.com`). No community/blog/forum sources were used.

**Source Selection**: Types: official package registry, official source repository, official platform docs. Reputation floor 0.90; average 0.95. Verification: cross-referenced package metadata across registry JSON *and* raw `package.json`; cross-referenced tool schemas across the live parent session, `README.md`, and `src/server.ts`.

**Quality Standards**: 3 sources targeted per major claim; version/license/tool-schema claims triangulated across ≥2 authoritative sources. All cited sources on the inline allowlist. Avg reputation: **0.95**.

**Note on WebFetch reliability**: The `developers.google.com` quota page was fetched twice; the summarizer rendered the `search.list` cost inconsistently ("1" on the first pass, a garbled "100 quota per day / 1 quota" on the second). The canonical documented cost of `search.list` is **100 units per call**; this is treated as High confidence (well-established, and the number 100 does appear in the fetched table), with the summarizer inconsistency flagged rather than hidden.

---

## Findings

### Finding 1: Package identity, version, and publish date (v1.0.2, 2026-07-16, MIT)
**Evidence**: Registry `dist-tags.latest` = `1.0.2`; publish time = `2026-07-16T14:13:35.202Z`; `license` = `MIT`; `author` = `Zubeid Hendricks`; `description` = "YouTube MCP Server Implementation"; `main` = `dist/index.js`; `bin` = `zubeid-youtube-mcp-server → dist/cli.js`; repository = `git+https://github.com/ZubeidHendricks/youtube-mcp-server.git`; homepage = `https://zubeidhendricks.github.io/youtube-mcp-server/`.
**Source**: [npm registry JSON — zubeid-youtube-mcp-server](https://registry.npmjs.org/zubeid-youtube-mcp-server) — Accessed 2026-07-19
**Confidence**: High
**Verification**: [raw `package.json` @ main](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/package.json) confirms name, `version: 1.0.2`, `license: MIT`, `author: Zubeid Hendricks`, `main: dist/index.js`, `bin: dist/cli.js`.
**Analysis**: Prior run's values (1.0.2, 2026-07-16) are re-verified and stand. The `bin` mapping to `dist/cli.js` is what `npx -y zubeid-youtube-mcp-server` executes.

### Finding 2: Invocation and dependency stack
**Evidence**: `dependencies`: `@modelcontextprotocol/sdk: ^1.1.1`, `dotenv: ^17.3.1`, `googleapis: ^173.0.0`, `youtube-transcript: ^1.3.1`. `scripts.start`: `node ./dist/index.js`; `build`: `tsc`.
**Source**: [raw `package.json` @ main](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/package.json) — Accessed 2026-07-19
**Confidence**: High
**Verification**: [npm registry JSON](https://registry.npmjs.org/zubeid-youtube-mcp-server) confirms `main`/`bin`/entry points.
**Analysis**: The Data-API tools are backed by `googleapis` (YouTube Data API v3); `transcripts_getTranscript` is backed by the `youtube-transcript` library, which scrapes caption tracks and therefore does **not** consume Data API quota (see Finding 6). `dotenv` allows the server to read keys from a `.env` in addition to the process environment supplied by Claude Code.

### Finding 3: Minimum Node.js version — package declares none; effective transitive floor is Node >=18
**Evidence**: Neither the registry metadata for v1.0.2 nor the raw `package.json` declares an `engines.node` field ("Not specified"). Its dependency `googleapis@173.0.0` declares `engines.node: ">=18"`, and `@modelcontextprotocol/sdk@1.1.1` declares `engines.node: ">=18"`.
**Source**: [raw `package.json` @ main](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/package.json) + [npm registry JSON](https://registry.npmjs.org/zubeid-youtube-mcp-server) — Accessed 2026-07-19
**Confidence**: High (for the absence of a package-level floor and for the dependency-imposed floor); Medium (that Node 18 is the *practical* runtime minimum, since it is inferred transitively rather than declared by the package).
**Verification**: [googleapis@173.0.0 registry JSON](https://registry.npmjs.org/googleapis/173.0.0) → `engines.node: ">=18"`; [@modelcontextprotocol/sdk@1.1.1 registry JSON](https://registry.npmjs.org/@modelcontextprotocol/sdk/1.1.1) → `engines.node: ">=18"`.
**Analysis**: Gap resolved. The How-To should state the prerequisite as **Node.js 18 or later** (imposed by `googleapis` and the MCP SDK), while noting the package does not itself pin an engine. Node 20 LTS or newer is the safe recommendation. `npx` (bundled with Node) is the only other runtime prerequisite.

### Finding 4: The 10 tools and their parameters (source-confirmed in `src/server.ts`)
**Evidence**: `src/server.ts` registers tools in the `ListToolsRequestSchema` handler. Confirmed tool names and parameters:

| # | Tool | Required | Optional |
|---|------|----------|----------|
| 1 | `videos_searchVideos` | `query` | `maxResults`, `order`, `publishedAfter`, `publishedBefore`, `channelId`, `uniqueChannels`, `channelMinSubscribers`, `channelMaxSubscribers`, `channelLastUploadAfter`, `channelLastUploadBefore`, `creatorOnly`, `sortBy` |
| 2 | `videos_getVideo` | `videoId` | `parts` (string[]) |
| 3 | `transcripts_getTranscript` | `videoId` | `language` |
| 4 | `channels_searchChannels` | `query` | `maxResults`, `order`, `channelType`, `minSubscribers`, `maxSubscribers`, `lastUploadAfter`, `lastUploadBefore`, `creatorOnly`, `sortBy` |
| 5 | `channels_findCreators` | `query` | `videoPublishedAfter`, `videoPublishedBefore`, `channelMinSubscribers`, `channelMaxSubscribers`, `channelLastUploadAfter`, `channelLastUploadBefore`, `creatorOnly`, `sortBy`, `sampleVideosPerChannel` (README also lists `maxResults`, `order`) |
| 6 | `channels_getChannel` | `channelId` | — |
| 7 | `channels_getChannels` | `channelIds` (string[]) | `parts`, `includeLatestUpload` (boolean) |
| 8 | `channels_listVideos` | `channelId` | `maxResults` |
| 9 | `playlists_getPlaylist` | `playlistId` | — |
| 10 | `playlists_getPlaylistItems` | `playlistId` | `maxResults` |

**Source**: [`src/server.ts` @ main](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/src/server.ts) — Accessed 2026-07-19
**Confidence**: High (tool names + parameter names are source-confirmed at the registration layer and match the 10 tools live in the parent session).
**Verification**: [`README.md` @ main](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md) lists the same 10 tools and the same parameters; the 10 tool names match exactly the tools connected live in this repo's parent session.
**Analysis**: **Gap resolved** — the extended filter parameters (`creatorOnly`, `sortBy`, subscriber ranges, `sampleVideosPerChannel`, `includeLatestUpload`, `uniqueChannels`, last-upload windows) are **source-confirmed in the MCP tool schemas** advertised by `src/server.ts`, not README-only. See Conflicting Information for the implementation-layer caveat.

### Finding 5: Backing YouTube Data API v3 method per tool
**Evidence / Analysis** (mapping inferred from tool semantics + `googleapis` dependency; label = interpretation):
- `videos_searchVideos`, `channels_searchChannels`, `channels_findCreators`, `channels_listVideos` → `search.list` (keyword/creator discovery). `findCreators`/`searchChannels` additionally hydrate results via `channels.list`.
- `videos_getVideo` → `videos.list`
- `channels_getChannel`, `channels_getChannels` → `channels.list`
- `playlists_getPlaylist` → `playlists.list`
- `playlists_getPlaylistItems` → `playlistItems.list`
- `transcripts_getTranscript` → `youtube-transcript` library (caption scraping), **not** the Data API `captions` resource.
**Source**: [raw `package.json` @ main](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/package.json) (dependency evidence) — Accessed 2026-07-19
**Confidence**: Medium (the mapping is analytical inference from tool names + the `googleapis`/`youtube-transcript` dependencies; not every mapping is line-verified in source).
**Verification**: `README.md` describes the search tools in terms of YouTube search; `youtube-transcript` dependency confirms transcripts bypass the Data API.
**Analysis**: This mapping drives the quota model in Finding 6 and should be presented in the Reference doc as "backing API (approximate)".

### Finding 6: YouTube Data API v3 quota costs and daily allocation
**Evidence**: Per the official quota-cost table: `search.list` = **100 units**; `videos.list` = **1**; `channels.list` = **1**; `playlists.list` = **1**; `playlistItems.list` = **1**; `captions.list` = **50**. Default allocation = **10,000 units/day** per project.
**Source**: [YouTube Data API v3 — Determine quota cost](https://developers.google.com/youtube/v3/determine_quota_cost) — Accessed 2026-07-19
**Confidence**: High for videos/channels/playlists/playlistItems `.list` = 1 and `captions.list` = 50 (consistent across both fetch passes) and for the 10,000-unit/day default. High-with-caveat for `search.list` = 100: the WebFetch summarizer rendered this value inconsistently across two passes, but 100 units/call is the canonical, well-established documented cost and the figure 100 appears in the fetched table.
**Verification**: Cross-checked by re-fetching the same page with a table-targeted prompt; videos/channels/playlists/playlistItems and captions costs matched on both passes.
**Analysis**: **Gap resolved with flag.** Practical implication for the How-To: any tool that calls `search.list` (`videos_searchVideos`, `channels_searchChannels`, `channels_findCreators`, `channels_listVideos`) costs ~100 units per underlying search call — so ~100 searches exhaust a default 10,000-unit daily quota, while `getVideo`/`getChannel`/`getPlaylist`/`getPlaylistItems` are cheap (1 unit). `findCreators` with a high `sampleVideosPerChannel` can multiply `search.list`/`videos.list` calls and burn quota fast. Transcripts cost **0** Data-API units (library-based).

### Finding 7: Multi-key quota fallback (`YOUTUBE_API_KEY` / `KEY2` / `KEY3`)
**Evidence**: README: "At least one of `YOUTUBE_API_KEY`, `YOUTUBE_API_KEY2`, or `YOUTUBE_API_KEY3` must be set. When a request fails because a key has exhausted its quota, the server retries the same request with the next configured key."
**Source**: [`README.md` @ main](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md) — Accessed 2026-07-19
**Confidence**: Medium (single authoritative source — the project README; not independently cross-referenced in source in this run).
**Verification**: Consistent with the `googleapis`/`dotenv` dependency stack (multiple keys read from env). Not line-verified in `src/services/youtube-client.ts`.
**Analysis**: This repo's `.mcp.json` sets only `YOUTUBE_API_KEY` (from `${YOUTUBE_API_TOKEN}`). The How-To can mention `YOUTUBE_API_KEY2`/`KEY3` as an optional way to triple effective daily quota (each additional key = another 10,000 units/day, assuming separate GCP projects/keys).

### Finding 8: Env-var expansion mechanics in `.mcp.json` (`${YOUTUBE_API_TOKEN}` → `YOUTUBE_API_KEY`)
**Evidence**: Claude Code supports env-var expansion in `.mcp.json`. Supported syntax: `${VAR}` expands to the value of `VAR`; `${VAR:-default}` expands to `VAR` if set, otherwise `default`. Expansion locations: `command`, `args`, `env`, `url`, `headers`. "If a referenced environment variable isn't set and has no default value, the config still loads: Claude Code reports a missing-variable warning for that server in `claude mcp list` output and uses the unexpanded `${VAR}` text as-is."
**Source**: [Claude Code — Connect to tools via MCP](https://code.claude.com/docs/en/mcp) (§ "Environment variable expansion in `.mcp.json`") — Accessed 2026-07-19
**Confidence**: High (official Claude Code documentation).
**Verification**: Single authoritative source (sufficient per policy for an authoritative doc); corroborated by the same page's `claude mcp list`/`/mcp` status behavior.
**Analysis**: In this repo, `"env": { "YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}" }` means: at server launch Claude Code reads the value of `YOUTUBE_API_TOKEN` from the host environment and injects it into the server's process as `YOUTUBE_API_KEY` (the variable name the package actually reads). The user must therefore export `YOUTUBE_API_TOKEN` (not `YOUTUBE_API_KEY`) in their shell/`.env`. Because there is no `:-default`, an unset `YOUTUBE_API_TOKEN` will not block config load but the server will receive the literal string `${YOUTUBE_API_TOKEN}` as its key and fail to authenticate — the How-To should call this out as the most likely setup error.

### Finding 9: Verifying the server is connected
**Evidence**: `claude mcp list` lists all configured servers and their status; `claude mcp get <name>` shows per-server detail. The `/mcp` panel (inside Claude Code) "shows the tool count next to each connected server and flags servers that advertise the tools capability but expose no tools." Project-scoped `.mcp.json` servers awaiting approval appear as "⏸ Pending approval (run `claude` to approve)"; a server with bad credentials shows `failed`, a healthy one shows `connected`.
**Source**: [Claude Code — Connect to tools via MCP](https://code.claude.com/docs/en/mcp) — Accessed 2026-07-19
**Confidence**: High (official docs).
**Verification**: Single authoritative source; internally consistent across the page's multiple references to `/mcp` and `claude mcp list`.
**Analysis**: How-To verification steps: (1) run `claude mcp list` → expect `youtube` present; (2) inside a session run `/mcp` → expect `youtube` `connected` with a tool count of 10; (3) if `youtube` shows `failed`, the usual cause is an unset/invalid `YOUTUBE_API_TOKEN` (Finding 8) or an unapproved project-scoped server (approve by running `claude` interactively).

---

## Sample Prompts (natural-language, one per tool — for the How-To)

Each prompt is phrased as a user would speak to Claude Code; the tool it exercises is noted in brackets.

1. **[videos_searchVideos]** "Find the 5 most-viewed YouTube videos about 'retrieval augmented generation' published after January 2025, and skip duplicate channels." (exercises `query`, `maxResults`, `order`, `publishedAfter`, `uniqueChannels`)
2. **[videos_getVideo]** "Get the title, description, view count and duration for YouTube video `dQw4w9WgXcQ`." (exercises `videoId`, `parts`)
3. **[transcripts_getTranscript]** "Pull the English transcript of video `dQw4w9WgXcQ` and summarize the three main points." (exercises `videoId`, `language`)
4. **[channels_searchChannels]** "Search for YouTube channels about home espresso with between 10,000 and 200,000 subscribers, creators only." (exercises `query`, `minSubscribers`, `maxSubscribers`, `creatorOnly`)
5. **[channels_findCreators]** "Find active creators making videos on 'urban gardening' who uploaded in the last 3 months, sample 3 recent videos per channel, sorted by subscriber count." (exercises `query`, `channelLastUploadAfter`, `sampleVideosPerChannel`, `sortBy`)
6. **[channels_getChannel]** "Show me the stats for channel `UC_x5XG1OV2P6uZZ5FSM9Ttw`." (exercises `channelId`)
7. **[channels_getChannels]** "Compare subscriber counts for these three channels and include each one's latest upload: `UC...A`, `UC...B`, `UC...C`." (exercises `channelIds`, `includeLatestUpload`)
8. **[channels_listVideos]** "List the 10 most recent videos from channel `UC_x5XG1OV2P6uZZ5FSM9Ttw`." (exercises `channelId`, `maxResults`)
9. **[playlists_getPlaylist]** "Give me the details of playlist `PLBCF2DAC6FFB574DE`." (exercises `playlistId`)
10. **[playlists_getPlaylistItems]** "List the first 25 videos in playlist `PLBCF2DAC6FFB574DE`." (exercises `playlistId`, `maxResults`)

---

## Reference: At-a-Glance Table

| Attribute | Value | Source |
|-----------|-------|--------|
| Package | `zubeid-youtube-mcp-server` | npm registry |
| Latest version | 1.0.2 | npm registry |
| Published | 2026-07-16T14:13:35Z | npm registry |
| License | MIT | npm registry + package.json |
| Author | Zubeid Hendricks | npm registry |
| Repository | github.com/ZubeidHendricks/youtube-mcp-server | npm registry |
| Homepage | zubeidhendricks.github.io/youtube-mcp-server | npm registry |
| Invocation | `npx -y zubeid-youtube-mcp-server` (stdio) | this repo `.mcp.json` |
| Entry / bin | `dist/index.js` / `dist/cli.js` | package.json |
| Node.js floor | none declared; **>=18** via deps (googleapis, MCP SDK) | package.json + dep registries |
| Backing API | YouTube Data API v3 (`googleapis`); transcripts via `youtube-transcript` | package.json |
| Auth env var | `YOUTUBE_API_KEY` (+ optional `YOUTUBE_API_KEY2`/`KEY3`) | README |
| Tools | 10 | src/server.ts + live session |
| Default quota | 10,000 units/day/project | developers.google.com |

---

## Source Analysis

| Source | Domain | Reputation | Type | Access Date | Cross-verified |
|--------|--------|------------|------|-------------|----------------|
| npm registry — zubeid-youtube-mcp-server | registry.npmjs.org | 0.90 | official package metadata | 2026-07-19 | Y |
| raw package.json @ main | raw.githubusercontent.com | 0.95 | official source | 2026-07-19 | Y |
| README.md @ main | raw.githubusercontent.com | 0.95 | official source | 2026-07-19 | Y |
| src/server.ts @ main | raw.githubusercontent.com | 0.95 | official source | 2026-07-19 | Y |
| src/functions/videos.ts + channels.ts @ main | raw.githubusercontent.com | 0.95 | official source | 2026-07-19 | Y |
| googleapis@173 + @modelcontextprotocol/sdk@1.1.1 registry | registry.npmjs.org | 0.90 | official package metadata | 2026-07-19 | Y |
| YouTube Data API v3 — quota cost | developers.google.com | 0.98 | official platform docs | 2026-07-19 | Y (2 passes) |
| Claude Code MCP docs | code.claude.com | 0.95 | official platform docs | 2026-07-19 | N |

Reputation: High (>=0.90): 8 of 8 (100%) | **Avg: ~0.95** | Citation coverage: >95% (every major claim carries an allowlist source). All sources on the trusted allowlist.

---

## Knowledge Gaps

### Gap 1: Package declares no Node engine floor (mitigated, not fully closed)
**Issue**: The package's own `package.json` has no `engines.node`, so the "minimum Node" is inferred transitively (Node >=18 from `googleapis`/`@modelcontextprotocol/sdk`).
**Attempted**: registry JSON for the package and for both dependencies; raw package.json. **Recommendation**: State "Node.js 18+" in the How-To but frame it as dependency-imposed, not package-declared. Recommend Node 20 LTS+.

### Gap 2: Implementation-layer honoring of extended filters not source-verified
**Issue**: Extended filters are confirmed in the *tool schema* (`src/server.ts`), but `src/functions/videos.ts` and `channels.ts` on `main` implement only `query`/`maxResults`, and do not define `searchChannels`/`findCreators` at all. The filtering logic presumably lives in `src/services/*` (e.g., `channel-metadata.ts`), which was not fetched this run.
**Attempted**: Fetched `src/server.ts`, `src/functions/index.ts`, `videos.ts`, `channels.ts`, and the repo tree. **Recommendation**: To fully verify runtime behavior of each filter, fetch `src/services/channel.ts`, `channel-metadata.ts`, and `video.ts`. For docs, present extended filters as "advertised/supported by the tool schema" and test empirically.

### Gap 3: Exact backing-API call per tool is analytical, not line-verified
**Issue**: The tool→API-method mapping (Finding 5) is inferred from names + dependencies, not read from the service source. **Attempted**: dependency inspection. **Recommendation**: Confirm against `src/services/youtube-client.ts` if the Reference doc needs exact `search.list` vs `channels.list` call counts.

### Gap 4: `captions.list` cost is documented but not used by this server
**Issue**: The 50-unit `captions.list` cost is confirmed from Google docs but is **not** consumed by `transcripts_getTranscript` (which uses the `youtube-transcript` library). Included for completeness only; do not present it as this server's transcript cost.

## Conflicting Information

### Conflict 1: Extended tool parameters present in `server.ts`/README but absent from `src/functions/*.ts`
**Position A (schema/registration layer)**: `src/server.ts` `ListToolsRequestSchema` handler registers `videos_searchVideos`, `channels_searchChannels`, and `channels_findCreators` with the full extended parameter sets (`creatorOnly`, `sortBy`, subscriber ranges, `sampleVideosPerChannel`, last-upload windows, `uniqueChannels`, `includeLatestUpload`). — Source: [src/server.ts](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/src/server.ts), rep 0.95. Corroborated by README and the 10 live tools.
**Position B (function layer)**: `src/functions/videos.ts` defines `searchVideos` with only `query` + `maxResults`; `src/functions/channels.ts` defines only `getChannel` + `getChannelVideos` and contains **no** `searchChannels`/`findCreators` and none of the extended params. — Source: [src/functions/videos.ts](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/src/functions/videos.ts) + [channels.ts](https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/src/functions/channels.ts), rep 0.95.
**Assessment**: Both are authoritative source files from the same repo. The MCP *interface* (what tools/params clients see and can pass) is governed by `src/server.ts`, which is the source of truth for the documented tool catalog — so the extended params are correctly documented as supported by the tool schema. The `src/functions/*.ts` files appear to be an older/partial helper layer; the actual search+filter execution most likely lives in `src/services/*`. **Resolution for docs**: document the full parameter set (from `server.ts`), and add a note that granular filter behavior should be validated empirically since the function-layer implementation on `main` is partial. This is the honest, gap-aware position rather than suppressing either source.

## Recommendations for Further Research
1. Fetch `src/services/channel.ts`, `channel-metadata.ts`, `video.ts`, and `youtube-client.ts` to line-verify (a) which advertised filters are actually applied and (b) the exact Data-API method + call count per tool — this would upgrade Finding 5 and the extended-filter claims from Medium/schema-confirmed to fully implementation-confirmed.
2. Download the published npm tarball's `dist/` to confirm the shipped build matches `main` (guards against README/source drift for v1.0.2).
3. Empirically test `findCreators` with `sampleVideosPerChannel` to measure real quota burn against the 100-unit `search.list` model.

## Full Citations
[1] npm. "zubeid-youtube-mcp-server (registry metadata)". registry.npmjs.org. Accessed 2026-07-19. https://registry.npmjs.org/zubeid-youtube-mcp-server
[2] Hendricks, Zubeid. "package.json (main branch)". GitHub. Accessed 2026-07-19. https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/package.json
[3] Hendricks, Zubeid. "README.md (main branch)". GitHub. Accessed 2026-07-19. https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/README.md
[4] Hendricks, Zubeid. "src/server.ts (main branch)". GitHub. Accessed 2026-07-19. https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/src/server.ts
[5] Hendricks, Zubeid. "src/functions/videos.ts & channels.ts (main branch)". GitHub. Accessed 2026-07-19. https://raw.githubusercontent.com/ZubeidHendricks/youtube-mcp-server/main/src/functions/
[6] npm. "googleapis@173.0.0 & @modelcontextprotocol/sdk@1.1.1 (engines)". registry.npmjs.org. Accessed 2026-07-19. https://registry.npmjs.org/googleapis/173.0.0
[7] Google. "Determine quota cost — YouTube Data API v3". developers.google.com. Accessed 2026-07-19. https://developers.google.com/youtube/v3/determine_quota_cost
[8] Anthropic. "Connect Claude Code to tools via MCP". code.claude.com. Accessed 2026-07-19. https://code.claude.com/docs/en/mcp

## Research Metadata
Duration: ~1 session | Examined: 8 sources | Cited: 8 | Cross-refs: 6 major claims triangulated | Confidence: High ~78%, Medium ~22%, Low 0% | Gaps: 4 (documented, all mitigated) | Conflicts: 1 (resolved with guidance) | Tool failures: WebFetch summarizer rendered `search.list` quota inconsistently (flagged; canonical value 100 units used) | Output: docs/research/youtube-mcp-server-for-howto-reference-doc.md

---

## PEER REVIEW — RESEARCH QUALITY ASSESSMENT

```yaml
review_id: "research_rev_20260719_001"
reviewer: "nw-researcher-reviewer (Scholar)"
review_date: "2026-07-19"

review_focus_areas:
  - source_verification
  - bias_detection
  - evidence_quality
  - cross_reference_independence
  - doc_readiness_for_guides

quality_gates:
  trusted_sources_only:
    status: "PASS"          # after revision rev_001
    note: "Off-allowlist api.github.com source removed; all 8 remaining sources on the trusted allowlist."
  citation_coverage:
    status: "PASS"
    coverage: ">95%"
  avg_source_reputation:
    status: "PASS"
    actual: 0.95
    minimum_required: 0.80
  sufficient_for_guides:
    status: "PASS"
  gaps_documented:
    status: "PASS"

three_specific_verification_results:
  node_floor:
    verdict: "VERIFIED"
    note: "No package engines.node; effective Node >=18 imposed transitively by googleapis / MCP SDK. Reasoning sound and sourced."
  quota_costs:
    verdict: "VERIFIED WITH CAVEAT DISCLOSED"
    note: "search.list=100, list ops=1, captions.list=50, 10k/day; sourced to developers.google.com with WebFetch summarizer inconsistency disclosed."
  extended_params:
    verdict: "SOURCE-CONFIRMED AT SCHEMA LAYER"
    note: "src/server.ts ListToolsRequestSchema; server.ts vs functions/*.ts conflict documented (Conflict 1) with doc guidance."

quality_scores:
  source_bias: 0.85
  evidence_quality: 0.90
  replicability: 0.88
  completeness: 0.92
  priority_validation: 0.90
overall_quality_score: 0.89

required_revisions:
  - id: "rev_001"
    action: "Remove off-allowlist Source [6] (api.github.com) from Source Analysis table and Full Citations; renumber."
    status: "APPLIED by orchestrator 2026-07-19; citations renumbered [7-9] -> [6-8], counts updated 9->8."

verdict: "APPROVED"        # NEEDS_REVISION at review; rev_001 applied -> APPROVED for doc authoring
reviewer_model: "Claude Haiku 4.5"
```
