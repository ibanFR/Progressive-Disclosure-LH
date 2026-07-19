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
| Package | [`zubeid-youtube-mcp-server`](https://www.npmjs.com/package/zubeid-youtube-mcp-server) |
| Source repository | [github.com/ZubeidHendricks/youtube-mcp-server](https://github.com/ZubeidHendricks/youtube-mcp-server) |
| Invocation | `npx -y zubeid-youtube-mcp-server` |
| Config file | `.mcp.json` (project root) |
| Config key | `YOUTUBE_API_KEY` ← `${YOUTUBE_API_TOKEN}` |
| Backing API | [YouTube Data API v3](https://developers.google.com/youtube/v3) |
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

`YOUTUBE_API_KEY` is the variable the server reads. `${YOUTUBE_API_TOKEN}` resolves the
`YOUTUBE_API_TOKEN` environment variable from the launching shell.

## Capabilities

| Area | What it exposes |
|------|-----------------|
| Videos | Search videos; fetch video metadata. |
| Transcripts | Fetch a video's transcript. |
| Channels | Search channels; find creators; fetch channel details; list a channel's videos. |
| Playlists | Fetch a playlist; list a playlist's items. |

## Sample prompts

Copy-paste starting points once the server is connected:

- `Search YouTube for videos about "progressive disclosure for coding agents".`
- `Get the metadata for the YouTube video with ID dQw4w9WgXcQ.`
- `Fetch the transcript of YouTube video <VIDEO_ID>.`
- `Find the YouTube channel for the creator Emily Bache.`
- `List the most recent videos from channel <CHANNEL_ID>.`
- `Show the items in the YouTube playlist <PLAYLIST_ID>.`
- `Summarise the transcript of <VIDEO_ID> in five bullet points.`

Replace the `<...>` placeholders with real IDs. Video, channel, and playlist IDs appear
in a YouTube URL (`watch?v=<VIDEO_ID>`, `/channel/<CHANNEL_ID>`,
`playlist?list=<PLAYLIST_ID>`).
