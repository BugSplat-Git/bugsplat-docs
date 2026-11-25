---
description: >-
  Add BugSplat to ChatGPT as a custom MCP server and ask ChatGPT about your
  crashes. You sign in with your BugSplat account.
---

# Connect ChatGPT to BugSplat

BugSplat hosts a Model Context Protocol (MCP) server at `https://api.bugsplat.com/mcp`. You sign in with your BugSplat account, and ChatGPT can then read every database your login can open, with no Client ID or Client Secret to manage.

## Connect BugSplat

ChatGPT connects to custom MCP servers from ChatGPT on the web, and your account and workspace policies decide whether you can add one. These steps follow OpenAI's [Add custom MCP server](https://developers.openai.com/api/docs/guides/developer-mode) guide.

1. Open [Plugins](https://chatgpt.com/plugins) in ChatGPT.
2. Select the plus button, then **Add custom MCP server**.
3. Enter `BugSplat` as the name. Under **Connection**, enter `https://api.bugsplat.com/mcp` as the server URL.
4. Choose **OAuth** for authentication. ChatGPT registers itself with BugSplat automatically, so don't enter a Client ID or Client Secret.
5. Review the risk warning and select **I understand and want to continue**.
6. Select **Create as a plugin**.
7. Install the plugin, and sign in to BugSplat in the browser window that opens.

To use BugSplat in a conversation, type `@` in the prompt box and select BugSplat.

## Try it

Ask a question about your crashes, for example *"List my BugSplat databases, then show the five crash groups with the most crashes in the default one."* [MCP Tools](tools.md) lists every tool and more example questions, and [MCP Server](README.md) explains how sign-in and permissions work.
