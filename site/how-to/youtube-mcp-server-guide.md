---
title: Install the YouTube MCP Server
layout: default
parent: How-to Guides
nav_order: 2
---

# Install the YouTube MCP Server
{: .no_toc }

Set up the `youtube` MCP server so Claude Code can search YouTube, read video metadata, and fetch transcripts from this repository.
{: .fs-6 .fw-300 }

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

This guide assumes you:

- have [Node.js](https://nodejs.org/) installed (the server runs via `npx`);
- know how to export an environment variable in your shell;
- can edit a JSON config file;
- have a Google account for the Google Cloud Console.

You do **not** need to install the server manually — `npx` fetches it on first run.

## Step 1 — Get a YouTube Data API key

The server uses the [YouTube Data API v3](https://developers.google.com/youtube/v3), which requires an API key from Google Cloud.

1. Open the [Google Cloud Console](https://console.cloud.google.com/).
2. From the project picker (top bar), create a new project or select an existing one.
3. Go to **APIs & Services → Library**, search for **YouTube Data API v3**, and click **Enable**.
4. Go to **APIs & Services → Credentials**.
5. Click **Create credentials → API key**.
6. Copy the generated key. Optionally click **Edit API key** to restrict it to the YouTube Data API v3 only.

{: .warning }
> **Treat the API key like a password.** Never commit it to git or paste it into `.mcp.json`. This project stores the key in an environment variable (Step 2) so the key never enters a tracked file.

## Step 2 — Set the `YOUTUBE_API_TOKEN` environment variable

Add the key to your shell profile so it persists across sessions.

1. Open your shell profile file (`~/.zshrc` for zsh, `~/.bashrc` for bash, etc.) in a text editor.
2. Add this line:

```bash
export YOUTUBE_API_TOKEN="paste-your-api-key-here"
```

3. Save the file and reload your shell:

```bash
source ~/.zshrc
```

Or open a new terminal window for the change to take effect.

## Step 3 — Confirm the `.mcp.json` entry

This repository already registers the server in `.mcp.json` at the project root. Verify the `youtube` entry exists:

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

The server reads the key from `YOUTUBE_API_KEY`. The `${YOUTUBE_API_TOKEN}` syntax maps your shell environment variable into that slot at startup, keeping the key out of the file.

## Step 4 — Verify the installation

1. Start Claude Code from the project root:

```bash
cd /path/to/this/repo
claude
```

2. Once inside Claude Code, run:

```bash
/mcp
```

You should see `youtube` listed as **connected** with a tool count. If it shows **failed**, check that `YOUTUBE_API_TOKEN` is set in your current shell:

```bash
echo $YOUTUBE_API_TOKEN
```

If the output is empty, reload your shell profile and try again.

{: .note }
> For project-scoped servers like this, Claude Code may ask to approve the server on first use. Follow the trust dialog or run `claude` to confirm.

## Optional — Add fallback API keys for quota resilience

The server supports up to three API keys. If a key exhausts its daily quota, the server automatically retries the request with the next key. This is useful for heavy usage.

Set fallback keys in your shell profile:

```bash
export YOUTUBE_API_TOKEN="your-first-key"
export YOUTUBE_API_TOKEN_2="your-second-key"
export YOUTUBE_API_TOKEN_3="your-third-key"
```

Then add them to `.mcp.json`:

```json
{
  "mcpServers": {
    "youtube": {
      "command": "npx",
      "args": ["-y", "zubeid-youtube-mcp-server"],
      "env": {
        "YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}",
        "YOUTUBE_API_KEY2": "${YOUTUBE_API_TOKEN_2}",
        "YOUTUBE_API_KEY3": "${YOUTUBE_API_TOKEN_3}"
      }
    }
  }
}
```

At least one key must be set; the others are optional.

## What's next

The server is now installed and connected. To see what tools are available and sample prompts to try, see the [YouTube MCP Server reference](../reference/youtube-mcp-server-reference.html).
