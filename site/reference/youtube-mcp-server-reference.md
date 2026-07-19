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
| npm package published | 2026-07-16 |
| Source repository | [github.com/ZubeidHendricks/youtube-mcp-server](https://github.com/ZubeidHendricks/youtube-mcp-server) |
| Author | Zubeid Hendricks |
| License | MIT |
| Invocation | `npx -y zubeid-youtube-mcp-server` |
| Config file | `.mcp.json` (project root) |
| Config key | `YOUTUBE_API_KEY` ← `${YOUTUBE_API_TOKEN}` |
| Backing API | [YouTube Data API v3](https://developers.google.com/youtube/v3) |
| Type | Read/search only (no write tools) |
| Install guide | [Install the YouTube MCP Server](../how-to/youtube-mcp-server-guide.html) |

## Configuration

Entry as registered in `.mcp.json`:

```json
"youtube": {
  "command": "npx",
  "args": ["-y", "zubeid-youtube-mcp-server"],
  "env": {
    "YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}"
  }
}
```

**Environment variable mapping**: `YOUTUBE_API_KEY` is the variable the server reads from its process environment. The `.mcp.json` entry `"YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}"` signals Claude Code to expand `${YOUTUBE_API_TOKEN}` from your shell environment and pass the result to the server as `YOUTUBE_API_KEY`.

**Fallback keys**: The server supports optional `YOUTUBE_API_KEY2` and `YOUTUBE_API_KEY3` entries in the `.mcp.json` `env` map. When a request fails because a key has exhausted its daily quota, the server automatically retries the same request with the next configured key. Example:

```json
"env": {
  "YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}",
  "YOUTUBE_API_KEY2": "${YOUTUBE_API_TOKEN_2}",
  "YOUTUBE_API_KEY3": "${YOUTUBE_API_TOKEN_3}"
}
```

At least one key must be set. If a key's quota is exhausted, the server fails over to the next key.

## Tools and parameters

The server exposes 10 read/search tools across four groups.

### Videos

#### `videos_searchVideos`

Search YouTube videos with filters.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `query` | string | Yes | Search terms. |
| `maxResults` | integer | No | Number of results to return. |

**Extended filters** (not fully verified): `order`, `publishedAfter`, `publishedBefore`, `channelId`, `uniqueChannels`, `channelMinSubscribers`, `channelMaxSubscribers`, `channelLastUploadAfter`, `channelLastUploadBefore`, `creatorOnly`, `sortBy`. See the [YouTube Data API v3 documentation](https://developers.google.com/youtube/v3/docs/search/list) for authoritative parameter details.

#### `videos_getVideo`

Fetch detailed metadata for a single video.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `videoId` | string | Yes | YouTube video ID. |
| `parts` | string | No | Comma-separated list of response parts (e.g., `snippet,statistics,contentDetails`). |

### Transcripts

#### `transcripts_getTranscript`

Extract a video's transcript (closed captions or auto-generated).

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `videoId` | string | Yes | YouTube video ID. |
| `language` | string | No | ISO 639-1 language code (e.g., `en`, `es`). Defaults to available caption track. |

### Channels

#### `channels_searchChannels`

Search YouTube channels.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `query` | string | Yes | Search terms. |
| `maxResults` | integer | No | Number of results to return. |

**Extended filters** (not fully verified): `order`, `channelType`, `minSubscribers`, `maxSubscribers`, `lastUploadAfter`, `lastUploadBefore`, `creatorOnly`, `sortBy`. See the [YouTube Data API v3 documentation](https://developers.google.com/youtube/v3/docs/search/list) for authoritative parameter details.

#### `channels_findCreators`

Discover active creator channels based on a video query.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `query` | string | Yes | Video search terms. |
| `maxResults` | integer | No | Number of channels to return. |

**Extended filters** (not fully verified): `videoPublishedAfter`, `videoPublishedBefore`, `channelMinSubscribers`, `channelMaxSubscribers`, `channelLastUploadAfter`, `channelLastUploadBefore`, `creatorOnly`, `sortBy`, `sampleVideosPerChannel`. See the [YouTube Data API v3 documentation](https://developers.google.com/youtube/v3/docs/search/list) for authoritative parameter details.

#### `channels_getChannel`

Fetch metadata for a single channel.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `channelId` | string | Yes | YouTube channel ID. |

#### `channels_getChannels`

Fetch metadata for multiple channels at once.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `channelIds` | array | Yes | List of YouTube channel IDs. |
| `includeLatestUpload` | boolean | No | Include the channel's most recent video (not fully verified). |

#### `channels_listVideos`

List videos published by a channel.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `channelId` | string | Yes | YouTube channel ID. |
| `maxResults` | integer | No | Number of videos to return. |

### Playlists

#### `playlists_getPlaylist`

Fetch metadata for a single playlist.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `playlistId` | string | Yes | YouTube playlist ID. |

#### `playlists_getPlaylistItems`

Fetch items (videos) within a playlist.

| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `playlistId` | string | Yes | YouTube playlist ID. |
| `maxResults` | integer | No | Number of items to return. |

## Quota and usage limits

**Default quota**: Projects receive a recurring quota of **10,000 units per day**. The quota resets daily at midnight Pacific Time.

**Quota costs vary by operation**: Read and list operations are cheap. Search operations are more expensive and consume quota faster. Consult the live [YouTube Data API v3 quota cost table](https://developers.google.com/youtube/v3/determine_quota_cost) for exact per-method unit costs before designing high-volume queries.

**Quota exhaustion**: When a request fails due to quota exhaustion, the server retries the same request with the next configured API key (if `YOUTUBE_API_KEY2` or `YOUTUBE_API_KEY3` are set). Using multiple API keys is a mitigation strategy for quota limits.

## Locating IDs in YouTube URLs

Video, channel, and playlist IDs appear in YouTube URLs as follows:

- **Video ID**: In `youtube.com/watch?v=<VIDEO_ID>` — the `v` parameter.
- **Channel ID**: In `youtube.com/channel/<CHANNEL_ID>` or in a channel's URL slug.
- **Playlist ID**: In `youtube.com/playlist?list=<PLAYLIST_ID>` — the `list` parameter.

## Sample prompts

Copy-paste starting points once the server is connected. Replace `<VIDEO_ID>`, `<CHANNEL_ID>`, and `<PLAYLIST_ID>` with real IDs from YouTube URLs.

**Video search**: `Find the 5 most-viewed videos about 'Rust async runtime' published in the last year.`

**Video metadata**: `Get the title, channel, view count, and duration for the YouTube video with ID <VIDEO_ID>.`

**Transcript**: `Fetch the transcript of video <VIDEO_ID> and summarize the three main points.`

**Channel lookup**: `Find the channel 'Fireship' and tell me its subscriber count and recent upload cadence.`

**Multi-channel lookup**: `Compare subscriber counts for these channel IDs and show their latest uploads: <CHANNEL_ID>, <CHANNEL_ID>, <CHANNEL_ID>.`

**Creator discovery**: `Find active creators who make videos about 'homelab kubernetes' with at least 10k subscribers.`

**List channel videos**: `List the 10 most recent videos from channel <CHANNEL_ID>.`

**Playlist**: `Show me all the videos in playlist <PLAYLIST_ID> with their titles and positions.`

## Notes on extended parameters

Several tools accept additional filter parameters beyond the core parameters listed above (e.g., `order`, `creatorOnly`, `sortBy`, subscriber ranges). These extended parameters are drawn from the server's README and were not fully verified against the published npm package source. Before using them, consult the [YouTube Data API v3 search.list documentation](https://developers.google.com/youtube/v3/docs/search/list) to confirm availability and syntax for your use case.
