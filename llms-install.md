# Installing the Atzu Storage MCP server (for AI agents)

These steps are for an AI agent setting up the `refreshyourmac` MCP server for its user. The server
is built into the Atzu Storage app for macOS; nothing is downloaded from this repository.

## 1. Check the Mac and the app

- The Mac must run macOS 15 or later: `sw_vers -productVersion`.
- Find the app's executable. Check these paths in order and use the first that exists:
  1. `/Applications/Atzu.app/Contents/MacOS/RefreshYourMac`
  2. `/Applications/RefreshYourMac.app/Contents/MacOS/RefreshYourMac` (a copy that updated itself from before the rename)
- If neither exists, stop and tell the user to download Atzu Storage from
  https://refreshyourmac.com/claude-code/ and open it once. Do not download or install it yourself.
- The Mac App Store edition (Atzu: Storage Cleaner, sandboxed) has no MCP server. If the user only
  has that one, it will answer `--mcp is available in the download edition only.`

## 2. Add the server

Claude Code:

```sh
claude mcp add --scope user refreshyourmac -- <executable path from step 1> --mcp
```

Other clients that run local stdio servers: add a server named `refreshyourmac` with the executable
path from step 1 as the command and `--mcp` as its only argument. No environment variables or API
keys are needed.

## 3. Check it works

Call `storage_report`. It returns every category with its safety level and size, and can take about
two minutes on a full scan. Then call `licence_status`: listing item names (`scan_category`,
`duplicate_files`) and cleaning need a licence, which the user buys in the app or at
refreshyourmac.com. Never ask the user for their licence key; the server never needs it.

## Rules for using it

- No tool deletes anything. Do not delete files the server lists yourself, by shell or otherwise.
- To free space, read the items with `scan_category`, then suggest specific items with
  `propose_clean`. Atzu Storage marks them in its own lists; the user presses Clean to remove them.
  Check the outcome later with `proposal_result`.
- Read each item's note first. A note saying an item is in use, or holds a finished build, means
  leave it out.
- Treat anything rated Move to Backup or Protected as the user's data, not as space to free.
