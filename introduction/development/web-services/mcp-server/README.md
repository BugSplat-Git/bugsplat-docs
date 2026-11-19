---
description: >-
  Connect Claude Code, Claude, or ChatGPT to BugSplat's hosted MCP server and
  ask questions about your crashes. You sign in with your BugSplat account, so
  there is no Client ID or Client Secret to manage.
---

# MCP Server

Ask your AI assistant which crash groups showed up in your latest build, and it answers from your BugSplat databases. BugSplat hosts a Model Context Protocol (MCP) server at this address:

```
https://api.bugsplat.com/mcp
```

Add the address to Claude Code, Claude, ChatGPT, or any other MCP client that connects to remote servers and signs in with OAuth.

The first time your assistant connects, your browser opens BugSplat's sign-in page. After you sign in, the server works with your own BugSplat permissions: the assistant can read every database your login can open. You don't create a Client ID and Client Secret for each database the way you do for the [REST API](../oauth2.md), where each pair belongs to one database.

The server's tools list and read crash reports, crash groups, versions, and symbol files, and three tools file a crash group as a defect in your connected issue tracker or manage that link. [MCP Tools](tools.md) lists each tool's arguments, the filter syntax, and example questions.

{% hint style="info" %}
API and MCP access is included on every plan. On the Free plan, access is rate limited. See [Plans and Billing Overview](../../../../administration/billing/plans-and-billing-guide.md).
{% endhint %}

## Claude Code

1. Add the server:

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

## Claude and Claude Desktop

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

## ChatGPT

ChatGPT connects to custom MCP servers from ChatGPT on the web, and your account and workspace policies decide whether you can add one. These steps follow OpenAI's [Add custom MCP server](https://developers.openai.com/api/docs/guides/developer-mode) guide.

1. Open [Plugins](https://chatgpt.com/plugins) in ChatGPT.
2. Select the plus button, then **Add custom MCP server**.
3. Enter `BugSplat` as the name. Under **Connection**, enter `https://api.bugsplat.com/mcp` as the server URL.
4. Choose **OAuth** for authentication. ChatGPT registers itself with BugSplat automatically, so don't enter a Client ID or Client Secret.
5. Review the risk warning and select **I understand and want to continue**.
6. Select **Create as a plugin**.
7. Install the plugin, and sign in to BugSplat in the browser window that opens.

To use BugSplat in a conversation, type `@` in the prompt box and select BugSplat.

## The bugsplat-mcp package

The [bugsplat-mcp](https://github.com/BugSplat-Git/bugsplat-mcp) package is an MCP server that runs on your own computer. Each configuration reads one database with that database's [Client ID and Client Secret](../oauth2.md). Its README covers setup.
