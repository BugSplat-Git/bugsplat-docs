---
description: >-
  Add BugSplat to Claude and Claude Desktop as a custom connector and ask
  Claude about your crashes. You sign in with your BugSplat account.
---

# Connect Claude to BugSplat

BugSplat hosts a Model Context Protocol (MCP) server at `https://api.bugsplat.com/mcp`. You sign in with your BugSplat account, and Claude can then read every database your login can open, with no Client ID or Client Secret to manage.

## Connect BugSplat

Claude calls a remote MCP server a custom connector. Custom connectors are available on every Claude plan, and the Free plan allows one. These steps follow Anthropic's [Get started with custom connectors using remote MCP](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp), which has the latest details.

On a Pro or Max plan:

1. Go to **Customize > Connectors**.
2. Click **+ Add**, then **Add custom connector**.
3. Enter `BugSplat` as the name and `https://api.bugsplat.com/mcp` as the server URL, then click **Continue**.
4. Review the authentication settings Claude detected, then click **Continue**. Claude registers itself with BugSplat automatically, so you don't need a Client ID or Client Secret. If Claude asks you to choose an **OAuth client**, choose **Register automatically**.
5. Click **Add**, then sign in to BugSplat in the browser window that opens.

On a Team or Enterprise plan, an Owner or Primary Owner adds the connector once for the organization:

1. Go to **Organization settings > Connectors** and click **Add**.
2. Hover over **Custom** and select **Web**.
3. Enter the name and server URL as above, review the authentication settings, and click **Add**.

Each member then goes to **Customize > Connectors**, finds BugSplat, clicks **Connect**, and signs in with their own BugSplat account. The assistant sees only the databases that member's login can open.

To use BugSplat in a conversation, click **+** at the lower left of the chat, choose **Connectors**, and turn on BugSplat.

## Try it

Ask a question about your crashes, for example *"List my BugSplat databases, then show the five crash groups with the most crashes in the default one."* [MCP Tools](tools.md) lists every tool and more example questions, and [MCP Server](README.md) explains how sign-in and permissions work.
