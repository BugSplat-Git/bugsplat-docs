---
description: >-
  Add BugSplat's hosted MCP server to Claude Code with one command and let
  it read your crash reports, crash groups and symbols while it fixes bugs.
---

# Connect Claude Code to BugSplat

BugSplat hosts a Model Context Protocol (MCP) server at `https://api.bugsplat.com/mcp`. You sign in with your BugSplat account, and Claude Code can then read every database your login can open, with no Client ID or Client Secret to manage.

## Connect BugSplat

1.  Add the server:

    ```bash
    claude mcp add --transport http bugsplat https://api.bugsplat.com/mcp
    ```

    The command adds the server to the current project. To use BugSplat in every project, add `--scope user`:

    ```bash
    claude mcp add --scope user --transport http bugsplat https://api.bugsplat.com/mcp
    ```
2. Start Claude Code and run `/mcp`.
3. Select **bugsplat**, then choose **Authenticate**.
4. Sign in to BugSplat in the browser window that opens, then return to Claude Code.

Claude Code's [MCP documentation](https://code.claude.com/docs/en/mcp) describes the other options of `claude mcp add`.

## Try it

Ask a question about your crashes, for example *"List my BugSplat databases, then show the five crash groups with the most crashes in the default one."* [MCP Tools](tools.md) lists every tool and more example questions, and [MCP Server](README.md) explains how sign-in and permissions work.
