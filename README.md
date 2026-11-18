# Atzu Storage MCP server

Atzu Storage (formerly RefreshYourMac) is a Mac storage cleaner that rates everything it finds by
what removing it costs: **Safe to Remove**, **Review**, **Move to Backup** or **Protected**. The
download edition has a built-in [Model Context Protocol](https://modelcontextprotocol.io) server, so
an AI assistant such as Claude Code or Codex can ask what is taking space on a Mac and suggest what
to clean.

This repository holds the setup instructions. The server is part of the app: there is nothing to
build or install from here.

- Website and download: https://refreshyourmac.com/claude-code/
- Server name: `refreshyourmac` · transport: stdio · runs: `/Applications/Atzu.app/Contents/MacOS/RefreshYourMac --mcp`

## No tool deletes anything

An assistant can already delete files on its own. What it can't know is what each folder costs to
remove: whether a cache regenerates, whether a build folder holds the only copy of a finished app,
or whether an SDK version is still used by a project. Atzu Storage knows that, so the work is split:

1. **The assistant reads and suggests.** It sees the scan, the safety level of every category and
   the app's note on every item, and picks items, not whole categories.
2. **Atzu Storage checks and marks.** `propose_clean` keeps only items the app's own scan found and
   marks them in its lists, ticked, beside the app's note on each.
3. **You decide.** Nothing is cleaned until you press Clean on that list, after the usual
   confirmation. Anything rated Move to Backup or Protected, such as a Photos library, can't be
   suggested.

## Tools

| Tool | What it returns | Licence |
|---|---|---|
| `storage_report` | Every category the app measures: safety level, size, item count and id. A full scan takes about two minutes. | |
| `scan_category` | One category's items, largest first, with a note on each. | Needed |
| `scheduled_clean_plan` | The automatic clean's schedule, its last run, and what a run would clean and skip now, with why. | |
| `duplicate_files` | Sets of identical files and what removing the extra copies would free. Can take a minute or more. | Needed |
| `memory_users` | What is using memory, by app, and whether each can be quit. | |
| `running_now` | Swap in use, and simulators and leftover log watchers still running. | |
| `maintenance_tasks` | The maintenance tasks the app offers and what each would run. Runs nothing. | |
| `licence_status` | Whether this Mac's copy can clean. Never the licence key. | |
| `propose_clean` | Marks suggested items in the app's lists. Cleans nothing. | Cleaning needs one |
| `proposal_result` | What happened to an earlier suggestion: cleaned (and what was freed), cancelled or still waiting. | |

Every tool except `propose_clean` only reads.

## Requirements

- macOS 15 or later.
- **Atzu Storage 1.5 or later, the download from [refreshyourmac.com](https://refreshyourmac.com/claude-code/).**
  The Mac App Store edition (Atzu: Storage Cleaner) has no MCP server: its sandbox lets it see too
  little of the disk for the answers to be useful.
- **A licence** to list item names and to clean: $24.99 once, or $14.99 a year with the first 7 days
  free, or $1.99 a month. No account needed.

## Set it up

**The easy way:** open Atzu Storage, then Settings › AI, and switch on Claude Code, the Claude app or
Codex. The app adds itself with the right path, and its **Ask AI** button (under Clean All Safe)
runs the assistant in the background and shows the answer in the window.

**By hand, in Claude Code:**

```sh
claude mcp add --scope user refreshyourmac -- /Applications/Atzu.app/Contents/MacOS/RefreshYourMac --mcp
```

A copy that came as RefreshYourMac and updated itself keeps its old file name:

```sh
claude mcp add --scope user refreshyourmac -- /Applications/RefreshYourMac.app/Contents/MacOS/RefreshYourMac --mcp
```

**Other MCP clients** that run local stdio servers can use the same command and argument. Tested
with Claude Code, the Claude app and Codex; other clients haven't been tested.

```json
{
  "mcpServers": {
    "refreshyourmac": {
      "command": "/Applications/Atzu.app/Contents/MacOS/RefreshYourMac",
      "args": ["--mcp"]
    }
  }
}
```

Then ask your assistant something like *"What's taking space on my Mac, and what's safe to remove?"*

Step-by-step instructions written for an AI agent are in [llms-install.md](llms-install.md).

## Privacy

Atzu Storage answers the assistant on your Mac and sends nothing over the network itself. The
assistant sends what it reads to its own service, so the file names and paths it asks about reach
Anthropic (Claude) or OpenAI (Codex) under their terms. Your licence key never does. Details:
https://refreshyourmac.com/privacy/

## Support

https://refreshyourmac.com/support/

Claude and Claude Code are made by Anthropic; Codex and ChatGPT by OpenAI. Atzu Storage is made by
Caddora and is not affiliated with either.

Made in Ireland by [Caddora](https://caddora.ie).
