---
title: YouTube MCP Server
layout: default
parent: Reference
nav_order: 1
---

# YouTube MCP Server
{: .no_toc }

Lookup reference for the `youtube` MCP server registered in this repository.
{: .fs-6 .fw-300 }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## At a glance

| Field | Value |
|-------|-------|
| Server name | `youtube` |
| Package | [`zubeid-youtube-mcp-server`](https://www.npmjs.com/package/zubeid-youtube-mcp-server) v1.0.2 |
| Published | 2026-07-16 |
| Author | Zubeid Hendricks |
| License | MIT |
| Repository | [github.com/ZubeidHendricks/youtube-mcp-server](https://github.com/ZubeidHendricks/youtube-mcp-server) |
| Invocation | `npx -y zubeid-youtube-mcp-server` |
| Config file | `.mcp.json` (project root) |
| Backing API | [YouTube Data API v3](https://developers.google.com/youtube/v3) |
| Tools | 10 read/search tools (no write) |
| Install guide | [Install the YouTube MCP Server]({% link how-to/youtube-mcp-server-guide.md %}) |

## Configuration

### Environment variable mapping

The `.mcp.json` entry maps your shell environment variables into the server's process:

```json
"youtube": {
  "command": "npx",
  "args": ["-y", "zubeid-youtube-mcp-server"],
  "env": {
    "YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}"
  }
}
```

- The server reads `YOUTUBE_API_KEY` from its process environment.
- Your shell exports `YOUTUBE_API_TOKEN` (export `YOUTUBE_API_TOKEN="your-key"` in `~/.zshrc` or `~/.bashrc`).
- Claude Code expands `${YOUTUBE_API_TOKEN}` and injects its value as `YOUTUBE_API_KEY` at startup.

**Common error**: If you export `YOUTUBE_API_KEY` directly instead of `YOUTUBE_API_TOKEN`, Claude Code will not map it correctly and the server will fail. See the [install guide]({% link how-to/youtube-mcp-server-guide.md %}) for details.

### Fallback keys (optional)

Add multiple keys for quota resilience:

```json
"env": {
  "YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}",
  "YOUTUBE_API_KEY2": "${YOUTUBE_API_TOKEN_2}",
  "YOUTUBE_API_KEY3": "${YOUTUBE_API_TOKEN_3}"
}
```

Set the fallback keys in your shell (`export YOUTUBE_API_TOKEN_2="key2"`, etc.). At least one key is required. When a key's quota is exhausted, the server retries with the next available key.

## Tools (10 total)

The server exposes 10 tools across four groups: videos, transcripts, channels, and playlists.

### Videos (2 tools)

#### `videos_searchVideos`

Search YouTube videos with optional filters.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `query` | string | Yes | Search terms. |
| `maxResults` | integer | No | Number of results (default behavior set by server). |
| `order` | string | No | Sort order (relevance, date, viewCount, etc.). |
| `publishedAfter` | string | No | ISO 8601 date. Include videos published after this date. |
| `publishedBefore` | string | No | ISO 8601 date. Include videos published before this date. |
| `channelId` | string | No | Restrict results to a specific channel. |
| `uniqueChannels` | boolean | No | Omit duplicate results from the same channel. |
| `channelMinSubscribers` | integer | No | Minimum channel subscriber count. |
| `channelMaxSubscribers` | integer | No | Maximum channel subscriber count. |
| `channelLastUploadAfter` | string | No | ISO 8601 date. Include channels with recent uploads after this date. |
| `channelLastUploadBefore` | string | No | ISO 8601 date. Include channels with recent uploads before this date. |
| `creatorOnly` | boolean | No | Restrict to verified creator channels. |
| `sortBy` | string | No | Sort by relevance, date, title, etc. |

{: .note }
> Extended filter parameters are advertised by the tool schema. Before using them, validate empirically in your use case, as implementation honoring of all parameters is not fully verified in the source.

#### `videos_getVideo`

Retrieve detailed metadata for a single video.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `videoId` | string | Yes | YouTube video ID (11 characters, found in `youtube.com/watch?v=<ID>`). |
| `parts` | string[] | No | Response parts to include (e.g., `snippet`, `statistics`, `contentDetails`). Defaults to core metadata. |

### Transcripts (1 tool)

#### `transcripts_getTranscript`

Extract a video's transcript (closed captions or auto-generated).

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `videoId` | string | Yes | YouTube video ID. |
| `language` | string | No | ISO 639-1 language code (e.g., `en`, `es`, `fr`). Defaults to the first available caption track. |

{: .note }
> Transcripts are fetched via the `youtube-transcript` library, which scrapes caption data. This **does not** consume YouTube Data API quota (see quota section below).

### Channels (5 tools)

#### `channels_searchChannels`

Search YouTube channels.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `query` | string | Yes | Search terms. |
| `maxResults` | integer | No | Number of results. |
| `order` | string | No | Sort order. |
| `channelType` | string | No | Filter by type (e.g., `show`). |
| `minSubscribers` | integer | No | Minimum subscriber count. |
| `maxSubscribers` | integer | No | Maximum subscriber count. |
| `lastUploadAfter` | string | No | ISO 8601 date. Channels uploaded after this date. |
| `lastUploadBefore` | string | No | ISO 8601 date. Channels uploaded before this date. |
| `creatorOnly` | boolean | No | Verified creators only. |
| `sortBy` | string | No | Sort by relevance, date, title, etc. |

#### `channels_findCreators`

Discover active creator channels matching a video search query.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `query` | string | Yes | Video search terms. Channels found by their recent video matches. |
| `maxResults` | integer | No | Maximum number of channels to return. |
| `order` | string | No | Sort order. |
| `videoPublishedAfter` | string | No | ISO 8601 date. Sample videos published after this date. |
| `videoPublishedBefore` | string | No | ISO 8601 date. Sample videos published before this date. |
| `channelMinSubscribers` | integer | No | Minimum channel subscriber count. |
| `channelMaxSubscribers` | integer | No | Maximum channel subscriber count. |
| `channelLastUploadAfter` | string | No | ISO 8601 date. Channels with recent uploads after this date. |
| `channelLastUploadBefore` | string | No | ISO 8601 date. Channels with recent uploads before this date. |
| `creatorOnly` | boolean | No | Verified creators only. |
| `sortBy` | string | No | Sort by relevance, date, title, subscriber count, etc. |
| `sampleVideosPerChannel` | integer | No | Number of recent videos to sample per channel for filtering. |

#### `channels_getChannel`

Retrieve metadata for a single channel.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `channelId` | string | Yes | YouTube channel ID (found in `youtube.com/channel/<ID>` or via search). |

#### `channels_getChannels`

Retrieve metadata for multiple channels in a single request.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `channelIds` | string[] | Yes | List of YouTube channel IDs. |
| `parts` | string[] | No | Response parts (e.g., `snippet`, `statistics`, `brandingSettings`). |
| `includeLatestUpload` | boolean | No | Include the channel's most recent video in the response. |

#### `channels_listVideos`

List videos published by a channel.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `channelId` | string | Yes | YouTube channel ID. |
| `maxResults` | integer | No | Number of videos to return. |

### Playlists (2 tools)

#### `playlists_getPlaylist`

Retrieve metadata for a single playlist.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `playlistId` | string | Yes | YouTube playlist ID (found in `youtube.com/playlist?list=<ID>`). |

#### `playlists_getPlaylistItems`

List videos within a playlist.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `playlistId` | string | Yes | YouTube playlist ID. |
| `maxResults` | integer | No | Number of items to return. |

## Quota and cost model

### Daily allocation

Each Google Cloud project receives **10,000 quota units per day** by default. The quota resets daily at midnight Pacific Time.

### Cost per operation

| Operation | Cost (units) | Tools affected |
|-----------|------|---|
| `search.list` | 100 | `videos_searchVideos`, `channels_searchChannels`, `channels_findCreators` |
| `videos.list` | 1 | `videos_getVideo` |
| `channels.list` | 1 | `channels_getChannel`, `channels_getChannels`, hydration in search results |
| `playlists.list` | 1 | `playlists_getPlaylist` |
| `playlistItems.list` | 1 | `playlists_getPlaylistItems` |
| Transcripts via `youtube-transcript` | 0 | `transcripts_getTranscript` (library-based, no Data API call) |

{: .note }
> The tool → API-method mapping above is approximate: it is inferred from each tool's behaviour and the server's dependencies, not line-verified against the server source. `channels_listVideos`, in particular, may resolve to either a `search.list` (100 units) or a cheaper `playlistItems.list` (1 unit) path depending on the server's implementation. Treat the figures as guidance and measure real quota burn for high-volume use.

### Cost examples

- A video search (`videos_searchVideos` with `maxResults=50`) costs ~100 units, allowing ~100 searches per day.
- Retrieving one video's metadata (`videos_getVideo`) costs 1 unit.
- A transcript fetch costs 0 units.
- Multi-key fallback: Use `YOUTUBE_API_KEY2` and `YOUTUBE_API_KEY3` to triple your effective daily quota (each key = another 10,000 units/day from a separate GCP project).

### Quota exhaustion

When a request fails because a key has reached its daily quota, the server automatically retries the same request with the next configured API key (if available). This failover is transparent to Claude Code.

For detailed current costs per API method, see the [YouTube Data API v3 quota cost documentation](https://developers.google.com/youtube/v3/determine_quota_cost).

## Finding IDs in YouTube URLs

### Video ID

In `youtube.com/watch?v=<VIDEO_ID>` — the `v` query parameter.
Example: `youtube.com/watch?v=dQw4w9WgXcQ` → `dQw4w9WgXcQ` is the video ID.

### Channel ID

In `youtube.com/channel/<CHANNEL_ID>` or in the channel's custom URL slug.
Example: `youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw` → `UC_x5XG1OV2P6uZZ5FSM9Ttw` is the channel ID.

### Playlist ID

In `youtube.com/playlist?list=<PLAYLIST_ID>` — the `list` query parameter.
Example: `youtube.com/playlist?list=PLBCF2DAC6FFB574DE` → `PLBCF2DAC6FFB574DE` is the playlist ID.

## Sample prompts

Copy-paste these prompts into Claude Code and adapt search terms or IDs for your use case. Each prompt shows a natural-language query that exercises one or more tools.

**Search for videos on a topic:**
"Find the 5 most-viewed YouTube videos about 'retrieval augmented generation' published after January 2025, and skip duplicate channels."

**Get a video's metadata:**
"Get the title, description, view count and duration for YouTube video `dQw4w9WgXcQ`."

**Extract a transcript:**
"Pull the English transcript of video `dQw4w9WgXcQ` and summarize the three main points."

**Search for channels:**
"Search for YouTube channels about home espresso with between 10,000 and 200,000 subscribers, creators only."

**Find active creators:**
"Find active creators making videos on 'urban gardening' who uploaded in the last 3 months, sample 3 recent videos per channel, sorted by subscriber count."

**View channel stats:**
"Show me the stats for channel `UC_x5XG1OV2P6uZZ5FSM9Ttw`."

**Compare channels:**
"Compare subscriber counts for these three channels and include each one's latest upload: `UC...A`, `UC...B`, `UC...C`."

**List recent channel videos:**
"List the 10 most recent videos from channel `UC_x5XG1OV2P6uZZ5FSM9Ttw`."

**Playlist details:**
"Give me the details of playlist `PLBCF2DAC6FFB574DE`."

**Playlist items:**
"List the first 25 videos in playlist `PLBCF2DAC6FFB574DE`."

## Notes on extended parameters

Several tools (`videos_searchVideos`, `channels_searchChannels`, `channels_findCreators`) advertise extended filter parameters such as `creatorOnly`, `sortBy`, subscriber ranges, and date windows. These parameters are drawn from the server's tool schema and README. Before deploying a query that relies on specific filters, validate the behavior empirically in your use case, as comprehensive implementation verification of all parameters was not performed at the function layer. Consult the [YouTube Data API v3 search.list documentation](https://developers.google.com/youtube/v3/docs/search/list) for authoritative API parameter details.
