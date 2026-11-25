---
description: >-
  Add BugSplat's hosted MCP server to OpenAI Codex with codex mcp add and let
  it read your crash reports, crash groups and symbols.
---

# Connect Codex to BugSplat

BugSplat hosts a Model Context Protocol (MCP) server at `https://api.bugsplat.com/mcp`. You sign in with your BugSplat account, and Codex can then read every database your login can open, with no Client ID or Client Secret to manage.

## Connect BugSplat

1.  Add the server:

    ```bash
    codex mcp add bugsplat --url https://api.bugsplat.com/mcp
    ```

    Codex saves the server in `~/.codex/config.toml`, so it is available in every project, and opens BugSplat's sign-in page in your browser.
2. Sign in to BugSplat, then return to the terminal. Codex prints `Successfully logged in.`

To sign in again, or to switch to a different BugSplat account, run `codex mcp logout bugsplat` and then `codex mcp login bugsplat`. `codex mcp list` shows the server and its sign-in method.

## Try it

Ask a question about your crashes, for example *"List my BugSplat databases, then show the five crash groups with the most crashes in the default one."* [MCP Tools](tools.md) lists every tool and more example questions, and [MCP Server](README.md) explains how sign-in and permissions work.
