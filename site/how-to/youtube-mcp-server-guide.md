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

- have [Node.js](https://nodejs.org/) **18 or later** installed (Node 20 LTS recommended). The server uses `npx` (bundled with Node);
- know how to export an environment variable in your shell (`~/.zshrc`, `~/.bashrc`, etc.);
- can edit a JSON config file;
- have a Google account for [Google Cloud Console](https://console.cloud.google.com/).

You do **not** need to install the server manually — `npx` fetches the package on first run.

## Step 1 — Create a YouTube Data API key

The server uses the [YouTube Data API v3](https://developers.google.com/youtube/v3), which requires an API key from Google Cloud.

1. Open [Google Cloud Console](https://console.cloud.google.com/).
2. From the project picker (top bar), create a new project or select an existing one.
3. Go to **APIs & Services → Library**, search for **YouTube Data API v3**, and click **Enable**.
4. Go to **APIs & Services → Credentials**.
5. Click **Create credentials → API key**.
6. Copy the generated key. Optionally click **Edit API key** to restrict it to YouTube Data API v3 only.

{: .warning }
> **Treat the API key like a password.** Never commit it to git or paste it into `.mcp.json`. This project stores the key in an environment variable so the key never enters a tracked file.

## Step 2 — Export the API key as `YOUTUBE_API_TOKEN`

Add the key to your shell profile so it persists across sessions. **Note the variable name carefully:**

1. Open your shell profile file (`~/.zshrc` for zsh, `~/.bashrc` for bash, etc.) in a text editor.
2. Add this line:

```bash
export YOUTUBE_API_TOKEN="paste-your-api-key-here"
```

{: .warning }
> **Variable name gotcha**: The shell variable **must** be named `YOUTUBE_API_TOKEN` (not `YOUTUBE_API_KEY`). The `.mcp.json` file uses `"YOUTUBE_API_KEY": "${YOUTUBE_API_TOKEN}"` to map the shell variable into the server's environment. If you export `YOUTUBE_API_KEY` directly instead of `YOUTUBE_API_TOKEN`, the server will not receive a valid key and will fail to authenticate.

3. Save the file and reload your shell:

```bash
source ~/.zshrc
```

Or open a new terminal window for the change to take effect.

4. Verify the variable is set:

```bash
echo $YOUTUBE_API_TOKEN
```

You should see your API key printed (or nothing if you have not reloaded the shell yet).

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

At startup, Claude Code expands `${YOUTUBE_API_TOKEN}` from your shell environment and injects it into the server's process as `YOUTUBE_API_KEY`. This keeps your key out of the `.mcp.json` file.

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

You should see `youtube` listed as **connected** with **10 tools**. If it shows **failed** or **warning**, check that `YOUTUBE_API_TOKEN` is set in your current shell:

```bash
echo $YOUTUBE_API_TOKEN
```

If the output is empty, reload your shell profile and try again.

{: .note }
> For project-scoped servers like this, Claude Code may ask to approve the server on first use. Follow the trust dialog or run `claude` interactively to confirm.

You can also run from the command line:

```bash
claude mcp list
```

This shows all configured servers and their status. If `youtube` shows a missing-variable warning, make sure `YOUTUBE_API_TOKEN` is exported in your shell.

## Optional — Add fallback API keys for quota resilience

The server supports up to three API keys. If one key exhausts its daily quota, the server automatically retries the request with the next key. This is useful for heavy usage.

Set fallback keys in your shell profile:

```bash
export YOUTUBE_API_TOKEN="your-first-key"
export YOUTUBE_API_TOKEN_2="your-second-key"
export YOUTUBE_API_TOKEN_3="your-third-key"
```

Then update `.mcp.json`:

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

{: .warning }
> **Only add the `YOUTUBE_API_KEY2`/`YOUTUBE_API_KEY3` lines if you actually export the matching `YOUTUBE_API_TOKEN_2`/`YOUTUBE_API_TOKEN_3` tokens.** When a referenced variable is unset, Claude Code injects the literal text `${YOUTUBE_API_TOKEN_2}` (not an empty value), so the server receives an invalid key and its quota-exhaustion failover will fail when it retries with that slot.

See the [YouTube MCP Server reference]({% link reference/youtube-mcp-server-reference.md %}) for quota limits and cost details.

## Using the server

Once installed, you can query YouTube via natural language. Copy-paste these starting prompts and replace IDs or search terms as needed. See the [YouTube MCP Server reference]({% link reference/youtube-mcp-server-reference.md %}) for the full tool catalog and extended parameters.

**Find videos about a topic:**
"Find the 5 most-viewed YouTube videos about 'retrieval augmented generation' published after January 2025, and skip duplicate channels."

**Get video metadata:**
"Get the title, description, view count and duration for YouTube video `dQw4w9WgXcQ`."

**Extract a transcript:**
"Pull the English transcript of video `dQw4w9WgXcQ` and summarize the three main points."

**Search channels:**
"Search for YouTube channels about home espresso with between 10,000 and 200,000 subscribers, creators only."

**Find active creators:**
"Find active creators making videos on 'urban gardening' who uploaded in the last 3 months, sample 3 recent videos per channel, sorted by subscriber count."

**View channel stats:**
"Show me the stats for channel `UC_x5XG1OV2P6uZZ5FSM9Ttw`."

**Compare channels:**
"Compare subscriber counts for these three channels and include each one's latest upload: `UC...A`, `UC...B`, `UC...C`."

**List channel videos:**
"List the 10 most recent videos from channel `UC_x5XG1OV2P6uZZ5FSM9Ttw`."

**Explore a playlist:**
"Give me the details of playlist `PLBCF2DAC6FFB574DE`."

**List playlist items:**
"List the first 25 videos in playlist `PLBCF2DAC6FFB574DE`."

## What's next

To understand quota costs, the full parameter list for each tool, and how to locate IDs in YouTube URLs, see the [YouTube MCP Server reference]({% link reference/youtube-mcp-server-reference.md %}).
