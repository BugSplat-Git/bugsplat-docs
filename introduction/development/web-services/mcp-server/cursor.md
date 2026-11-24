---
description: >-
  Add BugSplat's hosted MCP server to Cursor in mcp.json and let Cursor's
  agent read your crash reports, crash groups and symbols.
---

# Connect Cursor to BugSplat

BugSplat hosts a Model Context Protocol (MCP) server at `https://api.bugsplat.com/mcp`. You sign in with your BugSplat account, and Cursor can then read every database your login can open, with no Client ID or Client Secret to manage.

## Connect BugSplat

1.  Add the server to `~/.cursor/mcp.json` to use it in every project, or to `.cursor/mcp.json` in a project folder to use it in that project only:

    ```json
    {
      "mcpServers": {
        "bugsplat": {
          "url": "https://api.bugsplat.com/mcp"
        }
      }
    }
    ```

    If the file already lists other servers, add the `bugsplat` entry inside the existing `mcpServers` object.
2. In Cursor, open **Settings > Tools & MCP**.
3. Click **Connect** next to **bugsplat**, then sign in to BugSplat in the browser window that opens.

Cursor's [MCP documentation](https://cursor.com/docs/mcp) describes the other options in `mcp.json`.

## Try it

Ask a question about your crashes, for example *"List my BugSplat databases, then show the five crash groups with the most crashes in the default one."* [MCP Tools](tools.md) lists every tool and more example questions, and [MCP Server](README.md) explains how sign-in and permissions work.
