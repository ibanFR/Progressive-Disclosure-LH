---
title: Install the YouTube MCP Server
layout: default
parent: How-to Guides
nav_order: 2
---

# Install the YouTube MCP Server
{: .no_toc }

Configure the `youtube` MCP server so a coding agent (Claude Code) can search YouTube,
read video metadata, and fetch transcripts from within this repository.
{: .fs-6 .fw-300 }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

This guide assumes you already:

- have [Node.js](https://nodejs.org/) installed (the server runs via `npx`);
- know how to set an environment variable in your shell;
- are comfortable editing a JSON config file;
- have a Google account for the Google Cloud Console.

You do **not** need to install the server manually — `npx` fetches it on first run.

## Step 1 — Get a YouTube Data API key

The server talks to the [YouTube Data API v3](https://developers.google.com/youtube/v3),
which requires a Google Cloud API key.

1. Open the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a project (or select an existing one) from the project picker in the top bar.
3. Go to **APIs & Services → Library**, search for **YouTube Data API v3**, and click **Enable**.
4. Go to **APIs & Services → Credentials → Create credentials → API key**.
5. Copy the generated key. Optionally click **Edit API key** to restrict it to the
   YouTube Data API v3.

{: .warning }
> Treat the API key like a password. Do not commit it to git or paste it into
> `.mcp.json`. This project reads it from an environment variable (Step 2) so the key
> never lands in a tracked file.

## Step 2 — Set the `YOUTUBE_API_TOKEN` environment variable

Export the key in your shell. Add this line to your shell profile
(`~/.zshrc`, `~/.bashrc`, …) so it persists across sessions:

```bash
export YOUTUBE_API_TOKEN="paste-your-api-key-here"
```

Reload your shell (`source ~/.zshrc`) or open a new terminal.

## Step 3 — Confirm the `.mcp.json` entry

This repository already registers the server in `.mcp.json` at the project root. Confirm
it contains the `youtube` entry:

```json
{
  "mcpServers": {
    "youtube": {
      "command": "npx",
      "args": ["-y", "zubeid-youtube-mcp-server"],
      "env": {
        "YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}"
      }
    }
  }
}
```

The server expects the key in `YOUTUBE_API_KEY`; the `${YOUTUBE_API_TOKEN}` syntax maps
your environment variable into that slot at launch, so the key stays out of the file.

## Step 4 — Verify the installation

Start Claude Code from the project root and list the MCP servers:

```bash
claude mcp list
```

Or, inside an interactive Claude Code session, run the `/mcp` command. You should see
`youtube` reported as **connected**. If it fails to connect, check that
`YOUTUBE_API_TOKEN` is set in the shell that launched Claude Code
(`echo $YOUTUBE_API_TOKEN`).

## What's next

The server is installed and connected. For the invocation details and prompts you can
try, see the [YouTube MCP Server reference](../reference/youtube-mcp-server.html).
